
# The Technician 
----------------
This technical breakdown describes a real workflow in an unrelated pipeline. In order to see the process capabilities and their actual use case scenario.

To go back [Click Here](README.md)

[![workflow diagram](media/xsoll-eventapi.png "Diagram of Process")](https://developers.xsolla.com/doc/partner-network/features/tracking-analytics/#partner_network_features_tracking_analytics_get_token)
 
example send user_id and tracking_id:
https://playnewz.com?utm_source=n6LI9yVu&utm_campaign=5b9bff5f9d31b&tracking_id=19e2DLjNTk2YOdXA4d8J3NReNkXNafhC


----------------------------------------------
GET TOKEN HTTP
POST https://tracking-api.xsolla.com/v1/tokens
______________________________________________

Header
ContentType	application/json
Authorization
your_authorization_basic_key
merchantId:apiKey 

merchantId: Company settings > Company > Merchant ID
apiKey: Company settings > API key

Parameter
sourceType client
 trusted: false
sourceType server
  trusted: true
sourceName
projectId

Response code
201   token created
400   query perams invalid
401   proj key invalid

Example:
______________________________________________ 

http:

```
POST https://tracking-api.xsolla.com/v1/tokens 

Headers:
Content-Type: application/json
Authorization: Basic 12kj3hlk1j2hlkjhlk1j2h3lkj

Body:
{
  "sourceType": "client",
  "sourceName": "landing",
  "projectId": 1
}

HTTP/1.1 201 Token created
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsaW5rIjoiaHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2 g_dj1kUXc0dzlXZ1hjUSJ9.aVigY6UVY3jgoEKoBv31cZnROL3I6WKtcr5K-Z7B1du"
}
```

php:

```
<?php

$curl = curl_init();

$merchantId = 1;
$projectId = 2;
$apiKey = '<apiKey>';

$payload = [
    'sourceType' => 'server',
    'sourceName' => 's1',
    'projectId' => $projectId,
];

curl_setopt($curl, CURLOPT_USERPWD, $merchantId . ":" . $apiKey);


curl_setopt_array($curl, array(
    CURLOPT_URL => "https://tracking-api.xsolla.com/v1/tokens",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => "",
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 30,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => "POST",
    CURLOPT_POSTFIELDS => json_encode($payload),
    CURLOPT_HTTPHEADER => [
        "Content-type: application/json"
    ],
));


$response = curl_exec($curl);
$err = curl_error($curl);

curl_close($curl);

if ($err) {
    echo "cURL Error #:" . $err;
} else {
    echo $response;
}
```

__________________________________________
------------------------------------------
Registration event sending
From xsolla proxy page to website
__________________________________________
HTTP Request
POST https://tracking-api.xsolla.com/v1/events

Proxy Collects:
Click ID    # via events api endpoint
Redirects:
Tracking ID

Website Collects:
Tracking ID
Generates:
User ID     # via token api endpoint
Sends:
User ID + Tracking ID

Header
Authorization <token>

Parameter
type        string="<token>"
traits      Object($trackingid, $userid)
context     Object(null) *must be empty
properties  Object(null) *must be empty

Example:
_____________________________________
http:

```
POST https://tracking-api.xsolla.com/v1/events

Headers:
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsaW5rIjoiaHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g_d j1kUXc0dzlXZ1hjUSJ9.aVigY6UVY3jgoEKoBv31cZnROL3I6WKtcr5K-Z7B1yU

Body:
{
  "type": "registration",
  "traits": {
    "trackingId": "19e2DLjNTk2YOdXA4d8J3NReNkXNafhC",
    "userId": "20181126"
  },
  "properties": {
  },
  "context": {
  }
}'
```

php:

```
<?php

$curl = curl_init();

$token  = '<token>';

$payload = [
    'type' => 'registration',
    'traits' => [
        'trackingId' => "19e2DLjNTk2YOdXA4d8J3NReNkXNafhC",
        'userId' => "20181126",
    ],
    'properties' => [],
    'context' => [],
];

curl_setopt_array($curl, array(
    CURLOPT_URL => "https://tracking-api.xsolla.com/v1/events",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => "",
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 30,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => "POST",
    CURLOPT_POSTFIELDS => json_encode($payload,JSON_FORCE_OBJECT),
    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$token}",
        "Content-type: application/json"
    ],
));

$response = curl_exec($curl);
$err = curl_error($curl);

curl_close($curl);

if ($err) {
    echo "cURL Error #:" . $err;
}
```
_____________________________________________
---------------------------------------------
Create Event    # step that creates User ID on signup at website
_____________________________________________

HTTP REQUEST: 
POST https://tracking-api.xsolla.com/v1/events

Header 
Authorization <token>
Parameter 
type	string="Event Type"  *ie @signup @upgrade @coupon
traits	Object[$userdata) *var= [$phone, $email, $uUid, $etc]
              # identification data: phone, email, in-game ID, etc.
context	Object($context)  # Contextual data.
properties	Object($eventproperties) 
              # Event properties.
createdAt	Date(datetime)  
              # Format: datetime per RFC 3339 or ISO 8601.

Response codes:
204   event added
400   event invalid
401   token invalid

Example transition event to landing:
__________________________________
http:

```
POST https://tracking-api.xsolla.com/v1/events

Headers:
Content-Type: application/json
Authorization: Bearer
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsaW5rIjoiaHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g_d j1kUXc0dzlXZ1hjUSJ9.aVigY6UVY3jgoEKoBv31cZnROL3I6WKtcr5K-Z7B1yU 

Body:
{
  "type": "landing_visit",
  "traits":{
    },
  "properties":{
    },
  "context": {
    "user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36", "user_locale":"ru"
  }
}
```

_________________________________________________

EXAMPLE EVENT: TRANSITION TO PAY STATION
_________________________________________________

http:

```
POST https://tracking-api.xsolla.com/v1/events

Headers:
Content-Type: application/json
Authorization: Bearer
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsaW5rIjoiaHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g_d j1kUXc0dzlXZ1hjUSJ9.aVigY6UVY3jgoEKoBv31cZnROL3I6WKtcr5K-Z7B1yU 

Body:
{
  "type": "buy_btn",
  "traits":{
    },
  "properties":{
    "pkg_type":"bronze"
    },
  "context": {
    "user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36", "user_locale":"ru"
  }
}
```
_________________________________________________

INITIALIZING:
html:

```
xnt("init", YOUR_TOKEN);
```

SENDING:
html:

```
xnt("sendEvent", EVENT_TYPE, EVENT_PROPERTIES);
```

EXAMPLE:
html:

```
xnt("sendEvent", "landing_visit");
xnt("sendEvent", "buy_btn", { pkg_type: $(this).data('id') });
```

Description:

```
xnt("sendEvent", EVENT_TYPE, EVENT_PROPERTIES);
xnt("putContext", CONTEXT_OBJECT);
xnt("putTraits", TRAITS_OBJECT);

EVENT_TYPE="landing_visit"
EVENT_PROPERTIES[JS_Object: event-parameters]

JS_Object=[event-parameters, context, user-data]

CONTEXT_OBJECT[JS_Object(context)]
TRAITS_OBJECT[JS_Object(user-data)]

var event-parameters = a[source, type, traits, context, properties, createdAt, timestamp]

var source = s;
var s = [paystation, landing_page, etc];

var type = t;
var t = [user_visit, hit, etc];

var traits = tr;
var tr = [email, user_id, nickname, etc]

var context = cx;
var cx = [ip, gaClientId, etc]

var properties = props;
var props = [sum, levelup, etc]

var createdAt = dt; 
const dt = new dateTime(); 

const timestamp = new timestamp();

const a = appendData(f(i));
```

Context parameters userAgent, userLocal passed automatically passes as appended values.
________________________________________

IMPLEMENT SCRIPT:

```
<script>
(function(i,s,o,g,r,a,m){i['XsollaNetworkTrackingObject']=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(ar guments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.inse rtBefore(a,m)})(window,document,'script','https://cdn.xsolla.net/network/xtracking-0.1.js','xnt');
xnt("init", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsaW5rIjoiaHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g_ dj1kUXc0dzlXZ1hjUSJ9.aVigY6UVY3jgoEKoBv31cZnROL3I6WKtcr5K-Z7B1du");
xnt("sendEvent", "buy_btn", { pkg_type : "gold" });
</script>
```
 







------------------------------------------
n8n start node
__________________________________________

```
{
  "nodes": [
    {
      "name": "Start",
      "type": "n8n-nodes-base.start",
      "position": [
        -40,
        240
      ],
      "parameters": {},
      "typeVersion": 1
    },
    {
      "name": "Gmail",
      "type": "n8n-nodes-base.gmail",
      "position": [
        150,
        240
      ],
      "parameters": {
        "resource": "message",
        "operation": "getAll",
        "returnAll": true,
        "additionalFields": {
          "q": "-in:chats unsubscribe -license -key -password",
          "format": "ids"
        }
      },
      "credentials": {
        "gmailOAuth2": "Gmail"
      },
      "typeVersion": 1
    },
    {
      "name": "Delete Old Gmail",
      "type": "n8n-nodes-base.gmail",
      "position": [
        500,
        410
      ],
      "parameters": {
        "resource": "message",
        "messageId": "={{$json[\"id\"]}}",
        "operation": "delete"
      },
      "credentials": {
        "gmailOAuth2": "Gmail"
      },
      "typeVersion": 1
    },
    {
      "name": "SplitInBatches",
      "type": "n8n-nodes-base.splitInBatches",
      "position": [
        310,
        240
      ],
      "parameters": {
        "options": {},
        "batchSize": 100
      },
      "typeVersion": 1
    }
  ],
  "connections": {
    "Gmail": {
      "main": [
        [
          {
            "node": "SplitInBatches",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Start": {
      "main": [
        [
          {
            "node": "Gmail",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "SplitInBatches": {
      "main": [
        [
          {
            "node": "Delete Old Gmail",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Delete Old Gmail": {
      "main": [
        [
          {
            "node": "SplitInBatches",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```
