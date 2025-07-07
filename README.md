<h1>EDR Projet</h1>

<p>In this project we are going to be using LimaCharlie as our EDR to use a tool to recover passwords. Then it will be sent to our SIEM on Tines, which will then forward the playbook to Slack, providing an email asking the user if they want to isolate the machine. When “Yes” is selected, LimaCharlie will isolate the machine.</p>

<p>We will start by designing the playbook workflow to get a rough outline of what we want to achieve.</p>

<p>We then are going to start getting our data in from our Windows Machine.</p>

<p>After, we are going to generate events using the Password Recovery Tool. We will then create a custom detection and response rule which will be sent to Tines for automation.</p>

<p>We will then set up Slack and Tines, which we will validate inside of Tines.</p>

<p>We then will create our playbook automation or “Story” as categorized in Tines.</p>

<p>We will create a story in Tines.</p>

<p><b>Textual Diagram</b></p>

<p>We will create a Story in Tines.</p>

<p>Create a detection rule in LimaCharlie which will Detect Hack which will then be forwarded into Slack & Email.</p>

<p>In this email, we will include time, computer name, source IP, process, command line, file path, sensor ID, and link to detection (if available).</p>

<p>Tines will prompt the user as to whether they want to isolate the machine with a (Yes/No) option.</p>

<p>If the user selects Yes: LimaCharlie will automatically isolate the machine and a message will be sent to Slack. A message will then be sent notifying the user that “The computer &lt;computer&gt; has been isolated”.</p>

<p>If the user selects No: LimaCharlie will not isolate the machine and a message will be sent to Slack. A message will then be sent notifying the user that “The computer &lt;computer&gt; has not been isolated”.</p>

<p>Here is a visual diagram of our EDR and SOAR automation.</p>

<p>Let’s start by setting up LimaCharlie with my machine.</p>

<p>In the Sensor’s tab, we will go to Installation Key. Let’s create an installation key so we can enroll our machine into LimaCharlie.</p>

<p>Now that this machine is integrated into LimaCharlie, it will be listed in the Sensors List.</p>

<p>We are going to customize the D&amp;R section in automation to create our custom detection and response rules.</p>

<p>Finally, we will link LimaCharlie to Tines through the Outputs setting.</p>

<p>Let’s use Windows PowerShell to download the LimaCharlie agent:</p>
<pre><code>hcp &lt;file&gt; -i</code></pre>

<p>Great, we have downloaded the LimaCharlie agent to our machine.</p>

<p>Let’s download LaZagne as our telemetry tool.</p>

<p>We can see in the Timeline section all the relevant information regarding the processes occurring on our machine.</p>

<p>Let’s create a custom D&amp;R Rule using this information to provide a more granular approach to isolating machines.</p>

<p>Let’s start with our detection rule:</p>

<p>This rule starts with specifying two necessary events: either <code>New_Process</code> or <code>Existing_Processes</code>. We are using the operator to specify <code>FILE_PATH</code> ending with our program <code>lazagne.exe</code>. We then make sure to not have case sensitivity. We allow the command line to allow value as specifying “all”. Second to last, we specify the command line value to also potentially have <code>lazagne</code> without case sensitivity. Lastly, we input the specified hash of the LaZagne hash. This will allow us to cover every single detection criteria.</p>

<p>Now onto the response side:</p>

<p>The action will report the detection to the specified output.</p>

<p>We edited some values from this template, such as the name, and took it out from reference.</p>

<p>Let’s save the rule and test the event.</p>

<p>We tested a sample event, and the detection and response rule seems to be working great!</p>

<p>Now let’s set up Slack and Tines and test the connection between LimaCharlie and Tines.</p>

<p>Great, now we have a new channel.</p>

<p>Now let’s set up Tines, with our Playbook or as Tines calls it, a “Story”.</p>

<p>Let’s clear all the default options and build our Playbook.</p>

<p>We’ll start with a Webhook to retrieve the detections.</p>

<p>We’ll copy the Webhook URL into LimaCharlie.</p>

<p>Ok, let’s add the output.</p>

<p>Ok great, let’s choose detections.</p>

<p>Let’s choose Tines and copy our Webhook URL to connect it.</p>

<p>Ok, now let’s have our Webhook response send a message to Slack via the Tines Story configuration page.</p>

<p>Let’s copy and paste the Channel ID and connect Tines and Slack.</p>

<p>Finally, let’s add “Send Email Action” and User Prompt so the user can choose whether or not they want to isolate the machine.</p>

<p>Now, in the event data, let’s get the values we want to forward to the Slack message and the Email and title them.</p>

<p>Let’s also add some HTML line breaks.</p>

<p>Now let’s configure a Slack message if the user selects the “No” option in response to isolating the computer.</p>

<p>Great, now the “No” option of the Boolean is programmed.</p>

<p>Let’s configure the “Yes” option of the Boolean.</p>

<p>Ok, let’s now choose a LimaCharlie HTTP Request to isolate the sensor. So we copied the value into the API link to automate the isolation.</p>

<p>LimaCharlie’s API requires credentials, so let’s get our organization API credential key from LimaCharlie and add the credential to Tines.</p>

<p>Ok, now we have credentials for our HTTP Request isolation of the sensor.</p>

<p>Great, it worked. It has successfully isolated the computer from the network. Let’s test one final ping to the public internet to confirm the computer has been isolated from the internet.</p>

<p>Perfect, the ping also confirms it is isolated.</p>
