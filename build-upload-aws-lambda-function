#!/bin/bash -ex
#
# Build AWS Lambda function ZIP file and upload to S3
#
# Usage: ./build-upload-aws-lambda-function S3BUCKET S3KEY [HUGOVERSION]
#
# For example:
# ./build-upload-aws-lambda-function run.alestic.com lambda/aws-lambda-site-generator-hugo-0.17.zip 0.17
#

function ver_compare { 
    printf "%03d%03d%03d%03d" $(echo "$1" | tr '.' ' ') 
}

s3bucket=${1:?Specify target S3 bucket name}
s3key=${2:?Specify target S3 key}
target=s3://$s3bucket/$s3key
hugo_version="${3:-0.36.1}"

tmpdir=$(mktemp -d /tmp/lambda-XXXXXX)
zipfile=$tmpdir/lambda.zip

# Add AWS Lambda function file to ZIP file
zip -r9 $zipfile index.py

download_url="https://github.com/gohugoio/hugo/releases/download/v${hugo_version}/hugo_${hugo_version}_Linux-64bit.tar.gz"

# Hugo zip format changes on 0.21 0.16, and change download URL on 0.17 0.16 and before, so we have to treat them differently:

if [ $(ver_compare $hugo_version) -ge $(ver_compare 0.21) ] ; then
    (
     cd $tmpdir
     wget -qO hugo.tar.gz $download_url
     tar xvzf hugo.tar.gz
     zip -r9 $zipfile hugo
    )
elif [ $(ver_compare $hugo_version) -ge $(ver_compare 0.17) ] ; then
    (
     cd $tmpdir
     wget -qO hugo.tar.gz $download_url
     tar xvzf hugo.tar.gz
     mv hugo_${hugo_version}*/hugo_${version}* ./hugo
     zip -r9 $zipfile hugo
    )
elif [ "$hugo_version" == "0.16" ] ; then
    download_url="https://github.com/gohugoio/hugo/releases/download/v${hugo_version}/hugo_${hugo_version}_linux-64bit.tgz"
    (
     cd $tmpdir
     wget -qO hugo.tar.gz $download_url
     tar xvzf hugo.tar.gz
     zip -r9 $zipfile hugo
    )
elif [ $(ver_compare $hugo_version) -lt $(ver_compare 0.16) ] ; then
    download_url="https://github.com/gohugoio/hugo/releases/download/v${hugo_version}/hugo_${hugo_version}_linux_amd64.tar.gz"
    (
     cd $tmpdir
     wget -qO hugo.tar.gz $download_url
     tar xvzf hugo.tar.gz
     mv hugo_${hugo_version}*/hugo_${version}* ./hugo
     zip -r9 $zipfile hugo
    )
fi

# Upload AWS Lambda function ZIP file to S3
aws s3 cp --acl public-read $zipfile $target

# Clean up
rm -r $tmpdir

