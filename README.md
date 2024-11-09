# Working with Tines Resources

As mention in a previous blog, the first automation project we tackled concerned shunning malicious IP addresses on the Cisco Next-Gen Firewall (NGFW) without any involvement from the Network Management team.  In the Firewall Management Center, we used the custom network security intelligence feed as a means to shun IPs at the border.  The NGFW consumes two text files via URLs, one containing the list of IPs to shun, and the other containing the MD5 hash of the IP shun list.  

We decided to host the files on an S3 bucket, since it contains only Indicators of Compromise (IOCs), therefore no PI data. By using AWS, we didn't have to maintain any internal infrastructure.

One challenge, facing the Security Operations Centre (SOC), is how to lifecycle or sunset IOCs, especially IP addresses since threat actors rapidly cycle through them during phishing campaigns.  It is easy to add IOCs to a security platform, but how do we track them and sunset them automatically.

Our Tines Customer Success Engineer recommeneded using [Tines Resources](https://www.tines.com/docs/resources/).  

A Tines Resource allows the storage of information outside of a workflow, that can be consumed in a Tines Action, downstream in the same workflow or by other workflows.

Here is an example of the Tines Sunset list used to track IPs:

<img src="./images/new_resource_sunset_list.png">


Via a web form submission page, a security analyst submits IPs, which are written to an IP sunset list, along with the submission date.  Below, we have an object with two key-value pairs: ip and date

<img src="./images/sunset_list_object.png">


Then, a series of actions create an IP shun list from the sunset list.  By a Tines AWS HTTP REST API call, the Tines workflow pushes the IP shun list to the S3 bucket.  Then, another action, calculates the MD5 hash of the IP shun file, which gets written to another Tines Resource.  The MD5 hash file is pushed to the S3 bucket.  Both the IP Shun List and MD5 hash files are consumed by the NGFW custom intelligence feed.

For sunsetting IPs, we had another series of actions which filtered out stale IP addresses.  Then, the next series of actions created an updated IP shun list and MD5 hash files which were posted to the S3 bucket.   In a few weeks, we were able to submit 

## Tines Documenation
- [Tines Resources](https://www.tines.com/docs/resources/)
- [Tines Object Function](https://www.tines.com/docs/formulas/functions/object/)
- [Tines MD5 Function](https://www.tines.com/docs/formulas/functions/md5/)
- [Tines Append Function](https://www.tines.com/docs/formulas/functions/append/)
- [Tines Community Edition](https://www.tines.com/pricing/)