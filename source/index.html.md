---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: http

toc_footers:
  - <a href='#'>Sign Up for a storeID</a>

includes:
  - errors

---

# Intro

Welcome to the Blip.delivery API! Our API endpoints can be bundled into any application using the npm module `blip-deliveries`.

 This documents the use of the Blip.delivery API using server-side node.js, and will eventually document the library in other backend laguages.

 To get started with a live account, contact Blip to get a `storeID`. Alternatively, you can play around with the functions in testmode, by replacing the `storeID` with `'test'`

# Getting started

> To authorize, use this code:

```shell
# Authorization is done on a per-call basis. No authorization setup is needed when using Http
```

Blip uses a storeID to allow access to the API. You can register by [contacting sales](http://www.blip.delivery/).

<aside class="notice">
You must replace <code>YOURSTOREID</code> with your own storeID.
</aside>

# Get a delivery quote

This retrieves a live price for a delivery, given two addresses. If your account uses subscription based pricing, use `getDeliveriesLeft` instead.

### Required JSON

> Example request:

```shell
https://api.blip.delivery/getDeliveryPrice # Live API endpoint
https://test.blip.delivery/getDeliveryPrice # Test API endpoint

JSON body:

{
	pickupAddress: "156 Enfield Place, Mississauga, ON",
    deliveryAddress: "100 City Centre drive, Mississauga, ON",
	storeID: YOUR_STOREID_HERE
}
```

Parameter | Description
--------- | -----------
pickupAddress | The address of your pickup point. Include the locality + city
deliveryAddress | The address of your delivery point. Include the locality + city
storeID | Your storeID

<aside class="success">
Remember — Blip uses googlemaps to parse the address. Always ensure you enter the complete address, or we will be unable to verify the correct coordinates.
</aside>

> Example response:

```json
{
  "blipFee": 100,
  "price": 337,
  "totalPrice": 447,
  "duration": "41 min(s)"
}
```

### Response JSON

Parameter | Description
--------- | -----------
blipFee | Integer fee of the API call
price | Integer price of the delivery
totalPrice | Integer price of the delivery charged to your account
duration | String describing the estimated duration for the delivery

<aside class="notice">
The prices returned are all in cents. eg. 421 == $ 4.21
</aside>

# Create a new delivery

This creates a new delivery request, and charges the card on file. If you do not have any card on file with us, [download]() the Blip Store app, and update your card details.

> Example request

```shell
https://api.blip.delivery/makeDeliveryRequest # Live API endpoint

https://test.blip.delivery/makeDeliveryRequest # Test API endpoint

JSON body:

{
	delivery: {
        instructions: "Deliver to the lobby",
        contact: {
            name: "John Smith",
            number: "+16479839837"
        },
        location: {
            address: "156 Enfield Place, Mississauga, ON" 
        }
    },
    pickup: {
        order_number: "ABC123",
        instructions: "Pickup from the main desk",
        contact: {
            number: "+16478229867"
        },
        location: {
            address: "200 Burnhamthorpe road west, Mississauga, ON"
        }
    },
	metadata: {
		orderNumber: "ABC123",
		appID: "Shopify"
	},
	storeID: "L12354Hhhf9-f"
}
```

### Required JSON

Parameter | Description
--------- | -----------
delivery.contact.name | String name of the reciever
delivery.contact.number | String phone number of the reciever
delivery.location.address | String address of the delivery location
delivery.instructions | String instruction for the driver upon reaching the delivery point `OPTIONAL`
metadata.orderNumber | String order number for your own reference
metadata.appID | String descrbing the app that made the order `OPTIONAL`
pickup.instructions | String instructions for the driver upon reaching the pickup location `OPTIONAL`
pickup.contact.number | String phone number the driver can call if there is a problem during pickup
pickup.location.address | String address of the pickup location
storeID | String of the storeID you recieved when signing up with blip

> Example response:

```json
{
	"storeID": "-LJlJ-xuYqEtgs6C1qky",
	"delivery": {
		"contact": {
			"name": "John Smith",
			"number": "+16479839837"
		},
		"location": {
			"address": "156 Enfield Place, Mississauga",
			"latitude": 43.5907771,
			"longitude": -79.6340031
		},
		"instructions": "Deliver to the lobby"
	},
	"pickup": {
		"contact": {
			"number": "+16478229867"
		},
		"location": {
			"address": "200 Burnhamthorpe road west, Mississauga",
			"latitude": 43.5890505,
			"longitude": -79.64045229999999
		},
		"instructions": "Pickup from the main desk"
	},
	"metaData": {
		"orderNumber": "ABC123",
		"appID": "shopify"
	},
	"status": {
		"timeCreated": 1537389047
	},
	"chargeID": "ch_1DCCJvA4IkmlaKtpe3YcDT2G",
	"id": "PCAPIIL"
}
```

### Response JSON

Parameter | Description
--------- | -----------
delivery | A delivery object composed of a contact, location, and instruction.
pickup | A pickup object composed of an order number, contact, location, and instruction.
metadata | An object containing an order number for your own reference plus an optional appID for example "shopify"
delivery.contact.name | String name of the reciever
delivery.contact.number | String phone number of the reciever
delivery.location.address | String address of the delivery location
delivery.location.latitude | Coordinate of the delivery location's latitude
delivery.location.longitude | Coordinate of the delivery location's longitude
delivery.instruction | String instruction for the driver upon reaching the delivery point
metadata.orderNumber | String order number for your own reference
metadata.appID | String to reference the app from which this request has been generated
pickup.instructions | String instructions for the driver upon reaching the pickup location
pickup.contact.number | String phone number the driver can call if there is a problem during pickup
pickup.location.address | String address of the pickup location
pickup.location.latitude | Coordinate of the pickup location's latitude
pickup.location.longitude | Coordinate of the pickup location's longitude
status.timeCreated | UNIX timestamp when the delivery request was generated
chargeID | An ID referencing the charge made to the card on file
id | The deliveryID of the generated delivery

<aside class="notice">
Remember to save the <code>deliveryID</code> in your database, so you can retrieve it's status later.
</aside>

# Get delivery status

This retrieves a delivery object, and it's current status

> Example request

```shell
https://api.blip.delivery/getDeliveryStatus # Live API endpoint

https://test.blip.delivery/getDeliveryStatus # Test API endpoint

JSON body:

{
	"deliveryID": "ASF781",
	"storeID": "L12354Hhhf9-f"
}
```

### Required JSON

Parameter | Description
--------- | -----------
deliveryID | String deliveryID of a delivery created using `makeDeliveryRequest`
storeID | String of the storeID you recieved when signing up with blip

> Example response:

```json
{
	timeTaken: 1537144441,
	timePickedUp: 1537144845,
	timeDelivered: 1537145169,
	timeCreated: 1537144418,
	pickupETA: 1537144845,
	dropoffETA: 1537144588,
	courier: {
		name: {
			firstName: "Tai",
			lastName: "Lopez"
		}
		driverID: "bafa0a6ffbbead086d2aff257bdae684",
		number: "+16479839837",
		picture: "https://firebasestorage.googleapis.com/v0/b/blip-testapp.appspot.com/o/profilePictures%2F97548650-F8A1-4905-8D80-BDB4CBC6750C?alt=media&token=2bc75dd7-79f5-4bab-b862-8719aa7c4bb9"
	}
}
```

### Response JSON

Parameter | Description
--------- | -----------
timeTaken | UNIX timestamp in seconds of the time the delivery was accepted by a courier
timePickedUp | UNIX timestamp in seconds of the time the delivery was picked up
timeDelivered | UNIX timestamp in seconds of the time the delivery was delivered
timeCreated | UNIX timestamp in seconds of the time the delivery was created
pickupETA | UNIX timestamp in seconds of an estimated time of pickup
dropoffETA | UNIX timestamp in seconds of an estimated time of dropoff
courier.name.firstName | String firstname of the courier on the job
courier.name.lastName | String lastname of the courier on the job
courier.driverID | String of the courier's ID
courier.number | String of the phone number of the courier
courier.picture | String of the URL for the courier's profile picture

# Cancel delivery

You can cancel a delivery if it hasn't already been accepted by a courier. We will eventually add support for all types of cancellations, but currently only allow for cancellations before a courier has accepted a delivery request. A 100% refund will trigger upon cancellation, and your funds will appear within 5 business days.

> Example request

```shell
https://api.blip.delivery/cancelDelivery # Live API endpoint

https://test.blip.delivery/cancelDelivery # Test API endpoint

JSON body:

{
	"deliveryID": "ASF781",
	"storeID": "L12354Hhhf9-f"
}
```

### Required JSON

Parameter | Description
--------- | -----------
deliveryID | String deliveryID of a delivery created using `makeDeliveryRequest`
storeID | String of the storeID you recieved when signing up with blip

> Example response:

```json
{
	"refund": {
		"id": "re_1DCHmRA4IkmlaKtpRZC7i0mx",
		"object": "refund",
		"amount": 571,
		"balance_transaction": "txn_1DCHmRA4IkmlaKtpC6JdMukg",
		"charge": "ch_1DCCJvA4IkmlaKtpe3YcDT2G",
		"created": 1537410035,
		"currency": "cad",
		"metadata": {},
		"reason": null,
		"receipt_number": null,
		"status": "succeeded"
	}
}
```

### Response JSON

Parameter | Description
--------- | -----------
refund | A refund object verifying a successful refund was made

# Get driver location

After a delivery has been accepted, it contains a `courier` property. The `courier` is a driver that has been dispatched, who's location updates every 500-700 meters. This function should not be called more than once every 1 minute, as doing so may result in excess charges to your account. Furthermore, a status code of `400` will be returned if the `deliveryID` references a delivery that has not been picked up. To ensure a status code of `200`, make sure to call `getDeliveryStatus` first, and check if it contains a `courier` property.

To get the current location of your driver, use `getDriverLocation(options)` where `options` is an object containing the `deliveryID` of the delivery to track

> Example request

```shell
https://api.blip.delivery/getDriverLocation # Live API endpoint

https://test.blip.delivery/getDriverLocation # Test API endpoint

JSON body:

{
	deliveryID: "ASF781",
	storeID: "L12354Hhhf9-f"
}
```

### Required JSON

Parameter | Description
--------- | -----------
deliveryID | String deliveryID of a delivery created using `createNewDelivery`
storeID | String of the storeID you recieved when signing up with blip

> Example response:

```json
{	location: {
		latitude: 43.5890505,
		longitude: -79.6404522
	}
}
```

### Response JSON

Parameter | Description
--------- | -----------
location.latitude | Number indicating the current latitude of the driver on your job
location.longitude | Number indicating the current longitude of the driver on your job

# Get deliveries

Sometimes, you may need all the delivery requests created by your store. You can use `getDeliveries` to get an array of all the `deliveryID`'s associated with your `storeID`

> Example request

```shell
https://api.blip.delivery/getDeliveries # Live API endpoint

https://test.blip.delivery/getDeliveries # Test API endpoint

JSON body:

{
	"storeID": "L12354Hhhf9-f"
}
```

### Required JSON

Parameter | Description
--------- | -----------
storeID | String of the storeID you recieved when signing up with blip

> Example response:

```json
{
	"deliveries": [
					"FQAFS8",
					"QSFG12",
					"QAFS91"
				]
}
```

### Response JSON

Parameter | Description
--------- | -----------
deliveries | Array of `deliveryID`'s associated with your `storeID`

# Get delivery

Sometimes, you might want to get a single delivery object that you can reference with it's corresponding `deliveryID`. To do this, use `getDelivery`, passing in a `deliveryID` in the request body. Alternatively, if you do not want to keep a track of the deliveryID, you can pass in an `orderNumber` & `appID` pair, and it'll retrieve the corresponding delivery, without needing to know the `deliveryID`

> Example request

```shell
https://api.blip.delivery/getDelivery # Live API endpoint

https://test.blip.delivery/getDelivery # Test API endpoint

JSON body:

{
	storeID: "L12354Hhhf9-f",
	deliveryID: "NJKFN8",
	appID: "Shopify",
	orderNumber: "ABC123"
}
```

### Required JSON

Parameter | Description
--------- | -----------
storeID | String of the storeID you recieved when signing up with blip
deliveryID | String of the deliveryID of the corresponding delivery object you want to retrieve
appID | String of the appID passed when creating the delivery `OPTIONAL`
orderNumber | String of the orderNumber passed when creating the delivery `OPTIONAL`

> Example response:

```json
{
	"delivery": {
		"deliveryStatus": {
			"timeTaken": null,
			"timePickedUp": null,
			"timeDelivered": null,
			"timeCreated": 1541796957,
			"pickupETA": null,
			"dropoffETA": null,
			"courier": null
		},
		"id": "4OOHNJT",
		"delivery": {
			"contact": {
				"name": "John Smith",
				"number": "+16479839837"
			},
			"instructions": "Drop off at security",
			"location": {
				"address": "156 Enfield Place, Mississauga",
				"latitude": 43.5907771,
				"longitude": -79.6340031
			}
		},
		"pickup": {
			"contact": {
				"name": "James Bean",
				"number": "+16479839836"
			},
			"instructions": "Pickup from main desk",
			"location": {
				"address": "200 Burnhamthorpe road west, Mississauga",
				"latitude": 43.5890505,
				"longitude": -79.64045229999999
			},
		},
		"metadata": {
			"orderNumber": "ABC123",
			"appID": "Shopify"
		}
	}
}
```

### Response JSON

Parameter | Description
--------- | -----------
delivery | The delivery object
deliveryStatus | The delivery status object
id | The deliveryID of the delivery object
delivery (child object of the main delivery object) | The dropoff object
pickup | The pickup object


