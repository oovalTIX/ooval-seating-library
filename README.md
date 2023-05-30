# ooval-seating-library

![alt text](https://github.com/Coreeze/ooval-seating-library/blob/master/images/git-header-blue.png?raw=true)

The OOVAL Seating Library works in combination with the [OOVAL Ticketing Engine](https://ooval.readme.io) to power your ticketing infrastructure. 
The seating library is intended to be integrated in front-end applications (built for example with ReactJS). To provide you with a reliable, tried and tested seatmap solution, we partnered with [Seatsio](https://www.seats.io/).

## Install

```
npm install ooval-seating-library
```

## Usage

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
   selectionValidators={[
     { type: "noOrphanSeats" }
   ]}
   onObjectSelected={function (object) {
     selectedSeats.push(object.label);
   }}
   onObjectDeselected={function (object) {
     var index = selectedSeats.indexOf(object.label);
     if (index !== -1) selectedSeats.splice(index, 1);
   }}
 />
```

> **Important!**
> The details (eg. seat pricing) on the seatmap are **only** for visual purposes. The logic is 100% happeninng at the level of the [OOVAL Ticketing Engine](https://ooval.readme.io).

## Docs
The full docs of the seatmap can be found in the [OOVAL docs]() 

## See more
As we are using Seatsio for the visual seating map, the `OovalSeatingChart` is equivalent with the `SeatsioSeatingChart`. To see the full extent of the visual customization possible, read the [Seatsio ReactJS](https://docs.seats.io/docs/renderer/embed-a-floor-plan) library docs.
