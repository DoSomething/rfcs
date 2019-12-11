# On-Call

We should use [PagerDuty](http://pagerduty.com) to centralize our alerting in one place & manage an on-call rotation.

**Discussion:** https://github.com/DoSomething/rfcs/pull/7

## Problem

We rely on a plethora of monitoring services ([New Relic](https://newrelic.com), [Papertrail](https://papertrailapp.com), [Ghost Inspector](https://ghostinspector.com), [Runscope](https://www.runscope.com), [MongoDB Atlas alerts](https://docs.atlas.mongodb.com/monitoring-alerts/), and [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/)) that all have their own configuration & notification methods.

It's also not clear who's on-call at any given time, and so _everyone's_ always effectively on-call. This isn't ideal, since it means that people always need to be ready to respond to alerting and don't ever have "real" time off.

## Proposal

We should use [PagerDuty](http://pagerduty.com) to centralize our alerting in one place & manage an on-call rotation.

### Centralized Alerts

PagerDuty allows us to centralize notifications for all of our monitoring services in one place. This reduces the chance that we forget to add someone to the notification rules for a particular service.

In the case of an incident, centralizing these alerts reduces cognitive overhead by allowing us to collect & link to all the relevant alerts in one place. It'll also help us fill in our [incident template](https://github.com/DoSomething/internal/blob/master/.github/ISSUE_TEMPLATE/incident.md) for postmortems & [analyze trends](https://support.pagerduty.com/docs/reporting) over time.

### On-Call Rotation

Since we currently route notifications to the whole team, it's not clear who's responsible for handling an alert. This interrupts more people than we need to, and may make it [less likely that any one of us will respond](https://en.wikipedia.org/wiki/Bystander_effect).

To make sure all the pressure of responding to alerts doesn't rest on one person's shoulders, we'll schedule two people on call at any time – one of whom will always be a senior engineer. Tech leads will also always be available to escalate issues to in case of emergency.

While site uptime is important, we don't want the pressure of always being on-call to cause people to burn out. By using an on-call rotation, we give people time off where they don't need to worry about the pager.

We'll also define support hours (8:00am – 11:00pm EST) so we don't wake people up in the middle of the night for a false alarm. If an overnight alert doesn't resolve itself by the time responders wake up, it will automatically transition into a traditional "high priority" alert.

## Drawbacks/Alternatives

One obvious drawback to an on-call rotation is increased pressure on the people who are on-call to be available. To combat this, we're setting up a "duo" first-responder rotation (since hopefully both responders aren't trapped on the subway at the same time).

Since we already have a non-profit discount for PagerDuty, we did not consider other tools (such as [OpsGenie](https://www.opsgenie.com) or [VictorOps](https://victorops.com)). If we run into tooling limitations, we certainly could!

## References

- [PagerDuty's Incident Response Guide: Being On-Call](https://response.pagerduty.com/oncall/being_oncall/)
- [Atlassian Incident Management: On-Call](https://www.atlassian.com/incident-management/on-call)
- [Increment: Crafting Sustainable On-Call Rotations](https://increment.com/on-call/crafting-sustainable-on-call-rotations/)
