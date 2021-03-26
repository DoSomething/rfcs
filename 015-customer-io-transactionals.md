In 2021, we began to [only maintain Customer.io profiles for active members who have opted in to receive email and/or SMS promotions](https://www.pivotaltracker.com/epic/show/4721712). The workflows in Customer.io campaigns are convenient because they provide a no-code way to send transactional email and/or SMS messages -- but it does require that a member has a Customer.io profile in order to receive the transactional message.

This RFC details how we could instead use Northstar model event observers to make the relevant API requests to either send a trasnactional email message ID or SMS broadcast message ID, to avoid maintaing a Customer.io profile for a member to send them the relevant confirmation message.

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
