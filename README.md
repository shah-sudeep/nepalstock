# nepalstock

Public version of [nepalstock-api](https://nepalstock.onrender.com/info).<br>
Used Authorization Bypass code from [NepseUnofficialApi](https://github.com/basic-bgnr/NepseUnofficialApi)

## How does this work?
- You visit nepalstock.onrender.com/something/somehting/
- The Server sends request to nepalstock.com/api/nots/something/somehting/
- It bypasses the UNAUTHORIZED ACCESS you would get when directly visiting the API.
- It reads the response and sends the exact response back to you.

## Usage for local computer
- Download this repo
- python main.py
- Visit 127.0.0.1:5000/info

## Deploy
Instead of using nepalstock.onrender.com, you can deploy it yourself on [render](https://render.com/):

<a href="https://render.com/deploy?repo=https://github.com/Prabesh01/nepalstock-api">
  <img src="https://render.com/images/deploy-to-render-button.svg" alt="Deploy to Render">
</a>

# NepseUnofficialApi
Unofficial library to interface with nepalstock.com.np
Deciphers the authentication key to access the api.


# Development
1. [Sep 24, 2023] [Fixed SSL CERTIFICATE_VERIFY_FAILED](#Fixed:-SSL-Error).
1. [Sep 24, 2023] Branch `15_feb_2023` is now merged with the master branch. 
1. [Feb 15, 2023] ~~checkout new branch 15_feb_2023 to adjust for new change in Nepse.~~


# Fix Details 
## Fixed: SSL Error
Recently there was a [PR](https://github.com/basic-bgnr/NepseUnofficialApi/pull/3) in this repo by [@Prabesh01](https://github.com/Prabesh01) to merge few changes to fix SSL issue that he was facing.  

```
requests.exceptions.SSLError: 
HTTPSConnectionPool(host='www.nepalstock.com.np', port=443): 
Max retries exceeded with url: /api/authenticate/prove 
(Caused by SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] 
certificate verify failed: unable to get local issuer certificate (_ssl.c:1002)')))
``` 
The day when I actually received that PR, I too was facing similar issue with Nepse's website, so I thought the issue was serverside and left it as it is. 

Fast-forward today, upon diving a little deeper, It appears that the issue can be solved entirely from clientside. But it has nothing to do with code in this repository, it was because my linux distribution(and maybe others too, I haven't checked) doesn't have ca-certificate of Certificate Authority [GeoTrust](http://cacerts.geotrust.com/) that signs the ssl certificate of Nepse. The mistake is primarily due to Nepse as it means that the certificate chain used by Nepse is incomplete.

> ### Solution:

1. Find out the ssl [certificate details of Nepse](https://www.ssllabs.com/ssltest/analyze.html?d=nepalstock.com.np) using [ssllabs.com](https://www.ssllabs.com).
1. Copy the .pem file from the ssllabs and save it into your `/usr/local/share/ca-certificates/` folder using the following command[^1].  
```
sudo curl -A "Mozilla Chrome Safari" "https://www.ssllabs.com/ssltest/getTestCertificate?d=nepalstock.com.np&cid=3a83c9a7e960f29b48e5719510e2e8582c37f72f3abf35e6f400eaacec38aad2&time=1695547628855" >> geotrust.pem
sudo curl -A "Mozilla Chrome Safari" "https://www.ssllabs.com/ssltest/getTestChain?d=nepalstock.com.np&cid=3a83c9a7e960f29b48e5719510e2e8582c37f72f3abf35e6f400eaacec38aad2&time=1695547628855" >> geotrust_alt.pem 
```
3. and, finally you've to run the following command[^1] to include the added CA details into the system.  
``` sudo update-ca-certificates```
[^1]: The command uses root access so first verify before carrying out the operation.
