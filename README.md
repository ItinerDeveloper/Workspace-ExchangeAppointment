# Exchange Appointment Service

## Service Route
`/exchangeappointment/import/exchangeappointment`

## Description
The Exchange Appointment service allows the Itiner Workspace to call a configured Exchange server via a webhook set up within a workflow. It creates a calendar event on behalf of a configured user based on variables prefixed with "calendarEvent" in the workflow. The event is then sent to the addresses specified in the `calendarEventAttendees` variable, separated by semicolons.

## Dedicated Template Variables

### Required Variables
- **calendarEventSummary**: The summary or title of the calendar event.
- **calendarEventStartDate**: The start date and time of the event.
- **calendarEventEndDate**: The end date and time of the event.
- **calendarEventAttendees**: The attendees for the event. Multiple addresses should be separated by semicolons.
- **calendarEventOrganizer**: The organizer of the event.

### Optional Variables
- **calendarEventDescription**: The description of the event.
- **calendarEventTimeZone**: The time zone of the event. If not provided, the default is UTC. See [IANA Timezones](https://timeapi.io/documentation/iana-timezones) for the value set.
- **calendarEventLocation**: The location of the event.
- **calendarEventOptionalAttendees**: The optional attendees for the event. Multiple addresses should be separated by semicolons.

---

This service is part of the Itiner Workspace integrations and is designed to facilitate scheduling and managing calendar events by interfacing with an Exchange server.
