Lab 1 - Creating and Implementing an LX iRule
---------------------------------------------

Test and Review the Existing Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
  All code snippets are stored on the Windows Server 2016 Jumphost within a folder
  titled **ilxcode**.

To start off we have a web application that has a web form that we enter
some information into and submit.
# Now lets look at the web app at the URL **http://10.1.20.20/ilxlab1/** (Lab 1 on bookmarks).
# The response of the **POST** will show our form data and **“Content-Type”** header.
# Here is the example of the web form –

|image1|

# Go ahead and run your own test of the web app. Observe the **“Content-Type”**
  header and **POST** data values. Here is an example of the response to a POST.

|image2|


Create the LX Workspace
~~~~~~~~~~~~~~~~~~~~~~~

The first thing we need to do is create an LX Workspace. On the BIG-IP,
navigate over to the LX workspaces menu in the tab located at
**Local Traffic > iRules > LX Workspaces.** Then select the **create** button at the
top right of the table and name the workspace **ilxlab1**. You will now have
an empty workspace.

Create the Extension
~~~~~~~~~~~~~~~~~~~~

Next, we create a new extension (the Node.js code that will run). The
name of the extension will matter later because we will call that from
our iRules TCL code.

To create the extension, click the **Add Extension** button at the bottom
of the editor, then give it the name **ilxlab1\_ext**. The various files
of the extension will show up. Select the **index.js** file and you should
see a template of example code in the editor window. Normally you could
use this example code as a starting point, but in our case we should
delete all the example code from the window. In the Atom editor,
locate the file named **ilxlab1.js** within the ilxcode folder and double
click it which should open it in a text editor. Copy and paste this into
the **index.js** file on our BIG-IP.


Then you will need to save the changes to this file with the **Save File**
button at the bottom of the editor window.

Create the TCL iRule
~~~~~~~~~~~~~~~~~~~~

Next, we need to create the TCL iRule that will call our Node.js code.
Click the button **Add iRule** at the bottom of the editor window, name
the iRule **json\_post** and don’t check the box to include example code
(we don’t need the example code for this lab). In the Atom editor, locate
the file named within the ilxcode folder called **ilxlab1.tcl**.  Copy and paste
the contents into the **json\_post** iRule file.

Then you will need to save the changes to this file with the **Save File**
button at the bottom of the editor window.

Create the LX Plugin
~~~~~~~~~~~~~~~~~~~~

Now that we have our code in a workspace, you will need to navigate over
to the LX Plugins menu in the tab located at **Local Traffic > iRules > LX Plugins**.
Click the **Create** button, name the plugin **ilxlab1\_pl**,
select the **ilxlab1** workspace and click **Finished**. This makes the
Node.js code active.

Apply the LX iRule to the Virtual Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now that we have our Node.js code running, we can put it to use. In
order to use the code from the plugin we must assign the TCL iRule to a
virtual server. Just so we can be familiar with it (but it is not
required), we will look for the TCL iRule in the **Local Traffic > iRules > iRules List**
menu.  You will find the iRule that we created in the workspace located
there with a Partition/Path that has the same name as our plugin.

|image3|

You wont be able to make changes from here. This is the same behavior as
an iApp with strict updates enabled.

Now navigate over to our virtual server list, click the **Edit** button
(under the **resources** column) for the virtual **ilxlab1\_vs** and select
the **Manage** button for iRules. If you scroll to the bottom of the
available iRules list, you should see the iRule from our plugin.

|image4|

Move this iRule to the over to the enabled section and click **finished**.

Testing the LX iRule
~~~~~~~~~~~~~~~~~~~~

Now let’s navigate to the second tab on the browser with the web page of
our app. Go back to the web form and submit the information again. You
will see now that the data has been converted to JSON and the
**Content-Type** header has been changed.

|image5|

As you can see, with iRules LX we can implement solutions with very few
lines of code. If we wanted to accomplish the same goal in TCL alone, it
would most likely take several hundred lines of code.

Workspace Package Management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Lastly, we will show package management for LX workspaces. While it is
fairly simple to move TCL iRules from a dev/test environment to
production because it is a single file, iRules LX can have an almost
unlimited number of files depending on how many NPM modules a solution
needs. Therefore, workspaces have been given the ability to export and
import packages as a tgz file to have a more convenient method of
transporting iRules LX code. In this exercise, we will export our
package and import it back into the same device (but normally import
would happen on a separate BIG-IP).

Export/Import a Workspace
^^^^^^^^^^^^^^^^^^^^^^^^^

Go to the **LX Workspaces** list, check the box of our *ilxlab1*
workspace and click the **Export** button below the list. This will
save the file to the user’s **Downloads** folder.

Now click the **Import** button on the top right hand corner of the
workspace list. On the next window give the imported workspace the name
of **ilxlab1\_restore**, select the option **Archive File**, and use the
**Choose File** button to find the tgz file in the user’s **Downloads** folder.
When you click the **Import** button you will be taken back to the workspace
list and you should see the imported workspace now. Feel free to navigate into the
imported workspace.

You have concluded lab exercise #1
##################################

.. |image1| image:: /_static/class3/image2.png
   :width: 5.59375in
   :height: 4.28125in
.. |image2| image:: /_static/class3/image3.png
   :width: 6.229166in
   :height: 3.16666in
.. |image3| image:: /_static/class3/image4.png
   :width: 7.5in
   :height: 1.6979166in
.. |image4| image:: /_static/class3/image5.png
   :width: 7.208333in
   :height: 1.65625in
.. |image5| image:: /_static/class3/image6.png
   :width: 6.510416in
   :height: 3.8125in

