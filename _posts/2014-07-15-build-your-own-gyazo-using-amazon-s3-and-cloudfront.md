---
layout: post
title: How to build your own Gyazo using Amazon S3 and CloudFront
---
A few years back, I discovered [Gyazo](https://gyazo.com/), a lightweight tool that allows you to effortlessly share screenshots. Its usage instructions are dead simple: Click and drag to select a region of your screen you want to capture. Release the mouse button and Gyazo uploads the screenshot its servers. The screenshot then magically opens in your browser, and its URL has already been copied to your clipboard.

This was a huge improvement over my previous workflow which involved hitting Print Screen, pasting the image into an image editor, cropping, saving, going to ImageShack.us (how nostalgic), and uploading. A minute-long operation now took about a second. Fantastic!

Until they started displaying ads. I didn't mind at first, but then they had the gall to add pop-ups and interstitial ads. Not cool. So I googled "Gyazo alternatives" and found [SpeedCap](http://speedcap.net/). I used SpeedCap for a while, but it frequently went down for maintenance and failed on large screenshots.

So I decided to make my own.

## Introducing Amazon S3 and CloudFront

I decided to host my screenshots using [Amazon Web Services](http://aws.amazon.com/). Specifically, I use two AWS services: [S3](http://aws.amazon.com/s3/) for storage, and [CloudFront](http://aws.amazon.com/cloudfront/) for content delivery. With AWS, you pay only for what you use, and when it comes to hosting images, that's not much. The total cost of using these two *hardcore, [enterprise-class](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition)* services for screenshot hosting is about ten cents a month. For comparison, Gyazo charges $2.99 a month for premium.

## Creating an S3 bucket

To begin, we'll need to create an S3 bucket for our screenshots. Buckets are where all objects in S3 are stored. Follow the instructions [here](http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) to create a bucket. Here, I've named my bucket `kevinwang-screenshots`.

![Creating a new S3 bucket](/images/2014/07/s3-create-bucket.png)

## Creating a CloudFront distribution

Now we'll need to connect our new S3 bucket to Amazon's CDN, CloudFront.

In the AWS console, go to the CloudFront console and click Create Distribution. Select Web and continue. Click into the Origin Domain Name field and select the S3 bucket you just created (`bucket-name.s3.amazonaws.com`). The Origin ID will autofill.

Since we only want to serve images through CloudFront, select Yes for Restrict Bucket Access. For Origin Access Identity, select "Create a New Identity," and for Grant Read Permissions on Bucket, select "Yes, Update Bucket Policy."

![Creating a new CloudFront distribution](/images/2014/07/cloudfront-create-distribution.png)

If you have a custom domain name you want to use for your image host, add it to "Alternative Domain Names (CNAMES)." Otherwise, you'll have to use an unsexy domain name like `d2j5nv0uysqvdx.cloudfront.net`. If you choose to use a custom domain name, you'll also need to add a CNAME record from your custom domain name to your unsexy CloudFront domain. In this example, I will be using `screenshots.kevinwang.com`.

That's it for setting up your backend. Now let's make a client.

## Setting up your screenshot tool

Note: My script is specific to OS X, but you can easily adapt it for Linux by substituting `scrot` for `screencapture` and `xclip` for `pbcopy`, among other things.

Our screenshot tool will be a simple bash script. First, you will need to install and [set up](http://s3tools.org/s3cmd-howto) S3cmd, a command line client for S3.

``` bash
$ brew install --devel s3cmd
# --devel because `s3cmd info` breaks on subdirectories in 1.0.1.
$ s3cmd --configure
```

And without further ado, the code:

{% gist kevinwang/b1f9c8709c637ffdb601 upshot.sh %}

Some notes on this script:

- *What is* `resample_dpi`*?*

  Retina MacBook Pros take high-res screenshots by virtue of their kickass pixel density, but the resulting images appear [scaled up](http://benalman.com/news/2012/07/fixing-retina-image-scaling/) as a result. [This script](https://github.com/cowboy/dotfiles/blob/master/bin/resample-dpi) by Ben Alman fixes the resolution of screenshots taken on Retina displays. The script is also included in the Gist.

- *What is* `generate_unique_filename` *doing?*

  Two things. First, it combines a number of Unix commands to generate a random 6-digit alphanumeric filename. Second, it checks whether an image already exists in the bucket with that filename by checking the exit code of `s3cmd info`. If there is a collision, it tries again.

- *Why absolute paths for everything?*

  Because Automator requires it. Which brings us to our bonus round!

### Bonus round: Mapping the script to a keyboard shortcut

In OS X, `Cmd-Shift-4` takes a regular screenshot by default. Let's make it run our screenshot tool instead.

Open Automator and create a new service. Configure it as follows, and save it with a meaningful name like "Upload Screenshot."

![Automator service configuration](/images/2014/07/automator-upshot.png)

Then open System Preferences and navigate to Keyboard > Shortcuts. Untick "Save picture of selected area as a file." Now go to Services and scroll to the very bottom where you'll find the service you just made. Assign your service the shortcut `Cmd-Shift-4`, and you're done!
