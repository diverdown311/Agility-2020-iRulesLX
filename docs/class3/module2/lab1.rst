Lab 1 - Asynchronous Programming
--------------------------------

Test and Review the Existing Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

<<<<<<< HEAD:docs/iRulesLX/module2/lab1.rst
In this lab we will be working with the virtual server (10.1.20.22) &
workspace named *ilxlab3*. The plugin and TCL iRule are already assigned
to the virtual server. To start off we have a web application that
displays a list of users in a database. This web app is configured on
our BIG-IP at the URL http://10.1.20.22/.

SQL Database Lookup
~~~~~~~~~~~~~~~~~~~

In this lab we are simply going to view some log statements into the
Node.js and look at the order they appear in the log file. First we will
review the sql query method in our extension code highlighted below:

.. code-block:: javascript
   :linenos:
   :emphasize-lines: 4-18

   // Add a method
   ilx.addMethod('get_users', function(req, res) {
     // Perform the query from pool
     sqlPool.query(
       'SELECT id, name, grp FROM users_db.users ORDER BY id;',
       function(err, rows) {
         if (err) {
           // MySQL query failed for some reason, send a 2 back to TCK
           console.error('Error with query: ', err.message);
           return res.reply(2);
         }

         // Check array length from sql
         if (rows.length)
           res.reply([0, rows]);
         else
           res.reply(1); // if 0 return 1 to the Tcl iRule to show no matching records
       }
     );
   });
=======
In this lab we will be working from the file **ilxlab2\_steps.js**. You will
be **cutting and pasting** code from this file as directed. We will be
working with the virtual server (10.1.20.21) & workspace named **ilxlab2**
which already has some base code in it. The plugin has already been created
and the TCL iRule are already assigned to the virtual server.

To start off we have a web application with a web form that we enter
some information into and submit. The response of the POST will show our
form data and **“Content-Type”** header. This web app is configured on our
BIG-IP at the URL http://10.1.20.21/ilxlab2/. Now lets look at the web
app in the browser. 

#. Here is the example of the web form:

   |image6|

While we may never have a real use case with JSON in a web form, doing
this allows us to use a web browser for the lab rather than having to
use command line tools.

#. Without modifying any text in the form, pressing the submit button
   should result in this:

   |image7|

#. Push the back button and modify the JSON text to so that it will be
   invalid:

   |image8|

#. Then press submit and you should see this:

   |image9|

   This error came from our webserver. You can tell this because the
   webpage has the logo, header and horizontal rules. Later in this lab we
   will see errors from the BIG-IP that will be text only.

#. Now, go back to the web form and click refresh to clear the incorrect
   JSON.

iRules LX Code Update Behavior
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Because of the way iRules LX transitions new generations of an LX Plugin,
when we make changes to the code we will not see these changes in our
original browser window because it is using the same TCP connection and
is being serviced by the previous version of the LX plugin. 

#. To force the BIG-IP to give us the new generation of the LX plugin, we will be
   running the following TMSH command after every workspace change:

   .. admonition:: TMSH

      restart ilx plugin <plugin_name> immediate

Error Logs
~~~~~~~~~~

In lab exercises from here on out we will need to view the output of
**STDOUT/STDERR** of our Node.js processes. By default, it will be directed
to the log files /var/log/ltm. Starting in version 13.0, we introduced a
new feature to iRules LX allowing for STDOUT/STDERR to be sent to a
dedicated log file for each Node.js process for an extension within an
LX Plugin.

The extension ``ilxlab2_ext`` of plugin ``ilxlab2_pl`` is already
configured with the following settings so we can make the logs of the
lab easier to find -

+---------------------+-------------+----------------------------------------------------+
| Setting             | New Value   | Reason                                             |
+=====================+=============+====================================================+
| Concurrency Mode    | Single      | Keep logs for all connections in a single file.    |
+---------------------+-------------+----------------------------------------------------+
| iRules LX Logging   | Checked     | Will make extension send logs to dedicated file.   |
+---------------------+-------------+----------------------------------------------------+

