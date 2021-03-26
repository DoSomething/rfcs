## Emails

Use the App API for sending confirmation emails. This way, members who aren't subscribed for promotions will still receive transactional emails for these events:

* Signup created
* Post created
* Post accepted
* Post approved
* User Club ID changed

## SMS

Instead of relying on Customer.io campaigns to send SMS compliance messages, modify Northstar to execute a Gambit API request.

* Changed SMS Status to `active`
* Changed SMS Status to `less` -- not sure we send this (yo'ud think not since there isn't a `POST messages?origin=subscriptionStatusLess` route
* Did not finish their voter registration (a `voter-reg` post has been created with a non-completed status)
