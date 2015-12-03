# Service Check

You can go to Service Check from left navigation and do some basic service availability for selected domain. We highly recommend users to do service check before starting CNAME.

![][1]

Select domain to check health status and provide URL that can be accessed, and click 'Check'.

1. The original domain is OK (HTTP status code is 200) - means the domain works
2. CDN service is deployed - means the CDN service is deployed
3. The CDN cache is OK - means the contents accessed from both origin and CDN endpoints are the same (by HTTP host header：HTTP Status Code，Last Modified Time，Content Length).

>**Note**
>Service check doesn't ensure all the CDN endpoints are working fine.

 [1]: ./images/service-check.png