#. To see these settings for yourself, click on the **ilxlab2\_pl** LX plugin
   and then click on the **ilxlab2\_ext** extension. The dedicated log files
   can be found in the directory **/var/log/ilx/** and will be named in the
   format **<partition_name>.<plugin_name>.<ext_name><tmm_id_if_dedicated_mode>**.

#. Here are some examples of file names -

   .. code-block:: console

      Common.ilxlab2_pl.ilxlab2_ext
      Common.ilxlab99_pl.some_ext0
      Common.ilxlab99_pl.some_ext1

Exception Handling
~~~~~~~~~~~~~~~~~~

Good software development incorporates exception handling into the code.
Without it, our programs would simply crash when there is an uncaught
exception. On iRules TCL, the TCL interpreter crashes for an uncaught
exception, but the worst consequence is that a single client connection
is reset.

Because Node.js in iRules LX is external from TMM, a crash is much more
serious. Any connection being serviced by that Node.js process will get
reset and all state for any outstanding RPC calls will be lost. A crash
triggered from a single function call has the potential to reset
hundreds or even thousands of connections on the BIG-IP. Also, any new
connections that are trying to establish while Node.js is rebooting
could also be reset.

Therefore, it is imperative that we learn proper exception handling.

Handle Errors in JavaScript
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Right now the LX workspace code does not have any function call that can
throw an exception, but we would like to add more functionality to it.

#. Here is the addMethod function that we have in the Node.js code:

   .. code-block:: javascript
      :linenos:

      ilx.addMethod('jsonParse', function (req, res) {
        // Extract JSON from POST data
        var postData = qs.parse(req.params()[0]).JSON;

        // Send data back to TCL
        res.reply(postData);
      });
>>>>>>> d1189f21a2d9d72f47df1eacfa1873030e24fe7c:docs/class3/module2/lab1.rst

You will notice that the function has 2 arguments, the first being the
text of the actual query. Because this method is asynchronous, the
second argument is the callback function that will get executed when the
query answer is received by Node.js.

<<<<<<< HEAD:docs/iRulesLX/module2/lab1.rst
To demonstrate asynchronous behavior, we will put logging statements
before and after the query method as such:
=======
All we are doing is extracting the form input box labeled “JSON”. But we
would like to insert more data into the JSON that we send to the
application. 
>>>>>>> d1189f21a2d9d72f47df1eacfa1873030e24fe7c:docs/class3/module2/lab1.rst

#. In order to do that, we must first parse the JSON to a JS
   object, then stringify it again. Go to the **code\_instructions** and
   complete **code step 1** (remember to copy and paste). The ILX addMethod code should look like this
   after you are done (changes are highlighted) -

<<<<<<< HEAD:docs/iRulesLX/module2/lab1.rst
.. code-block:: javascript
   :linenos:
   :emphasize-lines: 4, 13, 22

   // Add a method 
   ilx.addMethod('get_users', function(req, res) {
     // Perform the query from pool
     console.log('Starting SQL query');
     sqlPool.query(
       'SELECT id, name, grp FROM users_db.users ORDER BY id;',
       function(err, rows) {
         if (err) {
           // MySQL query failed for some reason, send a 2 back to TCK
           console.error('Error with query: ', err.message);
           return res.reply(2);
         }
         console.log('There are', rows.length,'records in the DB.');
   
         // Check array length from sql
         if (rows.length)
           res.reply([0, rows]);
         else
           res.reply(1); // if 0 return 1 to the Tcl iRule to show no matching records
       }
     );
     console.log('SQL query finished.');
   });
   
Make sure to use the TMSH plugin restart command after you reload the
workspace. Now tail the log contents of the log file with the following
BASH command and then refresh the ilxlab3 web page:
=======
   **Code Step 1**

   .. code-block:: javascript
      :linenos:
      :emphasize-lines: 4, 6

      ilx.addMethod('jsonParse', function (req, res) {
        // Extract JSON from POST data
        var postData = qs.parse(req.params()[0]).JSON;
        var jsonData = JSON.parse(postData);
>>>>>>> d1189f21a2d9d72f47df1eacfa1873030e24fe7c:docs/class3/module2/lab1.rst

        res.reply(JSON.stringify(jsonData));
      });

<<<<<<< HEAD:docs/iRulesLX/module2/lab1.rst
``# tail -f /var/log/ilx/Common.ilxlab3_pl.mysql``

What do you notice about the order of the log statements?

Now let’s make the following changes to the node.js as seen below.
=======

#. Save and reload the workpsace. Now submit some invalid JSON in the form
   like we did earlier. You will see an text only error like this:

   |image10|

#. This error is coming from the iRules TCL code in our “catch” of the ILX
   call. If we look at the logs we will see the following:

   ..code-block:: console

     # tail -1 /var/log/ltm
     Jul 11 16:02:15 bigip1 err tmm1[14567]: Rule /Common/ilxlab2_pl/json_parse <HTTP_REQUEST_DATA>: Client - 10.0.0.  10, ILX failure: ILX timeout.     invoked from within "ILX::call $handle jsonParse [HTTP::payload]" ``

     # tail -1 /var/log/ltm
     Jul 11 16:02:15 bigip1 err tmm1[14567]: Rule /Common/ilxlab2_pl/json_parse <HTTP_REQUEST_DATA>: Client - 10.0.0.  10, ILX failure: ILX timeout.     invoked from within "ILX::call $handle jsonParse [HTTP::payload]"

#. The log file for the extension should have some entries similar to this:

   .. code-block:: console

      # tail -20 /var/log/ilx/Common.ilxlab2_pl.ilxlab2_ext
      Jul 11 16:02:12 pid[15201] undefined:5
      Jul 11 16:02:12 pid[15201] randomtext
      Jul 11 16:02:12 pid[15201] ^
      Jul 11 16:02:12 pid[15201] SyntaxError: Unexpected token w
      Jul 11 16:02:12 pid[15201]     at Object.parse (native)
      Jul 11 16:02:12 pid[15201]     at Object.jsonParse (/var/sdm/plugin_store/plugins/:Common:   ilxlab2_pl_62102_2/extensions/ilxlab2_ext/index.js:13:23)
      Jul 11 16:02:12 pid[15201]     at ILXClient.<anonymous> (/var/sdm/plugin_store/plugins/:Common:   ilxlab2_pl_62102_2/extensions/ilxlab2_ext/node_modules/f5-nodejs/lib/ilx_server.js:100:46)
      <--------------Rest of output truncated -------------->

#. As you can see, our bad JSON threw an exception that crashed the Node.js
   process which caused an ILX timeout in TCL. This is the stack track for our exception.
>>>>>>> d1189f21a2d9d72f47df1eacfa1873030e24fe7c:docs/class3/module2/lab1.rst

#. To prevent Node.js from crashing we need to put JSON.parse in a try/catch block. Perform
   code step 2 on the workspace to do this. The Node function should end up like this –

<<<<<<< HEAD:docs/iRulesLX/module2/lab1.rst
.. code-block:: javascript
   :linenos:
   :emphasize-lines: 20, 23

   // Add a method
   ilx.addMethod('get_users', function(req, res) {
     // Perform the query from pool
     console.log('Starting SQL query');
     sqlPool.query(
       'SELECT id, name, grp FROM users_db.users ORDER BY id;',
       function(err, rows) {
         if (err) {
           // MySQL query failed for some reason, send a 2 back to TCK
           console.error('Error with query: ', err.message);
           return res.reply(2);
         }
         console.log('There are', rows.length,'records in the DB.');

         // Check array length from sql
         if (rows.length)
           res.reply([0, rows]);
         else
           res.reply(1); // if 0 return 1 to the Tcl iRule to show no matching records
         console.log('SQL query is really finished.');
       }
     );
     console.log('Function call is finished.');
   });

Use the TMSH plugin restart command after you reload the workspace. Now
tail the log contents of the log file again and then refresh the ilxlab3
web page. You will see that they are in the right order. The callback
function is executed much later because I/O responses take much longer.

But you might ask, how much later is the callback function executing? To
answer that question, lets add some more code:

**Code Step 3**
=======
   **Code Step 2**

   .. code-block:: javascript
      :linenos:
      :emphasize-lines: 4-9

      ilx.addMethod('jsonParse', function (req, res) {
        // Extract JSON from POST data
        var postData = qs.parse(req.params()[0]).JSON;
        try {
          var jsonData = JSON.parse(postData);
        } catch (err) {
          console.log('Error with JSON.parse: ' + err.message);
          return; // Stop processing this function
        }

        res.reply(JSON.stringify(jsonData));
      });

#. Save and reload the workspace. Now if you try bad JSON again, you will still
   get the same error on the web browser, but we will not crash the Node.js
   process. Doing a tail of the log files again, you will see an error message
   similar to this:

   ``Jul 11 16:14:55 pid[15456] Error with JSON.parse: Unexpected token w``

   **Note**: Try/catch is only for synchronous functions. Most asynchronous
   functions handle exceptions/errors in the callback function or with
   event handlers and vary greatly from one module to the next. You will
   have to consult the documentation for the module you wish to use.

RPC Status Return Value
^^^^^^^^^^^^^^^^^^^^^^^

While try/catch did help to prevent the Node process from crashing, the
error the client received does not help them very much. It would be
better if we could give some more info to the client via iRules TCL, but
TCL does not know about the issue that happen with Node.js. Therefore,
we should return some type of status to TCL if it the RPC to Node fails.

One way we can accomplish this is by the return of multiple values from
Node.js. Our first value could be some type of RPC status value (say an
RPC error value) and the rest of the value(s) could be our result from
the RPC. It is quite common in programming to make an error value would
be 0 if everything was okay but would be an integer to indicate a
specific error code.

For this next step, we will make changes to both Node and TCL to create
the error communication between Node and TMM. Perform code step 3a and 3b
on the workspace. 

#. This is what the Node method and the TCL
   **HTTP\_REQUEST\_DATA** event should look like after you make the changes:

   **Code Step 3 Node.js**

   .. code-block:: javascript
      :linenos:
      :emphasize-lines: 8, 11

      ilx.addMethod('jsonParse', function (req, res) {
        // Extract JSON from POST data
        var postData = qs.parse(req.params()[0]).JSON;
        try {
          var jsonData = JSON.parse(postData);
        } catch (err) {
          console.log('Error with JSON.parse: ' + err.message);
          return res.reply(1);
        }

        res.reply([0, JSON.stringify(jsonData)]);
      });

#. As you can see in the res.reply function, we can return multiple values
   back to TCL if we put an array as the argument. TCL will then see these
   values returned as a TCL list.

   **Code Step 3 TCL**

   .. code-block:: tcl
      :linenos:
      :emphasize-lines: 10-21

      when HTTP_REQUEST_DATA {
          # Send data to Node.js
          set handle [ILX::init " ilxlab2_pl" "ilxlab2_ext"]
          if {[catch {ILX::call $handle jsonParse [HTTP::payload]} result]} {
            log local0.error  "Client - [IP::client_addr], ILX failure: $result"
            HTTP::respond 400 content "<html>There has been an error.</html>"
            return
          }

          if {[lindex $result 0] > 0} {
            # What is our error code?
            switch [lindex $result 0] {
              1 { set error_msg "Invalid JSON"}
            }
            HTTP::respond 400 content "<html>The following error occured: $error_msg</html>"
          } else {
            #Replace Content-Type header and POST payload
            HTTP::header replace "Content-Type" "application/json"
            HTTP::payload replace 0 $cl [lindex $result 1]
          }
      }

Here we are checking the value of index 0 of the TCL list to see if it is
greater than zero. Based upon what that value is we can tailor our return
message back to the client. What we have done is allowed Node.js to
communicate specific errors that we define back to the client. You would
never want to send back all errors because stack traces could reveal
sensitive data about your iRule.

#. Save and reload the workspace. Now when you submit invalid JSON in the
   browser you should see an error like this –

   |image11|

Now that we have the exception handling taken care of, lets add some
more functionality to this iRule. We mentioned a little while ago we
would like to add some more data to the JSON that gets sent to the
server.

Let’s say we wanted to insert random data to act as some type of nonce.
In code step 4 let’s use the crypto module to insert the random text.
This code snippet will show what all the node.js code should look like
after this step:

**Code Step 4**

.. code-block:: javascript
   :linenos:
   :emphasize-lines: 5, 21

   'use strict'; // Just for best practices
   // Import modules here
   var f5 = require('f5-nodejs');
   var qs = require('querystring');
   var crypto = require('crypto');

   // Create an ILX server instance
   var ilx = new f5.ILXServer();

   // This method will transform POST data into JSON
   ilx.addMethod('jsonParse', function (req, res) {
     // Extract JSON from POST data
     var postData = qs.parse(req.params()[0]).JSON;
     try {
       var jsonData = JSON.parse(postData);
     } catch (err) {
       console.log('Error with JSON.parse: ' + err.message);
       return res.reply(1);
     }

     jsonData.token = crypto.randomBytes(8).toString('hex');
     res.reply([0, JSON.stringify(jsonData)]);
   });

   ilx.listen();

#. Save and reload the workspace.

   **Note**: This is not really a proper use of a cryptographic nonce, it
   is just to show how we can extend functionality with Node.js.

   Now this time, send valid JSON text via the web form and we should see a
   result like this:

   |image12|

   You can see our token has been added to the JSON.

   This concludes the exception handling exercise.

Installing Packages with NPM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can install modules from NPM when you want to get extra
functionality that is not provided with the built in Node.js modules.
NPM and the active community around it is one of the primary reasons
that Node.js was chosen for iRules LX.

We have a use case requiring us to do syntax validation of an email
address that is in the JSON text from a web form. We won’t be checking
if the email address itself is a working address, just that the syntax
is in the correct form. We will download a package from NPM to handle
the this.

Installing the Validator Module from NPM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. The first thing we must do is install a NPM module for validating email
   addresses. We will accomplish this with the *validator* module. 

#. To install the module into the workspace, we need to access the BASH prompt
   of our BIG-IP, then ``cd`` into the workspace directory and run the commands:

   .. code-block:: console

      [root@localhost] # cd /var/ilx/workspaces/Common/ilxlab2/extensions/ilxlab2_ext/
      [root@localhost] # npm install validator --save
      validator@6.1.0 node_modules/validator
      [root@localhost] # ls node_modules/
      f5-nodejs  validator


   The ``--save`` option saves the module to the package.json file
   dependencies as shown here in the workspace:

   |image13|

Using the Validator Module
^^^^^^^^^^^^^^^^^^^^^^^^^^

To use this module, we must import it into out Node.js code and
then call it. In code step 5, we will “require” the module in
Node.js, then put some code that will validate if our email address
has the proper format. We will also need to add some extra code to TCL
to hand 2 more error conditions that email validation brings. The first
check ensures that the email value is in our JSON,  the second uses the
validator module to validate the syntax of the email address. Here is
what the code will look like once you are finished:

**Code Step 5 Node.js**
>>>>>>> d1189f21a2d9d72f47df1eacfa1873030e24fe7c:docs/class3/module2/lab1.rst

.. code-block:: javascript
   :linenos:
   :emphasize-lines: 5, 21

   // Add a method
   ilx.addMethod('get_users', function(req, res) {
     // Perform the query from pool
     console.log('Starting SQL query');
     var start = Date.now();
     sqlPool.query(
       'SELECT id, name, grp FROM users_db.users ORDER BY id;',
       function(err, rows) {
         if (err) {
           // MySQL query failed for some reason, send a 2 back to TCK
           console.error('Error with query: ', err.message);
           return res.reply(2);
         }
         console.log('There are', rows.length,'records in the DB.');

         // Check array length from sql
         if (rows.length)
           res.reply([0, rows]);
         else
           res.reply(1); // if 0 return 1 to the Tcl iRule to show no matching records
         console.log('SQL query is really finished, time:', Date.now() - start, 'msec');
       }
     );

     console.log('Function call is finished.');
   });

Use the TMSH plugin restart command after you reload the workspace. Now
tail the log contents of the log file again and then refresh the ilxlab3
web page. Most likely, you are seeing that the time logged is in the
order of tens of milliseconds. As you saw from the I/O time table in the
presentation, this is an eternity compared to reads from local memory.

