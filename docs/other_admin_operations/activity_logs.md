# Activity Logs

**Activity Logs** is a chronological audit trail of actions performed by end users (organization owners and their team members) across the platform — separate from admin-performed actions, which are tracked in [Audit Logs](audit_logs.md).

## Accessing Activity Logs

1. Log in to the admin console.
2. From the left navigation menu, under **Audit**, click **Activity Logs**.

Each row shows: **ID**, **User**, **Organization**, **Module**, **Action**, **Performed By**, and **Date and time**. Use the search box to filter entries, and the pagination controls at the bottom to page through results.

![admin-activity-logs-list.png](../images/admin-activity-logs-list.png)

## Viewing a Change

Click the **eye icon** in the **View Changes** column on any row to see exactly what changed:

- **Browser Agent** and **IP Address** the action was performed from.
- **Previous** – the record's state before the action.
- **Current** – the record's state after the action.
- **Action** - what action was performed.
- **Module** - In which module the change was occured.
- **Trace ID** - The Id that can be used to trace the activity.
- **Date & Time** - Shows the date and time of the action.

![admin-activity-logs-view-changes.png](../images/admin-activity-logs-view-changes.png)

### Consent at sign-up

One row type carries an extra panel. **Terms and Privacy Policy Accepted** records that a person
accepted the Terms of Service and Privacy Policy when their account was created, and its details
panel shows the time they accepted, which screen collected it, and the marks identifying the exact
wording they were shown. See
[Consent Records](../compliance/consent_records.md#4-terms-acceptance-at-account-creation).

![admin-activity-logs-consent.png](../images/admin-activity-logs-consent.png)

!!! note ""
    **Previous** and **Current** are not shown on a consent row. It records something that happened
    rather than something that changed, so there is no before and after — the panel is the whole
    record. Every other row type is unchanged.

Click **Close** to return to the log list.
