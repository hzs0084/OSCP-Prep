AWS: S3 Buckets
Azure: Blobs
GCP: Cloud Storage

These can be accessed without authentication if configured incorrectly. 

#### Company Hosted Server

Often cloud storage is added to the DNS list. 

There are also different ways to find such cloud storage. Google Dorks, we can search for these buckets using inurl: and intext

Third Party providers such as [domain.glass](https://domain.glass/) can also tell us about the company's infrastructure. 

In an example, we noticed that it did a cloudflare security assessment and it marked it as safe, but that also means now we have found a security measure that can be noted for the second layer (gateway)

[GrayHatWarfare](https://grayhatwarfare.com/) can help us do many different searches, discover AWS, Azure, and GCP cloud storage, and even sort and filter by file format. 

Sometimes these buckets can lead to SSH private keys, that can be used to log onto one or even more machines in the company without using a password. 

