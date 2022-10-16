|            |                     |                        |                   |
|------------|---------------------|------------------------|-------------------|
| **Title**  | *Activity Streams*  | **Status**             | *InProgress*      |
| **Owner**  | *@bill_looby*       | **Additional Editors** | *@TONYCAL3* |
| **Due**    | *30-Aug-2017*       | **Customer review ?**  | *yes / no*        |

*Review link* : https://apps.na.collabserv.com/forums/html/forum?id=aa90e530-69bf-4cd9-805a-47b8909550b0&ps=25

### Table of Contents
- [Summary](#summary)
  - [Scope](#scope)
  - [Actions from this paper](#actions-from-this-paper)
  - [Timeline for these actions](#timeline-for-these-actions)
  - [What teams need to do](#what-teams-need-to-do)
  - [Customer Review](#customer-review)
  - [You may also consider](#you-may-also-consider)
  - [Content](#content)

### Summary
Activity Streams is required as the core capability for -
- Viewing a users stream (unprioritised)
- Viewing a community stream
but it is also used to provide CUD details to Activity Stream Search and all
of our initial cognitive/analytics support.

This functionality clearly needs to be in Pink (as opposed to using Blue/Green
infrastructure) in order to effect change more quickly and provide the capability
directly to Pink based content services.

Please see [Event Sourcing](../eventsourcing) for more details on the actual bus
on which we transmit events.

### Scope
This paper concentrates on the *back-end* support for Activity Streams including
activity consumption, activity storage and presentation of an Activity Stream 2.0
REST API.

### Actions from this paper
- define the future of event consumption in Pink
- create a complete backend for Activity Stream Storage
- provide an Activity Streams 2.0 REST API

### Timeline for these actions
ASAP - goal is that work will be completed in time for releasing LiveRemarks

### What teams need to do
All teams creating content in Pink need to write directly to the microservice that provides this API

### Customer Review
*if relevant*

### You may also consider
 - *using Blue/Green* . . . bleugh !


### Content

**We keep it simple**
- Simple user focussed stream management (no dynamic assembly beyond that supported by Search)
- Items that need their own stream (e.g. Communities) . . . will need their own user (Connections kind of does this, but then dynamically joins them to create a user stream)
- No email notification (can be added later if needed)
- No Gadgets (we're already committed to this anyway)
- No dynamic message creation (Connections processes events and makes the message 'Commented on your wiki page' instead of 'Commented on the wiki page' for example - we can incrementally improve, but the less of this, the better)

*Note :* Blue homepage would largely continue unaffected but all events would now be duplicated in Pink and we would not go back to Blue for any more detail. Also, Blue homepage would get no new events.

**Impact on the UX**
*What won't change*
- Blue/Green is completely unaffected (but will also not show any new content)
- The main OrientMe view (prioritised) returns the view populated by Search - this should be the same regardless (though there is also work here regardless)
*The I'm following view will change as follows*
- If you start following something, you will only get events from that point on (previously you got earlier community events after following a community)
- A delete event will not remove previous events
- If you lose access to an object, you won't lose events that you received while you had access
- Messages will not be personalised (at least not at first)
*Need to decide*
- Notifications and Action Required - do we really need these to be available for more than 30 days ?

**Implementation**
*Pushing Events*
- Events are pushed into the current Redis queue
- All current subscribers will receive these
- (There isn't time to even consider an alternative)
*Event Format*
- The format needs review (as we were reacting to Blue events up to now)
- Closer to AS2.0 ideally, but can be a superset - only the API needs the AS2.0 format, and the base json-ld context isn't needed for storage
- Blue code may need updating to use this format (depending on Update Blue, below)
*Indexing and Scoring*
- Current code is updated to recognise the new format (this is part of the whole reason we are doing OrientMe after all !)
*Retrieval from Search*
- Retrieved from our Search Index, and made available directly (conversion to AS2.0 not needed at this stage)
*Storing and Retrieval for I'm Following View*
- Event is distributed directly to all followers ('100,000 following Ginni' needs discussion, but this is still the simplest model)
- No propagation of deletes or ACL limitations
- Each persons stream is retrievable directly and immediately - no 'joins'
- Simple filters supported : by application, more . . . ?
- If a user cannot be assumed to always follow their own actions, then a separate feed will need to be managed for a users profile
*API (optional)*
- We don't 'technically' need a full AS 2.0 API support from the off as long as we can make it available via GraphQL
- However as there is an implementation in NodeJS already by James Snell, it would be a shame not to release one ->https://github.com/jasnell/activitystrea.ms
*Update Blue Push (optional)*
- We could just update the format a little, but as we're doing a format change we should probably refactor to be a Web Service of some kind.
- Direct Redis access hasn't been a success from a security/deployment standpoint.

If we don't do this
- Not only do we not currently have a way to add Pink events into the I'm following stream we cannot currently add compliance for any Pink content !
- Without direct support in Pink . . .
- We will be pushing events for new content into Blue/Green . . .  to be pushed back into pink !
- Not Just Live Remarks . . . . any new Pink content !
- With corresponding limitations in scale/performance etc.
- And likely several defect resolutions in Blue to get the events returned as we need
- And then when we do develop in Pink . . . we have migration !

Also - Other AS2.0 features we need coming hot on the heels of the above
- Allow rich representation of referred content : OpenGraph or Schema.com
- Action Links support (dropped by Facebook, but we still want it)
