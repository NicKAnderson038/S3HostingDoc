# Static Website Hosting

- Enable s3 static website hosting
- Enable in route53
- Enable ssl cert with cloudFront
- Create alise for ssl cert in route53


<br>

### Enable s3 static website hosting.

1. Create a new Bucket with a name that matchs your domain name example: `MyWebAppExample.com`


2. Click on the `Perssions` tab and click on `Public Access Setting`. Make sure all boxes are unchecked and save.

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/D9O7PE6.png" alt="s3 access settings"></a>

<br>

3. Click on `Bucket Policy` and copy snippet below into s3 Bucket Policy and replace `MyWebAppExample.com` below with your domain name.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::MyWebAppExample.com/*"
        }
    ]
}


```

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/WadfbKI.png" alt="s3 Bucket Policy"></a>

<br>

4. Enter `index.html` for the Index Document and click `save`.

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/fcRpvSK.png" alt="inable static website hosting"></a>

<br>

5. Click on the `Overview` tab and upload an index.html file for testing.
6. ***Example html boiler plate below or download `index.html` file from this repo.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>My WebApp</title>
  </head>
  <body>
    <h1>Static Webapp Hosting Test!</h1>
  </body>
</html>
```

7. Once file is `index.html` file is uploaded, click on the `Properties` then click on `Static website hosting`. Then open the Endpoint link: http://MyWebAppExample.com.s3-website-us-east-1.amazonaws.com to comfirm the web app is being hosted by aws.

<br>

#

### Enable in route53

1. Purchasing Domains through AWS. Choice route53 and click through as follows. If not, skip to next step.

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/IZ2lryY.png" alt="New buying domain via AWS"></a>

<br>

2. Click on `Hosted Zones` > `Create Hosted Zone` > Enter in domain name > click `Create`.

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/om7frjl.png" alt="route53 create a new hosted zone"></a>

<br>

3. If using external domain provider, copy `ns's` and add to your external domain provider account. Domain Provider  examples are namecheap, goDaddy, Google Domain, and etc.

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/PTyYOLv.png" alt="route53 NS"></a>

<br>

<br>

#

### Enable ssl cert with CloudFront

1. In CloudFront click on `Distributions` > `Create Distribution` > under Web click `Get Started`.

2. Change the following in Create Distribution.
    
    - Original Domain Name: enter your domain `MyWebAppExample.com`
    - Viewer Protocol Policy: click on  `Redirect HTTP to HTTPS` radio button.
    - Alternate Domain Names (CNAMES): 
        ```bash
        MyWebAppExample.com
        www.MyWebAppExample.com
        ```
    - *** SSL Certificate: Custom SSL Certificate (example.com) radio 
    and click the `Request of Import a Certificate with ACM` button.
    - Default Root Object: enter `index.html` *** If servering via lambda instead of s3, leave empty!

*** Click on the `Request of Import a Certificate with ACM` button will open new screen. Follow the promps.

- Step 1: Add domain names. Enter in `MyWebAppExample.com` and click `Add another name to this certificate` and enter `www.MyWebAppExample.com`
-Step 2: If the domain was purchased through AWS, click the radio `DNS Validation`, for an domain provider select `Email Validation`
- Follow prompts for Step 3 & 4. Validation can take up to 1 hour.

<br>

#

### Create alise for ssl cert in route53

1. In ClouldFront click on `Distributions` and copy the `Domain Name` and paste into the url to test if its working. Also check the `Status` if it has been `Deployed` 

<a href="http://www.freeimagehosting.net/commercial-photography/"><img src="https://i.imgur.com/bpVcwn3.png" alt="Copy my Domain Name"></a>

<br>

2. Open route53. Click on `Hosted Zones` > Click on `Create Record Set`.

3. If these records sets do not exist, create them as follows.

    *** 1st
    - Name: `MyWebAppExample.com`
    - Type: A - IPv4 address
    - Alias: YES
    - Alias Target: `paste in copied domain name from cloudfront`
    - click on `Save Record Set`

    *** 2nd
    - Name: `www.MyWebAppExample.com`
    - Type: CNAME
    - Alias: NO
    - Value: `paste in copied domain name from cloudfront`
    - click on `Save Record Set`

*** NOTE: Domains purchased via AWS will create another CNAME if the CloudFront was successfully deployed.

4. At this point, all http request should be redirected to secure https domains.# S3HostingDoc
