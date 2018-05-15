# @botpress/broadcast

## X: Migrated. Requires documentation update.

## Get started

```
botpress install broadcast
```

The broadcast module should now be available in your bot UI, and the APIs exposed.

## Features

### Send according to Users timezone

You can decide wether the scheduled time is abolute to the bot's time or to individual users. If no timezone information available for the user, GMT is chosen.

### Send as Javascript snippet

Instead of sending text, you can decide the behavior of the sending function and do basically anything. The function will be called for every message (so for every user).

Variables exposed: 

- `bp` botpress instance
- `userId` the userId to send the message to
- `platform` the platform on which the user is on

The built-in Facebook Messenger snippets are example of Javascript execution (see UI).

### Filtering

You can apply filters to the broadcasts. Filters are small javascript functions that will be evaluated before sending the broadcast to a user. The condition is called for every users the broadcast is scheduled to.

Variables exposed to the filter function:
- `bp` botpress instance
- `userId` the userId to send the message to
- `platform` the platform on which the user is on

The function needs to return a **boolean** or a **Promise of a boolean**.

**Note:** Starting your function with `return ` is optional.

#### Examples

##### Send a message only to users on Facebook

```js
["platform === 'facebook'"]
```

##### Send a message only to subscribed users 

**Note**: Assuming your bot has a `subscriptions` table that holds userId and platform

```js
// in your bot's index.js

bp.isUserSubscribed = (userId, platform) => {
  return bp.db.get()
  .then(knex => 
    knex('subscriptions')
    .where({ userId, platform })
    .select('count(*) as count')
    then().get(0).then(({count}) => count > 0)
  )
}
```

```js
["bp.isUserSubscribed(userId)"]
```

## Roadmap

- User segmentation

## API

### `GET /api/botpress-broadcast/broadcasts`

Returns a list of the scheduled broadcasts.

### `PUT /api/botpress-broadcast/broadcasts`

Schedules a new broadcast.

#### Body

```js
{
  date: string, // *required*, 'YYYY-MM-DD'
  time: string, // *required*, 'HH:mm'
  timezone: null|int, // null (users timezone), or integer (absolute timezone)
  type: string, // *required*, 'text' or 'javascript'
  content: string // *required*, the text to be sent or the javascript code to execute,
  filters: [string] // filtering conditions, Javascript code
}
```

#### Response

```
"Hello, Human!"
```

### `POST /api/botpress-broadcast/broadcasts`

Update an existing broadcast. Same as PUT except that `id` is also necessary. You can't modify a processing broadcast.

### `DELETE /api/botpress-broadcast/broadcasts/:id`

Delete an existing broadcast. You can't delete a processing broadcast.

## Community

Pull requests are welcomed! We believe that it takes all of us to create something big and impactful.
There's a [Slack community](https://slack.botpress.io) where you are welcome to join us, ask any question and even help others.

Get an invite and join us now! 👉[https://slack.botpress.io](https://slack.botpress.io)

## License

botpress-broadcast is licensed under [AGPL-3.0](/LICENSE)
