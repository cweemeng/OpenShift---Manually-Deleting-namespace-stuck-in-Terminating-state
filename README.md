# OpenShift---Deleting-namespace-stuck-in-Terminating-state
<pre>
  You are deleting a namespace from the OpenShift Console using the Options menu at the far right side of the project listing, selecting "Delete project".
  After the action, the namespace stays with a "Terminating" status on the RedHat OpenShift console and doesn't go away.
  
  <img src="/images/namespace_in_terminating_state.png" alt="Namespace stuck in Terminating state"/>

  Here are the steps you can take to manually delete the terminated namespace. 
  
  Step 1
      Open a terminal window. Make sure you have already installed the OpenShift CLI (oc) binary. 
  
  Step 2
      Login to OpenShift cluster with the "Display Token" from the "Copy Login command" action found in the OpenShift Console, top, far right side
            <img src="/images/login_token.png" alt="Login Token"/><br>
            <img src="/images/display_token.png" alt="Display Token"/>
  
  Step 3
       View the namespaces that are stuck in the Terminating state:<br>
            <b>oc get namespaces</b>

  Step 3
      Copy the content of the terminating namespace into a temporary file
            <b>oc get namespace "terminating-namespace" -o json > tmp.json </b>

  Step 3
      Edit your tmp.json file. Remove the kubernetes value from the finalizers field and save the file.

            vi tmp.json

            Search for the keyword "finalizers" It should be under the section <b>spec:</b>b>
            Once found, remove the entire line and save the file.  
  
  Step 4
      You will need to start a temporary local proxy server to let the local server talk to the remote OpenShift cluster. 

      Make sure you do not have any existing proxy process started. You can check this with the following command:

          <b>ps -ef | grep "oc proxy"</b>

      It will show you the PID of the process, if it is there

      Then you can stop it with
          <b>kill -9 "pid"</b>
  
         <img src="/images/delete_oc_proxy.png" alt="Kill proxy process"/>

      Running the following command to start the temporary proxy process
              <b>oc proxy</b>

      Your proxy IP and port might resemble the following output:
             Starting to serve on 127.0.0.1:8001
            
  Step 5
      Important ****
      Open a new terminal window. Make sure you login to the OpenShift cluster
  
      Make an API call with your temporary proxy IP and port:
             <b>curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/"terminating-namespace"/finalize</b>

  Step 6
      Verify that the terminating namespace is removed with :
              <b>oc get namespaces</b>
           to see if the namespace is still there OR
              <b>oc project "terminating namespace"</b>
                
  <b style='color:blue !important;'>Resources</b>
  Red Hat has also share an article on this topic
  https://www.redhat.com/en/blog/openshift-terminating-state
  
</pre>
