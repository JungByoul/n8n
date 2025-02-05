#n8n 공식문서 참고함
https://docs.n8n.io/try-it-out/longer-introduction/

https://datapopcorn.notion.site/NASA-1272a185e5ce80f48ad5f48551ed6a18



<!-- A slightly longer introduction#
This guide shows you how to automate a task using a workflow in n8n, explaining key concepts along the way. You will:

Create a workflow from scratch. You'll build a workflow that runs every week to get data from NASA, filters the data, and generates two reports.
Understand key concepts and skills, including:
Starting workflows with trigger nodes
Configuring credentials
Manipulating data
Representing logic in an n8n workflow
Using expressions
Step one: Sign up for n8n#
Skip this section if you've already installed n8n or signed up for a Cloud account

n8n is available as a Cloud service, npm module, and Docker image. For this quickstart, n8n recommends using Cloud. A free trial is available for new users.

Step two: New workflow#
When you open n8n, you'll see either:

An empty workflow: if you have no workflows and you're logging in for the first time. Use this workflow.
The Workflows list on the Overview page. Select the universal create resource icon button to create a new workflow. -->


Step three: Add a trigger node#
n8n provides two ways to start a workflow:

Manually, by selecting Test Workflow, or from the CLI if you installed n8n with npm or Docker.
Automatically, using a trigger node as the first node. The trigger node runs the workflow in response to an external event, or based on your settings.
For this tutorial, use the Schedule trigger. This allows you to run the workflow on a schedule:

Select Add first step.
Search for Schedule. n8n shows a list of nodes that match the search.
Select Schedule Trigger to add the node to the canvas. n8n opens the node.
For Trigger Interval, select Weeks.
For Weeks Between Triggers, enter 1.
Enter a time and day. For this example, select Monday in Trigger on Weekdays, select 9am in Trigger at Hour, and enter 0 in Trigger at Minute.
Close the node details view to return to the canvas.
Step four: Add the NASA node and set up credentials#
The NASA node allows you to interact with NASA's public APIs. The API gives you data to work with in this tutorial.

Select the Add node Add node icon connector on the Schedule Trigger node.
Search for NASA. n8n shows a list of nodes that match the search.
Select NASA to view a list of operations.
Search for and select Get a DONKI solar flare. This operation returns a report about recent solar flares. When you select the operation, n8n adds the node to the canvas and opens it.
To access the NASA APIs, you need to set up credentials:
Select the Credential for NASA API dropdown.
Select - Create New -. n8n opens the credentials view.
Go to NASA APIs and fill out the form in Generate API Key. NASA generates the key and displays it.
Copy the key, and paste it into API Key in n8n.
Select Save.
Close the credentials screen. n8n returns to the node. The new credentials should be automatically selected in Credential for NASA API.
By default, DONKI Solar Flare provides data for the past 30 days. To limit it to just the last week, use Additional Fields:

Select Add field.
Select Start date.
To get a report starting from a week ago, you can use an expression: next to Start date, select the Expression tab, then select the expand button Add node icon to open the full expressions editor.
In the Expression field, enter the following expression:

{{$today.minus({days: 7}).toFormat('yyyy-MM-dd')}}
This generates a date in the correct format, seven days before the current date.
Date and time in n8n

n8n uses Luxon to work with date and time, and also provides two variables for convenience: $now and $today. For more information, refer to Expressions > Luxon.

Close the Edit Expression modal to return to the NASA node.

You can now check that the node is working and returning the expected date: select Test step to run the node manually. n8n calls the NASA API and displays details of solar flares in the past seven days in the OUTPUT section.
Close the NASA node to return to the workflow canvas.
Step five: Add logic with the If node#
n8n supports complex logic in workflows. In this tutorial, use the If node to create two branches that each generate a report from the NASA data. Solar flares have five possible classifications. You'll create logic that sends a report with the lower classifications to one output, and the higher classifications to another.

Add the If node:

Select the Add node Add node icon connector on the NASA node.
Search for If. n8n shows a list of nodes that match the search.
Select If to add the node to the canvas. n8n opens the node.
You need to check the value of the classType property in the NASA data. To do this:

Drag classType into Value 1.

Make sure you ran the NASA node in the previous section

If you didn't follow the step in the previous section to run the NASA node, you won't see any data to work with in this step.

Change the comparison operation to String > Contains.

In Value 2, enter X. This is the highest classification of solar flare. In the next step, you will create two reports: one for X class solar flares, and one for all the smaller solar flares.
You can now check that the node is working and returning the expected date: select Test step to run the node manually. n8n tests the data against the condition, and shows which results match true or false in the OUTPUT panel.
Weeks without large solar flares

In this tutorial, you are working with live date. If you find there aren't any X class solar flares when you run the workflow, try replacing X in Value 2 with either A, B, C, or M.

Once you are happy the node will return some events, you can close the node to return to the canvas.
Step six: Output data from your workflow#
The last step of the workflow is to send the two reports about solar flares. For this example, you'll send data to Postbin. Postbin is a service that receives data and displays it on a temporary web page.

On the If node, select the Add node Add node icon connector labeled true.
Search for PostBin. n8n shows a list of nodes that match the search.
Select PostBin.
Select Send a request. n8n adds the node to the canvas and opens it.
Go to Postbin and select Create Bin. Leave the tab open so you can come back to it when testing the workflow.
Copy the bin ID. It looks similar to 1651063625300-2016451240051.
In n8n, paste your Postbin ID into Bin ID.
Now, configure the data to send to Postbin. Next to Bin Content, select the Expression tab (you will need to mouse-over the Bin Content for the tab to appear), then select the expand button Add node icon to open the full expressions editor.
Select Current Node > Input Data > JSON > classType. n8n adds the expression to the Expression editor, and displays a sample output.
The expression is: {{$json["classType"]}}. Add a message to it, so that the full expression is:

There was a solar flare of class {{$json["classType"]}}
Close the expressions editor to return to the node.
Close the Postbin node to return to the canvas.
Add another Postbin node, to handle the false output path from the If node:
Hover over the Postbin node, then select Node context menu Node context menu icon > Duplicate node to duplicate the first Postbin node.
Drag the false connector from the If node to the left side of the new Postbin node.
Step seven: Test the workflow#
You can now test the entire workflow. Select Test Workflow. n8n runs the workflow, showing each stage in progress.
Go back to your Postbin bin. Refresh the page to see the output.
If you want to use this workflow (in other words, if you want it to run once a week automatically), you need to activate it by selecting the Active toggle.
Time limit

Postbin's bins exist for 30 minutes after creation. You may need to create a new bin and update the ID in the Postbin nodes, if you exceed this time limit.
