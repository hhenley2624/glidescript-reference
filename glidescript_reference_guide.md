# GlideSystem (gs) Complete Reference Guide
**ServiceNow Server-Side Scoped API**

---

## Table of Contents
- [User Information](#user-information)
- [Session Management](#session-management)
- [Logging & Debugging](#logging--debugging)
- [Messages & UI Feedback](#messages--ui-feedback)
- [Date & Time Methods](#date--time-methods)
- [System Properties](#system-properties)
- [Event Management](#event-management)
- [Security & Roles](#security--roles)
- [Utility Methods](#utility-methods)
- [Common Use Cases](#common-use-cases)

---

## User Information

### gs.getUser()
Returns a reference to the scoped GlideUser object for the current user.

```javascript
var currentUser = gs.getUser();
gs.info(currentUser.getDisplayName());
```

**Use Case:** Access user properties, check roles, get user details
```javascript
var user = gs.getUser();
if (user.hasRole('itil')) {
    // User has ITIL role
}
```

---

### gs.getUserID()
Gets the sys_id of the current user.

```javascript
var userId = gs.getUserID();
// Returns: 6816f79cc0a8016401c5a33be04be441
```

**Use Case:** Query records assigned to current user
```javascript
var gr = new GlideRecord('incident');
gr.addQuery('assigned_to', gs.getUserID());
gr.query();
```

---

### gs.getUserName()
Returns the user name (login) of the current user.

```javascript
var userName = gs.getUserName();
// Returns: "abel.tuter" (not "Abel Tuter")
```

**Use Case:** Logging or audit trails
```javascript
gs.info("Action performed by: " + gs.getUserName());
```

---

### gs.getUserDisplayName()
Gets the display name of the current user.

```javascript
var displayName = gs.getUserDisplayName();
// Returns: "Abel Tuter" (not "abel.tuter")
```

**Use Case:** User-facing messages
```javascript
gs.addInfoMessage("Welcome, " + gs.getUserDisplayName());
```

---

## Session Management

### gs.getSession()
Gets a reference to the current Glide session.

```javascript
var session = gs.getSession();
```

**Use Case:** Check if session is interactive
```javascript
if (gs.getSession().isInteractive()) {
    // User is logged in via UI, not API/scheduled job
}
```

---

### gs.getSessionID()
Retrieves the GlideSession session ID.

```javascript
var sessionId = gs.getSessionID();
// Returns: A0D4E5416F3F21005BE8883E6B3EE4B8
```

**Use Case:** Session tracking or debugging
```javascript
gs.info("Current session: " + gs.getSessionID());
```

---

### gs.isInteractive()
Checks if the current session is interactive (user logged in) vs non-interactive (SOAP, scheduled job).

```javascript
if (gs.isInteractive()) {
    // User is actively logged in
} else {
    // Running as scheduled job or API call
}
```

**Use Case:** Apply different logic for UI vs background processes
```javascript
if (!gs.hasRole("admin") && gs.isInteractive()) {
    current.addQuery("active", "true");
}
```

---

### gs.isLoggedIn()
Determines if the current user is currently logged in.

```javascript
if (gs.isLoggedIn()) {
    gs.info("User is authenticated");
}
```

---

### gs.isMobile()
Determines if a request comes from a mobile device.

```javascript
if (gs.isMobile()) {
    gs.info("Request from mobile UI");
} else {
    gs.info("Request from desktop UI");
}
```

**Use Case:** UI actions or business rules that behave differently on mobile
```javascript
if (gs.isMobile()) {
    // Simplified mobile workflow
}
```

---

## Logging & Debugging

### gs.info(String message, [params])
Writes an info message to the system log. Supports up to 5 parameters.

```javascript
gs.info("This is an info message");

// With parameters
var firstName = "Abel";
var lastName = "Tuter";
gs.info("User: {0} {1}", firstName, lastName);
// Output: User: Abel Tuter
```

**Use Case:** Standard logging for workflows
```javascript
gs.info("Processing record: {0}", current.number);
```

---

### gs.error(String message, [params])
Writes an error message to the system log.

```javascript
gs.error("This is an error message");

// With parameters
gs.error("Failed to process {0}: {1}", current.number, errorMessage);
```

**Use Case:** Error handling
```javascript
try {
    // Some operation
} catch (e) {
    gs.error("Operation failed: {0}", e.message);
}
```

---

### gs.warn(String message, [params])
Writes a warning message to the system log.

```javascript
gs.warn("This is a warning");

var firstName = "Abel";
gs.warn("Warning from {0}", firstName);
```

**Use Case:** Non-critical issues that need attention
```javascript
if (current.priority == '5' && current.assignment_group.nil()) {
    gs.warn("Critical incident {0} has no assignment group", current.number);
}
```

---

### gs.debug(String message, [params])
Writes a debug message to the system log (only if debugging is enabled).

```javascript
gs.debug("Debug message");

var value = "test";
gs.debug("Variable value: {0}", value);
```

**Use Case:** Detailed troubleshooting without cluttering production logs
```javascript
gs.debug("Entering function with params: {0}, {1}", param1, param2);
```

---

### gs.isDebugging()
Determines if debugging is active for the current scope.

```javascript
if (gs.isDebugging()) {
    // Add extra logging
    gs.debug("Detailed debug information");
}
```

---

## Messages & UI Feedback

### gs.addInfoMessage(String message)
Adds an info message for the current session (appears at top of form).

```javascript
gs.addInfoMessage("Record updated successfully");
```

**Use Case:** User feedback on form submissions
```javascript
if (current.state == '6') {
    gs.addInfoMessage("Incident has been resolved");
}
```

⚠️ **Note:** Not supported for asynchronous business rules

---

### gs.addErrorMessage(String message)
Adds an error message for the current session (appears at top of form in red).

```javascript
gs.addErrorMessage("Password does not meet requirements");
```

**Use Case:** Validation errors
```javascript
if (current.u_date1 > current.u_date2) {
    gs.addErrorMessage("Start date must be before end date");
    current.setAbortAction(true);
}
```

---

### gs.getErrorMessages()
Returns error messages added by addErrorMessage() for the session.

```javascript
var errors = gs.getErrorMessages();
// Returns: Array of error message strings
```

---

### gs.getMessage(String id, [Array args])
Retrieves translated messages from the Message [sys_ui_message] table.

```javascript
var msg = gs.getMessage("rows will not be updated");
// Returns translated message based on user's language

// With variables
var msg = gs.getMessage("Abort adding action '{0}'", current.action.name);
```

**Use Case:** Multi-language support
```javascript
var welcomeMsg = gs.getMessage("welcome_message");
gs.addInfoMessage(welcomeMsg);
```

---

### gs.getEscapedMessage(String id, [Array args])
Same as getMessage() but replaces HTML special characters with HTML codes.

```javascript
var msg = gs.getEscapedMessage("Is the summary & details accurate?");
// Returns: "Is the summary &amp; details accurate?"
```

**Use Case:** Display messages with special characters in HTML contexts
```javascript
var msg = gs.getEscapedMessage("Value is <{0}>", someValue);
```

---

## Date & Time Methods

### Current Time
```javascript
// Get current date/time
var now = new GlideDateTime();
```

---

### gs.daysAgo(Number days)
Returns date and time for specified number of days ago (or future if negative).

```javascript
var fiveDaysAgo = gs.daysAgo(5);
// Returns: 2024-01-15 14:30:00 (if today is 2024-01-20 14:30:00)

var threeDaysFromNow = gs.daysAgo(-3);
// Returns: 2024-01-23 14:30:00
```

**Use Case:** Query records from last X days
```javascript
var gr = new GlideRecord('incident');
gr.addQuery('sys_created_on', '>', gs.daysAgo(7));
gr.query();
// All incidents created in last 7 days
```

**Use Case:** Set future due dates
```javascript
if (current.urgency == '1') {
    current.due_date = gs.daysAgo(-2); // Due in 2 days
}
```

---

### gs.daysAgoStart(Number days)
Returns beginning of the day X days ago (00:00:00).

```javascript
var startOfFiveDaysAgo = gs.daysAgoStart(5);
// Returns: 2024-01-15 00:00:00
```

**Use Case:** Query all records from a specific day
```javascript
var gr = new GlideRecord('sysapproval_approver');
gr.addQuery('state', 'requested');
gr.addQuery('sys_updated_on', '<', gs.daysAgoStart(5));
gr.query();
```

---

### gs.daysAgoEnd(Number days)
Returns end of the day X days ago (23:59:59).

```javascript
var endOfFiveDaysAgo = gs.daysAgoEnd(5);
// Returns: 2024-01-15 23:59:59
```

---

### gs.hoursAgo(Number hours)
Returns date and time for specified number of hours ago.

```javascript
var fourHoursAgo = gs.hoursAgo(4);

// Future time (negative)
var twoHoursFromNow = gs.hoursAgo(-2);
```

**Use Case:** Set SLA due dates
```javascript
if (current.urgency == '1') {
    current.due_date = gs.hoursAgo(-4); // Due in 4 hours
}
```

---

### gs.hoursAgoStart(Number hours)
Returns start of the hour X hours ago.

```javascript
var startOfHour = gs.hoursAgoStart(2);
// If current time is 14:45:30
// Returns: 12:00:00
```

---

### gs.hoursAgoEnd(Number hours)
Returns end of the hour X hours ago.

```javascript
var endOfHour = gs.hoursAgoEnd(2);
// If current time is 14:45:30
// Returns: 12:59:59
```

---

### gs.minutesAgoStart(Number minutes)
Returns start of the minute X minutes ago.

```javascript
var thirtyMinutesAgo = gs.minutesAgoStart(30);
// If current time is 14:56:45
// Returns: 14:26:00
```

---

### gs.minutesAgoEnd(Number minutes)
Returns end of the minute X minutes ago.

```javascript
var thirtyMinutesAgo = gs.minutesAgoEnd(30);
// If current time is 14:56:45
// Returns: 14:26:59
```

---

### Week Methods

```javascript
// This week
gs.beginningOfThisWeek()  // Returns: 2024-01-14 00:00:00 (Sunday)
gs.endOfThisWeek()        // Returns: 2024-01-20 23:59:59 (Saturday)

// Last week
gs.beginningOfLastWeek()  // Returns: 2024-01-07 00:00:00
gs.endOfLastWeek()        // Returns: 2024-01-13 23:59:59

// Next week
gs.beginningOfNextWeek()  // Returns: 2024-01-21 00:00:00
gs.endOfNextWeek()        // Returns: 2024-01-27 23:59:59
```

---

### Month Methods

```javascript
// This month
gs.beginningOfThisMonth() // Returns: 2024-01-01 00:00:00
gs.endOfThisMonth()       // Returns: 2024-01-31 23:59:59

// Last month
gs.beginningOfLastMonth() // Returns: 2023-12-01 00:00:00
gs.endOfLastMonth()       // Returns: 2023-12-31 23:59:59

// Next month
gs.beginningOfNextMonth() // Returns: 2024-02-01 00:00:00
gs.endOfNextMonth()       // Returns: 2024-02-29 23:59:59 (2024 is leap year)
```

**Use Case:** Monthly reports
```javascript
var gr = new GlideRecord('incident');
gr.addQuery('sys_created_on', '>=', gs.beginningOfThisMonth());
gr.addQuery('sys_created_on', '<=', gs.endOfThisMonth());
gr.query();
gs.info("Incidents this month: " + gr.getRowCount());
```

---

### gs.monthsAgo(Number months)
Returns date and time X months ago at same time.

```javascript
var oneMonthAgo = gs.monthsAgo(1);
// If now: 2024-01-20 15:30:00
// Returns: 2023-12-20 15:30:00
```

---

### gs.monthsAgoStart(Number months)
Returns start of the month X months ago.

```javascript
var threeMonthsAgoStart = gs.monthsAgoStart(3);
// If now: 2024-01-20 15:30:00
// Returns: 2023-10-01 00:00:00
```

---

### gs.monthsAgoEnd(Number months)
Returns end of the month X months ago.

```javascript
var twoMonthsAgoEnd = gs.monthsAgoEnd(2);
// If now: 2024-01-20 15:30:00
// Returns: 2023-11-30 23:59:59
```

---

### Quarter Methods

```javascript
// This quarter
gs.beginningOfThisQuarter()  // Returns: 2024-01-01 00:00:00 (Q1)
gs.endOfThisQuarter()        // Returns: 2024-03-31 23:59:59

// Quarters ago
gs.quartersAgoStart(1)       // Returns: 2023-10-01 00:00:00 (last quarter start)
gs.quartersAgoEnd(1)         // Returns: 2023-12-31 23:59:59 (last quarter end)
```

---

### Year Methods

```javascript
// This year
gs.beginningOfThisYear()  // Returns: 2024-01-01 00:00:00
gs.endOfThisYear()        // Returns: 2024-12-31 23:59:59

// Last year
gs.endOfLastYear()        // Returns: 2023-12-31 23:59:59

// Next year
gs.beginningOfNextYear()  // Returns: 2025-01-01 00:00:00
gs.endOfNextYear()        // Returns: 2025-12-31 23:59:59

// Years ago
gs.yearsAgo(2)            // Returns: 2022-01-20 15:30:00 (2 years ago, same time)
```

---

### gs.yesterday()
Returns yesterday's time (24 hours ago).

```javascript
var yesterday = gs.yesterday();
// If now: 2024-01-20 15:30:00
// Returns: 2024-01-19 15:30:00
```

---

### gs.dateGenerate(String date, String range)
Generates date and time for specified date in GMT.

```javascript
// Start of day
var startDate = gs.dateGenerate('2024-01-15', 'start');
// Returns: 2024-01-15 00:00:00

// End of day
var endDate = gs.dateGenerate('2024-01-15', 'end');
// Returns: 2024-01-15 23:59:59

// Specific time
var specificTime = gs.dateGenerate('2024-01-15', '14:30:00');
// Returns: 2024-01-15 14:30:00
```

**Use Case:** Query records for specific date
```javascript
var gr = new GlideRecord('incident');
gr.addEncodedQuery("sys_created_onBETWEENjavascript:gs.dateGenerate('2024-01-07','start')@javascript:gs.dateGenerate('2024-01-07','end')");
gr.query();
```

---

### gs.getTimeFormat()
Returns the time format for the current user.

```javascript
var timeFormat = gs.getTimeFormat();
// Returns: "HH:mm:ss" or "hh:mm:ss a" depending on user preference
```

---

### gs.getTimeZoneName()
Returns the time zone name for the current user.

```javascript
var timeZone = gs.getTimeZoneName();
// Returns: "America/Los_Angeles"
```

⚠️ **Deprecated:** Use `gs.getSession().getTimeZoneName()` instead

---

## System Properties

### gs.getProperty(String key, [Object alt])
Gets the value of a Glide property.

```javascript
// Get property
var servletURI = gs.getProperty('glide.servlet.uri');
// Returns: "https://instance.service-now.com/"

// With default/alternate value
var customProp = gs.getProperty('custom.property', 'default_value');
```

**Use Case:** Configuration-driven behavior
```javascript
var maxRetries = gs.getProperty('x_app.max_retries', '3');
for (var i = 0; i < parseInt(maxRetries); i++) {
    // Retry logic
}
```

---

### gs.setProperty(String key, String value, [String description])
Sets a system property value.

```javascript
gs.setProperty("glide.custom.foo", "bar", "Custom property description");

// Verify
gs.info(gs.getProperty("glide.custom.foo"));
// Output: bar
```

⚠️ **Warning:** Causes system-wide cache flush. Use sparingly! Don't use for frequently changing values.

**Use Case:** One-time configuration setup
```javascript
// During app installation
gs.setProperty("x_app.api_endpoint", "https://api.example.com", "External API endpoint");
```

---

## Event Management

### gs.eventQueue(String name, Object instance, [String parm1], [String parm2], [String queue])
Queues an event for the event manager.

```javascript
// Simple event
gs.eventQueue('incident.commented', current, gs.getUserID(), gs.getUserName());

// With custom queue
gs.eventQueue('custom.event', current, 'param1', 'param2', 'custom_queue');
```

**Use Case:** Trigger notifications or workflows
```javascript
if (current.operation() != 'insert' && current.comments.changes()) {
    gs.eventQueue('incident.commented', current, gs.getUserID(), gs.getUserName());
}
```

---

### gs.eventQueueScheduled(String name, Object instance, [String parm1], [String parm2], [Object expiration])
Queues an event with scheduled execution time.

```javascript
// Schedule event 7 days from now
var processTime = new GlideDateTime();
processTime.addDaysLocalTime(7);
gs.eventQueueScheduled('sn_app.user.deactivate', current, requestXML, gs.getUserID(), processTime);

// With sys_id instead of GlideRecord
gs.eventQueueScheduled('event.test', '0e29421383101000dada83ec37d9292d', '', '', '');
```

**Use Case:** Delayed actions or reminders
```javascript
// Send reminder 1 day before due date
var reminderTime = new GlideDateTime(current.due_date);
reminderTime.addDaysLocalTime(-1);
gs.eventQueueScheduled('task.reminder', current, '', '', reminderTime);
```

---

### gs.executeNow(GlideRecord job)
Executes a scheduled job immediately.

```javascript
var jobId = '61847fe04c603300fa9bb64c2b491dac';
var gr = new GlideRecord('sysauto_script');
if (gr.get(jobId)) {
    gs.executeNow(gr);
}
```

⚠️ **Scope Limitation:** Can only execute jobs in the same application scope.

**Use Case:** Trigger scheduled job on-demand
```javascript
// Trigger import job immediately
var gr = new GlideRecord('sysauto_script');
if (gr.get('import_job_sys_id')) {
    var jobSysId = gs.executeNow(gr);
    gs.info("Started job: " + jobSysId);
}
```

---

## Security & Roles

### gs.hasRole(Object role)
Determines if current user has specified role.

```javascript
if (gs.hasRole("admin")) {
    // User is admin
}

if (gs.hasRole("itil")) {
    // User has ITIL role
}
```

⚠️ **Note:** Always returns `true` for users with administrator role.

**Use Case:** Conditional logic based on role
```javascript
if (!gs.hasRole("admin") && !gs.hasRole("groups_admin") && gs.isInteractive()) {
    var qc = current.addQuery("u_hidden", "!=", "true");
    qc.addOrCondition("sys_id", "javascript:getMyGroups()");
}
```

**Use Case:** Business rule with role check
```javascript
if (gs.hasRole("approval_admin")) {
    // Skip approval for approval admins
    current.approval = 'approved';
}
```

---

## Utility Methods

### gs.nil(Object o)
Checks if object is null, undefined, or empty string.

```javascript
if (gs.nil(current.assignment_group)) {
    gs.addErrorMessage("Assignment group is required");
}

// Returns true for:
// - null
// - undefined
// - ""
// Returns false for everything else
```

**Use Case:** Field validation
```javascript
if (gs.nil(current.short_description)) {
    gs.addErrorMessage("Short description cannot be empty");
    current.setAbortAction(true);
}
```

---

### gs.generateGUID()
Generates a unique 32-character hexadecimal GUID.

```javascript
var guid = gs.generateGUID();
// Returns: af770511ff013100e04bfffffffffff6
```

**Use Case:** Generate unique identifiers
```javascript
var uniqueId = gs.generateGUID();
current.u_external_id = uniqueId;
```

---

### gs.include(String name)
Safely includes a script include.

```javascript
gs.include("LDAPUtils");
var ldapUtils = new LDAPUtils();
```

**Use Case:** Use shared library code
```javascript
gs.include("MyCustomUtils");
var utils = new MyCustomUtils();
var result = utils.processData(current);
```

---

### gs.tableExists(String name)
Checks if a database table exists.

```javascript
if (gs.tableExists("incident")) {
    gs.info("Incident table exists");
}

if (!gs.tableExists("custom_table")) {
    gs.error("Custom table does not exist!");
}
```

**Use Case:** Safe table operations
```javascript
if (gs.tableExists("u_custom_app_table")) {
    var gr = new GlideRecord("u_custom_app_table");
    gr.query();
}
```

---

### gs.base64Encode(String source)
Encodes string to base64.

```javascript
var key = "sample_key";
var encodedKey = gs.base64Encode(key);
// Returns: c2FtcGxlX2tleQ==
```

**Use Case:** Encode credentials or sensitive data
```javascript
var apiKey = gs.base64Encode(current.u_api_key);
```

---

### gs.base64Decode(String source)
Decodes base64 string to ASCII.

```javascript
var encoded = "c2FtcGxlX2tleQ==";
var decoded = gs.base64Decode(encoded);
// Returns: sample_key
```

---

### gs.urlEncode(String url)
Encodes URL with special characters.

```javascript
var url = "https://example.com/search?q=hello world&lang=en";
var encoded = gs.urlEncode(url);
// Returns: https%3A%2F%2Fexample.com%2Fsearch%3Fq%3Dhello+world%26lang%3Den
```

---

### gs.urlDecode(String url)
Decodes URL-encoded string.

```javascript
var encoded = "https%3A%2F%2Fexample.com%2Fsearch%3Fq%3Dhello+world";
var decoded = gs.urlDecode(encoded);
// Returns: https://example.com/search?q=hello world
```

---

### gs.xmlToJSON(String xmlString)
Converts XML string to JSON object.

```javascript
var xmlString = '<root><name>Test</name><value>123</value></root>';
var jsonObject = gs.xmlToJSON(xmlString);
```

**Use Case:** Parse XML API responses
```javascript
var response = soapResponse.getBody();
var jsonData = gs.xmlToJSON(response);
gs.info("Parsed data: " + JSON.stringify(jsonData));
```

---

### gs.getUrlOnStack()
Gets the current URI for the session.

```javascript
var currentUrl = gs.getUrlOnStack();
gs.info(currentUrl);
```

---

### gs.setRedirect(Object o)
Sets redirect URI for the transaction.

```javascript
gs.setRedirect("incident_list.do");

// With parameters
gs.setRedirect("com.glideapp.servicecatalog_cat_item_view.do?sysparm_id=" + current.sys_id);
```

**Use Case:** Redirect after form submission
```javascript
// After creating record, redirect to list view
gs.setRedirect("incident_list.do?sysparm_query=assigned_to=" + gs.getUserID());
```

---

### gs.getCssCacheVersionString()
Gets cache version string for CSS files.

```javascript
var cssVersion = gs.getCssCacheVersionString();
// Returns: _d82979516f0171005be8883e6b3ee4cf&theme=
```

---

### gs.getCurrentApplicationId()
Gets sys_id of current application from Application Picker.

```javascript
var appId = gs.getCurrentApplicationId();
// Returns: 04936cb16f30b1005be8883e6b3ee4e0
// or "global" if none selected
```

---

### gs.getCurrentScopeName()
Gets name of current scope.

```javascript
var scopeName = gs.getCurrentScopeName();
// Returns: "x_acme_my_app" or "global"
```

**Use Case:** Scope-aware logic
```javascript
if (gs.getCurrentScopeName() == 'global') {
    gs.warn("Running in global scope");
}
```

---

### gs.getCallerScopeName()
Gets the caller scope name.

```javascript
var callerScope = gs.getCallerScopeName();
// Returns scope name of calling code, or null
```

**Use Case:** Cross-scope debugging
```javascript
gs.info("Called from scope: " + gs.getCallerScopeName());
```

---

## Common Use Cases

### Use Case 1: Query Records from Last 7 Days
```javascript
var gr = new GlideRecord('incident');
gr.addQuery('sys_created_on', '>', gs.daysAgo(7));
gr.query();

while (gr.next()) {
    gs.info("Incident: " + gr.number);
}
```

---

### Use Case 2: Set Due Date Based on Priority
```javascript
// In a business rule on incident insert
if (current.operation() == 'insert' && current.due_date == '') {
    if (current.priority == '1') {
        current.due_date = gs.hoursAgo(-2); // Due in 2 hours
    } else if (current.priority == '2') {
        current.due_date = gs.hoursAgo(-4); // Due in 4 hours
    } else {
        current.due_date = gs.daysAgo(-2); // Due in 2 days
    }
}
```

---

### Use Case 3: Role-Based Field Access
```javascript
// In business rule or ACL
if (!gs.hasRole('admin') && !gs.hasRole('manager')) {
    // Hide sensitive fields for non-admins/non-managers
    current.u_salary.setDisplayValue('');
    current.u_ssn.setDisplayValue('');
}
```

---

### Use Case 4: Validate Date Range
```javascript
// In business rule or client script
if (!gs.nil(current.start_date) && !gs.nil(current.end_date)) {
    var start = current.start_date.getGlideObject().getNumericValue();
    var end = current.end_date.getGlideObject().getNumericValue();
    
    if (start > end) {
        gs.addErrorMessage('Start date must be before end date');
        current.start_date.setError('Invalid date range');
        current.setAbortAction(true);
    }
}
```

---

### Use Case 5: Conditional Logging
```javascript
// Only log detailed info when debugging
if (gs.isDebugging()) {
    gs.debug("Processing record: {0}", current.sys_id);
    gs.debug("User: {0}, Role: admin={1}", gs.getUserName(), gs.hasRole('admin'));
}

// Always log errors
if (errorOccurred) {
    gs.error("Failed to process {0}: {1}", current.number, errorMessage);
}
```

---

### Use Case 6: Monthly Reporting Query
```javascript
// Get all incidents created this month
var gr = new GlideRecord('incident');
gr.addQuery('sys_created_on', '>=', gs.beginningOfThisMonth());
gr.addQuery('sys_created_on', '<=', gs.endOfThisMonth());
gr.addQuery('state', '!=', '7'); // Exclude closed
gr.query();

gs.info("Active incidents this month: " + gr.getRowCount());
```

---

### Use Case 7: Scheduled Event with User Notification
```javascript
// Schedule notification 1 day before task due date
if (current.operation() == 'insert' && !gs.nil(current.due_date)) {
    var reminderTime = new GlideDateTime(current.due_date);
    reminderTime.addDaysLocalTime(-1);
    
    gs.eventQueueScheduled(
        'task.due_reminder',
        current,
        current.assigned_to.toString(),
        current.number.toString(),
        reminderTime
    );
}
```

---

### Use Case 8: Safe Table Query
```javascript
// Check table exists before querying
var tableName = 'u_custom_table';

if (gs.tableExists(tableName)) {
    var gr = new GlideRecord(tableName);
    gr.query();
    gs.info("Records found: " + gr.getRowCount());
} else {
    gs.error("Table {0} does not exist", tableName);
}
```

---

### Use Case 9: User-Specific Record Filter
```javascript
// In business rule: Show only user's records unless admin
if (!gs.hasRole('admin') && gs.isInteractive()) {
    var userId = gs.getUserID();
    current.addQuery('opened_by', userId);
    current.addOrCondition('assigned_to', userId);
}
```

---

### Use Case 10: Dynamic Property Configuration
```javascript
// Get configurable retry count from system property
var maxRetries = parseInt(gs.getProperty('x_app.max_retries', '3'));
var retryDelay = parseInt(gs.getProperty('x_app.retry_delay_seconds', '5'));

for (var i = 0; i < maxRetries; i++) {
    try {
        // Attempt operation
        break;
    } catch (e) {
        gs.warn("Retry {0}/{1} failed: {2}", i+1, maxRetries, e.message);
        gs.sleep(retryDelay * 1000);
    }
}
```

---

### Use Case 11: Automated Workflow Trigger
```javascript
// Trigger workflow when SLA breaches
if (current.sla_due < new GlideDateTime() && current.state != '6') {
    gs.eventQueue('sla.breach', current, current.number.toString(), current.assigned_to.toString());
    
    gs.addInfoMessage("SLA has been breached - escalation triggered");
}
```

---

### Use Case 12: Audit Trail with User Context
```javascript
// Create audit log entry
var auditGR = new GlideRecord('u_audit_log');
auditGR.initialize();
auditGR.u_user = gs.getUserID();
auditGR.u_user_name = gs.getUserName();
auditGR.u_action = 'UPDATE';
auditGR.u_table = current.getTableName();
auditGR.u_record = current.sys_id.toString();
auditGR.u_timestamp = new GlideDateTime();
auditGR.u_session_id = gs.getSessionID();
auditGR.insert();
```

---

## Quick Reference Cheat Sheet

### Get User Info
```javascript
gs.getUser()                // GlideUser object
gs.getUserID()              // sys_id
gs.getUserName()            // Login name (abel.tuter)
gs.getUserDisplayName()     // Display name (Abel Tuter)
```

### Logging
```javascript
gs.info(msg, p1, p2, ...)   // Info level
gs.warn(msg, p1, p2, ...)   // Warning level
gs.error(msg, p1, p2, ...)  // Error level
gs.debug(msg, p1, p2, ...)  // Debug level (if enabled)
```

### UI Messages
```javascript
gs.addInfoMessage(msg)      // Blue info message
gs.addErrorMessage(msg)     // Red error message
gs.getMessage(id, args)     // Get translated message
```

### Date Shortcuts
```javascript
gs.daysAgo(n)               // n days ago (negative = future)
gs.hoursAgo(n)              // n hours ago
gs.beginningOfThisMonth()   // Start of month
gs.endOfThisMonth()         // End of month
gs.yesterday()              // 24 hours ago
```

### Security
```javascript
gs.hasRole(role)            // Check user role
gs.isInteractive()          // UI session vs background
gs.isLoggedIn()             // User authenticated
```

### Utilities
```javascript
gs.nil(obj)                 // Check null/undefined/empty
gs.getProperty(key, alt)    // Get system property
gs.tableExists(name)        // Check table exists
gs.generateGUID()           // Generate unique ID
```

---

## Important Notes

1. **Scope Awareness**: Many methods behave differently in scoped vs global applications
2. **Date/Time in GMT**: All date methods return values in GMT format
3. **Cache Flushing**: `setProperty()` causes system-wide cache flush - use sparingly
4. **Parameter Formatting**: Use `{0}`, `{1}`, etc. for variable substitution in log messages
5. **Async Business Rules**: Some methods like `addInfoMessage()` don't work in async rules
6. **Role Checking**: `hasRole('admin')` always returns true for administrators
7. **Null Checking**: Use `gs.nil()` instead of `== null` or `== ''` for reliable null checks

---

**End of GlideSystem Reference Guide**
