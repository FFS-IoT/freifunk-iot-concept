For authentication a NoSQL databse is used, with objects like the following:

Sensor:
```
{
    "sensorid": "sensor123",
    "token": "123abc",
    "pubkey": "123abc",
    "auth_level": "<none|token|signature>",
    "acl": {
             "read": ["guest"],
             "manage": ["user1"],
             "delete": ["user1"]
           },
    "datatypes": {
                   "channel1": "int",
                   "channel2": "float",
                   "channel3": "string"
                 },
    "errorcodes": {
                   "1":         "Internal sensor error"
    },
    "events": {
                    "onDataIn":     ["hook.DataIn"],
                    "minutes(5)":   ["utils.checkInterval"]
    }
}
```

User:
```
{
    "username": "user1",
    "pwhash": "123abc",
    "email": "test@test.test",
    "2fa": "123abc",
    "notification_hooks": [{"module":"hook",
                            "options": {"url":"example.com/hook",
                                        "method":"POST"}
                            },
                            {
                            "module":"im",
                            "options": {"messenger":"Signal",
                                        "phone":"000000000"};
                            }]
}
```
