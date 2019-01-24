# aws-accesslog-logstash-groks
Logstash groks used for AWS access logs

## Application Load Balancer
Example log entry:

```
https 2019-01-23T12:46:07.699948Z app/my-alb/3fd123dd98b6b9c 12.34.56.78:14331 10.0.1.2:5000 0.000 0.018 0.000 401 401 162 273 "POST https://example.com:443/api/example HTTP/1.1" "-" ECDHE-RSA-AES128-GCM-SHA256 TLSv1.2 arn:aws:elasticloadbalancing:eu-west-2:412345672514:targetgroup/api-asg/9831cb7a32a8e63d "Root=1-5c48620f-4d9e3f6747ab019ea87f7020" "example.com" "arn:aws:acm:eu-west-2:412345672514:certificate/e33a927d-9333-33cb-9038-88333b7f1cd6" 100 2019-01-23T12:46:07.682000Z "forward" "-" "-"
```

Logstash grok:
```
%{WORD:method} %{TIMESTAMP_ISO8601:timestamp} %{NOTSPACE:elb} %{IPORHOST:clienthost}:%{POSINT:clientport} %{IPORHOST:targethost}:%{POSINT:targetport} %{NUMBER:request_processing_time} %{NUMBER:target_processing_time} %{NUMBER:response_processing_time} %{NUMBER:elb_status_code} %{NUMBER:target_status_code} %{NUMBER:received_bytes} %{NUMBER:sent_bytes} %{QUOTEDSTRING:request} %{QUOTEDSTRING:user_agent} %{NOTSPACE:ssl_cipher} %{NOTSPACE:ssl_protocol} %{NOTSPACE:target_group_arn} %{QUOTEDSTRING:trace_id} %{QUOTEDSTRING:domain_name} %{QUOTEDSTRING:chosen_cert_arn} %{NOTSPACE:matched_rule_priority} %{TIMESTAMP_ISO8601:request_creation_time} %{QUOTEDSTRING:actions_executed} %{QUOTEDSTRING:redirect_url} %{QUOTEDSTRING:error_reason}
```

Results in structured data:
```
{
  "received_bytes": "162",
  "request": "\"POST https://example.com:443/api/example HTTP/1.1\"",
  "actions_executed": "\"forward\"",
  "target_processing_time": "0.018",
  "ssl_cipher": "ECDHE-RSA-AES128-GCM-SHA256",
  "clienthost": "12.34.56.78",
  "clientport": "14331",
  "sent_bytes": "273",
  "domain_name": "\"example.com\"",
  "error_reason": "\"-\"",
  "elb": "app/my-alb/3fd123dd98b6b9c",
  "user_agent": "\"-\"",
  "ssl_protocol": "TLSv1.2",
  "timestamp": "2019-01-23T12:46:07.699948Z",
  "targetport": "5000",
  "target_group_arn": "arn:aws:elasticloadbalancing:eu-west-2:412345672514:targetgroup/api-asg/9831cb7a32a8e63d",
  "trace_id": "\"Root=1-5c48620f-4d9e3f6747ab019ea87f7020\"",
  "matched_rule_priority": "100",
  "method": "https",
  "elb_status_code": "401",
  "request_processing_time": "0.000",
  "chosen_cert_arn": "\"arn:aws:acm:eu-west-2:412345672514:certificate/e33a927d-9333-33cb-9038-88333b7f1cd6\"",
  "response_processing_time": "0.000",
  "targethost": "10.0.1.2",
  "target_status_code": "401",
  "request_creation_time": "2019-01-23T12:46:07.682000Z",
  "redirect_url": "\"-\""
}
```
