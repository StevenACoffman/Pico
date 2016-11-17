## POST

```bash
curl -X POST -H "Content-Type: application/json; charset=UTF-8" -d '{"bit":true}' "http://ec2-54-234-125-241.compute-1.amazonaws.com:32782/watchable/dipswitch/show_survey_comm_388"
```

## POST with file payload (yes, you need the `@`)

```bash
curl -X POST -H "Content-Type: application/json; charset=UTF-8" -d @payload.json  "http://ec2-54-234-125-241.compute-1.amazonaws.com:32782/watchable/dipswitch/show_survey_comm_388"
```

## GET

```bash
curl -X GET -i -H "Accept: application/json" "http://ec2-54-234-125-241.compute-1.amazonaws.com:32782/watchable/dipswitch/mock_has_autorenew"
```
