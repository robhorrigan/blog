+++
title = "Using Webtask.io to send data to AWS S3"
tags = ["nodejs", "aws", "webtask"]
date = "2017-03-10T11:13:56-06:00"
author = "Rob Horrigan"
description = "A simple Webtask to send data to AWS S3"

+++

Recently I needed to find a simple solution for storing a JSON response in an S3 bucket for future inspection. I won't bore you with the details of the specific use-case, but in the process I learned about how [Webtask.io](https://webtask.io/) can make something like this trivially easy to implement.

The Webtask I created accepts JSON and sends it to be stored in an AWS S3 bucket using Express and the AWS SDK. This is a simplified version of the solution I used for my real world implementation, since I needed to do some filtering of the data to determine which requests should be stored, but hopefully this shows how one goes about setting something like this up.

<pre class="prettyprint linenums">
// s3-upload.js

'use latest'

import AWS from 'aws-sdk'
import express from 'express'
import Webtask from 'webtask-tools'
import bodyParser from 'body-parser'
import uuid from 'uuid'

var app = express();

app.use(bodyParser.json());

app.post('/', function (req, res) {
  const { AWS_REGION, AWS_PUBLIC_KEY, AWS_SECRET_KEY, AWS_BUCKET } = req.webtaskContext.data;
  const s3 = new AWS.S3({
    region: AWS_REGION,
    accessKeyId: AWS_PUBLIC_KEY,
    secretAccessKey: AWS_SECRET_KEY
  })
  const maxAge = 60 * 60 * 24 * 365
  const cacheControl = `public, max-age=${maxAge}`
  const bucket = AWS_BUCKET
  const awsConfig = {
    Bucket: bucket,
    ACL: 'public-read',
    CacheControl: cacheControl
  }
  const key = uuid.v4().substring(0, 6)
  const body = JSON.stringify(req.body)

  s3.putObject({
    ...awsConfig,
    Key: key,
    Body: body
  }, (err, data) => {
    if (err) throw err;
    res.send(`http://${bucket}/${key}`);
  })
});

module.exports = Webtask.fromExpress(app);
</pre>

Then, once you have the [Webtask CLI](https://webtask.io/docs/wt-cli) installed, you can run `wt create s3-upload.js --no-parse --secret AWS_REGION= --secret AWS_PUBLIC_KEY= --secret AWS_SECRET_KEY= --secret AWS_BUCKET=`

Enjoy.