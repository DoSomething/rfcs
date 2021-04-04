In 2021, we began to [only maintain Customer.io profiles for active members who have opted in to receive email and/or SMS promotions](https://www.pivotaltracker.com/epic/show/4721712). The [workflows we use in Customer.io](https://customer.io/visual-workflow-builder/) are convenient because they provide a no-code way to send transactional email and/or SMS messages -- but it does require that a member has a Customer.io profile in order to receive the transactional message. It's also a bit brittle (e.g. -- an admin could accidentally delete the workflow that sends our SMS compliance message if someone opts-in to receive texts via web)

This RFC details how we could instead use Northstar model event observers to make the relevant API requests to either send a trasnactional email message ID or SMS broadcast message ID, to avoid maintaing a Customer.io profile for a member to send them the relevant confirmation message.

## Emails

We should use the App API to send confirmation emails, so members who aren't subscribed to promotions will still receive transactional emails for these events:

| Event | New job | New config variable |
|-------|----------|-----------------|
| Signup created | `SendSignupCreatedEmail` | `signup_created_email_message_id` |
| Post created | `SendPostCreatedEmail` | `post_created_email_message_id` | 
| Post approved | `SendPostApprovedEmail` | `post_approved_email_message_id` | 
| Post rejected | `SendPostRejectedEmail` | `post_rejected_email_message_id` | 
| User Club ID changed | `SendUserClubChangedEmail` | `user_club_changed_email_message_id`

The config variables will store [transactional message ID's](https://customer.io/docs/transactional-api#transactional-message-template-code-databackticks1transactional_message_idcode) to use for each confirmation message can be stored in config variables:


### Overrides


Add fields to the relevant Campaign and Action models to store the transactional message ID to override the transactional messages for a signup or post:

#### Campaign

* `signup_created_email_message_id` 

#### Action

* `post_created_email_message_id`
* `post_accepted_email_message_id`
* `post_rejected_email_message_id`


## SMS

Instead of relying on Customer.io campaigns to send SMS compliance messages, modify Northstar to execute a Gambit API request. We can store config variables with the suffix `sms_message_id` to track the broadcast message ID's to send for each event, if we don't want to hardcode them. For SMS compliance messages -- we've always gone with hardcoding this message text in case its Contentful entry could accidentally be deleted.

* Changed SMS Status to `active`
* Changed SMS Status to `less` -- not sure we send this (yo'ud think not since there isn't a `POST messages?origin=subscriptionStatusLess` route
* Did not finish their voter registration (a `voter-reg` post has been created with a non-completed status)

## Notes

* We'd still want to track events for campaign signups and reportbacks (e.g. `campaign_signup`, `campaign_signup_post`) in order to create Customer.io segements based on members' past campaign activity.
