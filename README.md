# ooval-seating-library

![alt text](https://github.com/Coreeze/ooval-seating-library/blob/master/images/git-header-blue.png?raw=true)

The OOVAL Seating Library works in combination with the [OOVAL Ticketing Engine](https://ooval.readme.io) to power your ticketing infrastructure.
The seating library is intended to be integrated in front-end applications (built for example with ReactJS). To provide you with a reliable, tried and tested seatmap solution, we partnered with [Seatsio](https://www.seats.io/).

- [🌱 Install](#-install)
- [🏗️ Usage](#%EF%B8%8F-usage)
- [🚀 Pricing](#-pricing)
- [🍿 Selection](#-selection)
- [🏟️ React to events](#%EF%B8%8F-react-to-events)
- [📖 Docs](#-docs)
- [🌌 Find out more](#-find-out-more)

# 🌱 Install

```
npm install ooval-seating-library
```

# 🏗️ Usage

- Step 1: to integrate the seatmap, you need to import the `OovalSeatingChart`:

```js
const { OovalSeatingChart } = require("ooval-seating-library");
```

or, if you are [using ES6](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/):

```js
import { OovalSeatingChart } from "ooval-seating-library";
```

- Step 2: creating a chart

The `public_workspace_key` and `event_seatmap_key` are part of the `seatmap` field in the [Event Object](https://ooval.readme.io/reference/the-event-object#the-seatmap-object)

```js
<OovalSeatingChart
  workspaceKey={event.public_workspace_key}
  event={event.event_seatmap_key}
  region="eu"
  session="continue"
  showMinimap={true}
  maxSelectedObjects={1}
  pricing={[
    { category: "Category 1", price: 40 },
    { category: "Category 2", price: 30 },
    { category: "Category 3", price: 20 },
  ]}
  priceFormatter={function (price) {
    return "€" + price;
  }}
  selectionValidators={[{ type: "noOrphanSeats" }]}
  onObjectSelected={function (object) {
    selectedSeats.push(object.label);
  }}
  onObjectDeselected={function (object) {
    var index = selectedSeats.indexOf(object.label);
    if (index !== -1) selectedSeats.splice(index, 1);
  }}
/>
```

- Step 3: call the OOVAL `create ticket` API - you need an API key

```js
const url = "https://sandbox-server.ooval.io/api/v1/tickets/create";
const options = {
  method: "POST",
  headers: {
    accept: "application/json",
    "x-api-key": "example-API-key",
    "content-type": "application/json",
  },
  body: JSON.stringify({
    tickets: [
      {
        seat_details: { label: "Floor 1 - Center Block-B-3" },
        category_name: "VIP Ticket",
        current_price: "150",
        current_currency: "€",
        type: "seated_ticket",
      },
      {
        seat_details: { label: "Floor 1 - Center Block-B-4" },
        category_name: "VIP Ticket",
        current_price: "150",
        current_currency: "€",
        type: "seated_ticket",
      },
    ],
    customer_email: "example@email.com",
    event_id: "646a28d84f84532ce8cf1084",
    event_name: "Bayern vs Dortmund",
  }),
};

fetch(url, options)
  .then((res) => res.json())
  .then((json) => console.log(json))
  .catch((err) => console.error("error:" + err));
```

> **Important!**
> The details (eg. seat pricing) on the seatmap are **only** for visual purposes. The logic is 100% happeninng at the level of the [OOVAL Ticketing Engine](https://ooval.readme.io).

# 🚀 Pricing

## `pricing`

- **Type**: `object[]`
- **Default**: `[]`

Making the single price point per category visible on the seating map.

> 🚧 Prices must be numbers, not strings
>
> For historical reasons, it's technically possible to pass in strings as price values. Doing so, however, breaks things like ordering, and displaying a minimum and maximum price in tooltips.
>
> So don't do price: `"10.00 €"` or `'10.00'`!  
> Instead, pass in price: `10.00` and define a `priceFormatter` to turn the number into a properly formatted price string

### Example

```javascript
pricing: [
  { category: 1, price: 30 },
  { category: 2, price: 40 },
  { category: 3, price: 50 },
];
```

Note that you can also use the category labels instead of their keys:

```javascript JavaScript
pricing: [
  { category: "Balcony", price: 30 },
  { category: "Ground Floor", price: 40 },
  { category: "Wheelchair", price: 50 },
];
```

## `priceFormatter`

- **Type**: `function(price)`
- **Default implementation**: return the raw price, as provided by the pricing configuration parameter (i.e. a number or a string).

A function that formats a price when its shown to an end user. This is notably used in the tooltip you see when you hover over a seat.

Note that the result of this function will be escaped, meaning you can't use html entities such as `$#36;`.

### Example

```javascript
<OovalSeatingChart
   ...
   priceFormatter={function (price) {
     return "€" + price;
   }}
	...
 />
```

# 🍿 Selection

## `selectionValidators`

- **Type**: `array`
- **Default**: `[]`

Selection validators run every time a seat is selected or deselected. They check whether there are no orphan seats, and/or whether all selected seats are consecutive (meaning: next to each other and in the same category).

## `noOrphanSeats`

Checks for orphan seats. An orphan seat is a single seat that's left open.

![alt text](https://github.com/oovalTIX/ooval-seating-library/blob/master/images/orphan-validator%402x.png?raw=true)

### Example

```javascript
selectionValidators: [{ type: "noOrphanSeats" }];
```

## `maxSelectedObjects`

- **Type**: `number | object[]`
- **Default**: not set

Restrict the number of objects a user is allowed to select.

This restriction can be implemented in two ways: as a maximum total number of selected objects (by passing in a number), or you can set different limits for each category or ticket type, or even combination thereof (by passing in an object).

### Example

Number:

```javascript
<OovalSeatingChart
  ...
  maxSelectedObjects={10}
  ...
/>
```

Limit per ticket type:

```javascript
maxSelectedObjects: [
  { ticketType: "adult", quantity: 2 },
  { ticketType: "child", quantity: 3 },
  { total: 4 },
];
```

> 🚧 ONLY PASSED CATEGORIES WILL BE SELECTABLE
>
> If you don't pass in all categories, the ticket buyer will not be able to select tickets in the missing categories. E.g. if the max number of balcony tickets is set to 2, and no max is set for stalls tickets, the ticket buyer will only be able to select balcony tickets.

# 🏟️ React to events

## `onObjectSelected`

- **Type**: `function(object)`

Fired when the user selects an object. The selected object is passed into this function as a parameter.

### Example

```coffeescript
const selectedSeats = [];

<OovalSeatingChart
	...
  onObjectSelected={function (object) {
  	selectedSeats.push(object.label);
  }}
	...
/>
```

## `onObjectDeselected`

- **Type**: `function(object)`

Fired when the user deselects an object. The deselected object is passed into this function as a parameter.

### Example

```javascript
const selectedSeats = [];

<OovalSeatingChart
	...
  onObjectDeselected={function (object) {
    var index = selectedSeats.indexOf(object.label);
  	if (index !== -1) selectedSeats.splice(index, 1);
  }}
	...
/>
```

# 📖 Docs

The full docs of the seatmap can be found in the [OOVAL docs](https://ooval.readme.io).

# 🌌 Find out more

As we are using Seatsio for the visual seating map, the `OovalSeatingChart` is equivalent with the `SeatsioSeatingChart`. To see the full extent of the visual customization possible, read the [Seatsio ReactJS](https://docs.seats.io/docs/renderer/embed-a-floor-plan) library docs.
