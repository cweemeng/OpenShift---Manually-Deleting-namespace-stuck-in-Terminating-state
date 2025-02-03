# OpenShift---Deleting-namespace-stuck-in-Terminating-state
<pre>
  You see a namespace with a "Terminating" status on the RedHat OpenShift console. Here are the steps you can take to manually delete the terminated namespace. 
  
  Step 1
      View the namespaces that are stuck in the Terminating state:<br>
            <b>oc get namespaces</b>

  Step 2
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

          ps -ef | grep "oc proxy"

      It will show you the PID of the process, if it is there

      Then you can stop it with
          kill -9 <pid>

          ![image](https://github.com/user-attachments/assets/4a6a0774-b4e0-4655-abd9-0e52f44b68fa)

      Running the following command to start the temporary proxy process
          oc proxy

      Your proxy IP and port might resemble the following output:
         Starting to serve on 127.0.0.1:8001
            
  Step 5
      Open a new terminal window, and make an API call with your temporary proxy IP and port:
             curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/"terminating-namespace"/finalize
      
  <b>Resources</b>
  Red Hat has also share an article on this topic
  https://www.redhat.com/en/blog/openshift-terminating-state
  
</pre>
