 # Website with s3 bucket as image storage
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Description
Here, we are creating a simple html website. However, it's images are loading from s3 bucket.An Amazon S3 bucket is a public cloud storage resource available in Amazon Web Services' (AWS) Simple Storage Service (S3), an object storage offering. Amazone s3 is imilar to file folders, store objects, which consist of data and its descriptive metadata.

## Resources
- Creation of s3 bucket
- Uploading images to s3 bucket
- Creation of bucket policy to make bucket objects public
- Adding rewrite rule in webserver configuration file to  access the images in s3 bucket


## Prerequisites for this project
-- Need an IAM user
-- bucket policy 

## IAM   Configure
Here Iam configuring an IAM user without any policy
```sh 
[root@ip-172-31-6-205 html]# aws configure
AWS Access Key ID [None]: ************
AWS Secret Access Key [None]: ********************
Default region name [None]: ap-south-1
Default output format [None]: json
```
## Bucket policy creation for providing priveillages for IAM user to access the bucket.

Here we are creating a bucket policy under the bucket "image_website" for providing priveillage for IAM user to access the bucket.

The following bucket policy is added under the bucket permmision section.
```sh 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::*****************:user/test"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::***************",
                "arn:aws:s3:::************/*"
            ]
        }
    ]
}
```
![alt text] (https://github.com/sruthymanohar/s3bucket-objects-image-website/blob/main/Capture1.PNG?raw=true)

## Uploading of images to the s3 bucket using sync  command

The following command sync  local image folder with s3 bucket. You can directly drag images to the bucket using upload command in aws console.
```
aws s3 sync img s3://image.website/
upload: img/constructive_bg_01.jpg to s3://image.website/constructive_bg_01.jpg
upload: img/gallery-img-05.jpg to s3://image.website/gallery-img-05.jpg
upload: img/gallery-img-02-tn.jpg to s3://image.website/gallery-img-02-tn.jpg
upload: img/gallery-img-04-tn.jpg to s3://image.website/gallery-img-04-tn.jpg
upload: img/gallery-img-03-tn.jpg to s3://image.website/gallery-img-03-tn.jpg
upload: img/constructive_bg_02.jpg to s3://image.website/constructive_bg_02.jpg
upload: img/constructive_bg_03.jpg to s3://image.website/constructive_bg_03.jpg
upload: img/gallery-img-06-tn.jpg to s3://image.website/gallery-img-06-tn.jpg
```

## Now we are creating a bucket policy to make the objects in s3 bucket as public

Before that we have to edit the status of Block public access (bucket settings)  to off from on status.
![alt text] (https://github.com/sruthymanohar/s3bucket-objects-image-website/blob/main/Capture3.PNG?raw=true)

```sh
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::image.website/*"
            ]
        }
    ]
}
```
![alt text] (https://github.com/sruthymanohar/s3bucket-objects-image-website/blob/main/Capture2.PNG?raw=true)

The above code will  provide all the  object in that bucket to public access.

Finally we have to modify the apache config file to serve the img directory from this bucket.

```sh 
RewriteEngine On
RewriteRule ^/img/(.*)$ https://s3.ap-south-1.amazonaws.com/image.website$1 [L]
```

Restart apache to reflect the changes..

```sh 
systemctl restart httpd.service
```

Now our images are loading from s3 bucket, we can identify the same by  browsing website's image on a browser tab and see the image url will be pointed to your S3 bucket like "https://s3.ap-south-1.amazonaws.com/image.website/gallery-img-02-tn.jpg"

Please check the following screen shots :

![alt text] (https://github.com/sruthymanohar/s3bucket-objects-image-website/blob/main/Capture5.PNG?raw=true)

![alt text] (https://github.com/sruthymanohar/s3bucket-objects-image-website/blob/main/Capture6.PNG?raw=true)


![alt text](https://github.com/sruthymanohar/s3bucket-objects-image-website/blob/main/Capture6.PNG)



