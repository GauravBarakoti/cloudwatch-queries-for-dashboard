# CloudWatch Queries for IIS logs

First please configure the CloudWatch with Windows.

Remember, While installing the cloudwatch-agent provide the logs path of the IIS-server.

Here I'm using a specific logging pattern you can adjust the pattern and parse according to the logs pattern.

We can also use regex {regular expression} for queries.

Query for status code counts:

```bash
fields @message
| parse @message "* * * * /* - * * * *" as date, time, instanceip, method, uri_query, ip_hitting, user_agent, status 
| stats count(status) as status_code by status
| sort status_code desc
```

Query for user-agent {browser details}:

```bash
fields @message
| parse @message "* * * * /* - * * *" as date, time, instanceip, method, uri_query, ip_hitting, user_agent 
| stats count(user_agent) as visits by user_agent
| sort visits desc
| limit 6
```

Query to count the IP hitting your site:

```bash
fields @message
| parse @message "* * * * /* - * *" as date, time, instanceip, method, uri_query, ip_hitting
| stats count(ip_hitting) as max_hits by ip_hitting
| sort max_hits  desc
| limit 7
```

Query for / path URIs:

```bash
fields @message
| parse @message "* * * * /* - " as date, time, instanceip, method, uri_query
| filter !isblank(uri_query)
| stats count(uri_query) as uri_quries by uri_query
| sort uri_quries  desc
| limit 6
```