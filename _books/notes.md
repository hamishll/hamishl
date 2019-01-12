---
title: Blue Prism Professional Developer - Exam Notes
---

# Best Practice

1. Objects
	- Naming convention: *{App Name} - {Screen Name}*
	- You can never have too many objects, only too few.  Advantages:
		- App Modeller is smaller and less prone to error
		- A running process consumes only the actions it requires
		- Many developers can work concurrently
		- Changes to object layer have less downstream risk
	- Stage design:
		- Always begin with element wait stage, tHis should always throw exception on timeout
		- Perform action
		- Wait for change; don't use arbitrary waits
		- Do not call published actions within the action
		- Do not make business decisions in the object
		- Provide descriptions to inputs/outputs, this improves quality of the automatically generated documentation
		- Store globals on the **Initialise** page
		- App Model naming convention: *{Element Type} - {Element Name}
		- Delete customer confidential, and environment specific attributes
	- Exposure
		- background: multiple instances of that object can run
		- foreground: only one instance of that object can run, and can run at same time as other background objects
		- exclusive: only this instance of object can run, and no others

2. Processes
	- Naming convention: *01 - Create Queue* (number if called chronologically
	- Page Design:
		- use sub-pages, with retry loops
		- add Stop? decision between queue items
		- have a main page exception block
		- check for concurrent exceptions (++count if same as previous)
		- use environment variables and work queue tags
		- *use the process templates!*



# Java Automation

1. Getting started
	- JAB is required to access Java's Accessibility API
	- You may need to launch java apps from command line and attach after
	- Restarting Java apps, and restarting the resource PC may reduce errors as this resets Java's leaky memory issues

2. Application Wizard parameters
	- **descendtree** makes BP search the whole application model, not just visible elements - *use if spied elements are causing crashes*
	- **ignorenotshowing** makes BP search only for visible elements
	- enter these in plain text, with a comma between **descendtree,ignorenotshowing**

3. Interfacing
	- Wait stages require the app model to be traversed which is resource-hungry. Avoid:
		- Overly-long waits, as they will be continually checking for element
		- Multiple-condition waits
		- Spy pop-ups in Win32 instead
	- Application Navigator can be used to view the whole model

4. App Model Attributes
	- **Match Index**: matches the Nth instance of an element
	- **AncestorCount**: search of tree stops when about to descend deeper than the ancestor count
	- **Showing**: element and all its parents are visible (although may be occluded)
	- *The following can significantly improve performance:
		- descendtree
		- match index
		- match reverse
		- ancestorcount*


# Web Services

1. Objects and Processes can be exposed as web services using WSDL
	- can be encrpyted using certificates
	- a proxy can route requests for you to different machines

2. Accessible from http://[machinename]:<port>/ws/

3. BP can also consume web services through SOAP




# Mainframe Automation

1. You must specify a session identifier when starting or attaching to a mainframe session

2. It's not always possible to tell how long a field is, so spy to the right of the screen and trim later

3. IBM keywords: [enter], [delete], [pf1]

4. Powerterm Attatchmate: @E, @D, @1


# Exception Handling

1. General:
	- Types: system, business, (validation, login, system unavailable)
	- Use a multi-calc after recover to store ExceptionType() and ExceptionDetail() to a data item
	- Minimal exception handling should be in the object layer
	- Use Retry Limits (be careful about ending up with 3x3x3 retries if you do this on object, sub-page, and page layers -- you could use an input parameter to the page to dictate number of retries)
	- **if an exception isn't recovered before another is thrown, this next exception will be unrecoveable - always recover-resume**

2. Preserving details
	- **when checked**, the exception **'re-releases'** the exception *as though it had never been recovered*
	- **when unchecked**, a new exception is generated

3. Business Objects
	- (apparently) if an attach fails it can be reasonably assumed that an application isn't running
	- you could use a multi-condition wait to determine whether an exception is a system issue, data validation or privilege-based - rather than just throwing a generic exception, we could pass as an output data item

4. Queue Items
	- the Mark Exception action has inputs **Retry** and **Keep Locked**, which can be used to retry cases


# Work Queues

1. Syntax:
	- +  -  ?  *  ;

2. Status:
	- can be set when using *Add to Queue*
	- can be updated with the action *Update Status*
	- can be retrieved as output to *Get Next Item*

3. Priority:
	- can be set when using *Add to Queue*
	- can be updated with the action *Set Priority*
	- Priority **1** is highest

4. Work queues are **not encrypted by default**, but can be encrypted

5. Deferring or Multiple Queues?
	- Defer if separation period is just a few minutes
	- Use second queue if separation period is longer (days etc)

6. Parent/child queues
	- you need a **relationship key** (parent's ID) on each child

7 Loading from a workflow system, use env locks to either:
	- lock and load all cases
	- lock an individual item and load it