

| type     | no auth | token auth | cors filter | ipfilter |     |     |
| -------- | ------- | ---------- | ----------- | -------- | --- | --- |
| external | true    | false      |             |          |     |     |
| internal | p       | 0x0001     |             |          |     |     |

 

no auth : 
	allowd by:
		any person from
		any domain from
		any machine aka cli client
		
token auth :
	allowed by:
		authenticate person from any domain and any machine


cors filter:
	allowed by :
		any person from any machine but webpages with restricted origin cannot access
		
ip filter:
	filter any machine ip


- ip filter :should only be applie to internal apis that are not using in a webpage 
- cors can be use to deny certain webpage from making a request but it cannot filter any cli client. or any maching like using cur 
- 
