

<!---
Version: 1.0 
-->
# AD replication symptom identification
>LODSProperties
>* IntroductionUri = https://lodmanuals.blob.core.windows.net/manuals/Ignite 2016/Videos/IDL3089/Exercise 1- AD Replication Symptom Identification.mp4

## INTRODUCTION MESSAGE
In this exercise, you will use repadmin.exe, the AD Replication Status tool \)adreplstatus.exe) and Windows PowerShell to display the Active Directory replication status for the Contoso forest. Each task presents a different method to accomplish the same thing.
## COMPLETION MESSAGE

### Task 1: Use the AD Replication Status Tool
In this task, you will use the AD replication Status Tool to view the current state of AD replication in the Contoso environment.  
  
In this task you will use the Win8Client virtual machine.

#### :bulb: KNOWLEDGE
In this task, you will use the AD replication Status Tool to view the current state of AD replication in the Contoso environment.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_Win8Client



### Connect to Win8Client
Login as ROOT\\Administrator account  with Password: adrepl123!

#### :bulb: KNOWLEDGE
• Note: Domain admin privileges are not needed for this task, but these privileges are required in later exercises.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_Win8Client 



### Use Repadmin to connect to dc1
Open an elevated command prompt and use repadmin to verify that Win8Client can connect to DC1. Use the following command:  
Repadmin /bind dc1

#### :warning: ALERT
If the above command returns data proceed to step 3. If it fails with an LDAP error 82, do the following:  
• Switch to DC2 and pause the virtual machine by selecting Pause from the holSystems Launchpad  
• Switch to Win8Client and attempt to bind to DC1 again:  
Repadmin /bind dc1  
• When this completes and returns data, switch back to DC2 and click Resume  
The above steps may be necessary if Win8Client uses DC2 for KDC operations. For this task, we need the client to use DC1 as a KDC since DC2 has been intentionally broken for a later exercise. The issue with DC2 is resolved in exercise 2.



#### :calling: COMMAND TypeText
```
Repadmin /bind dc1
```

### Manually initiate replication ###
Execute the following commands to initiate replication manually.
repadmin /syncall DC1 /Aed
repadmin /syncall DC2 /Aed
repadmin /syncall ChildDC1 /Aed
repadmin /syncall ChildDC2 /Aed
repadmin /syncall TRDC1 /Aed


#### :calling: COMMAND TypeText
```
repadmin /syncall DC1 /Aed
repadmin /syncall DC2 /Aed
repadmin /syncall ChildDC1 /Aed
repadmin /syncall ChildDC2 /Aed
repadmin /syncall TRDC1 /Aed
```
### Open the AD Replication Status Tool
On Win8Client, double click the AD Replication Status Tool 1.0 shortcut on the desktop.





### View Replication Status
Within the AD Replication Status Tool, click Refresh Replication Status.  
  
• The tool will take one to two minutes to check the AD replication status.   
• You will know data collection is complete when the Status: prompt changes from Running to Ready and the focus is switched to the Replication Status Viewer tab.   
 • Note that replication status is reported for three DCs, but there are actually five DCs in the forest. You will see why replication status is missing from two DCs in the next step.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/611504.jpg





### Review the results of the data collection

• Click the Configuration/Scope Settings tab.  
• The Replication Status Collection Details pane lists all DCs in which the tool was able to collect data.   
o  Review the Environment Discovery tab for any errors.   
o Take note there is an error for child.root.contoso.com.   
o The Discovery Missing Domain Controllers tab notes that NTDS Settings objects exist but since discovery failed for the child domain, the tool was unable to collect data from these DCs: ChildDC1 and ChildDC2. This issue is resolved in Exercise 3.


#### :warning: ALERT
When refreshing replication status on future runs of the tool, ensure you select the Re-Discover Environment option so it will attempt to collect data from the child domain DCs. This setting is available under the Configuration/Scope Settings tab.





### View Replication Errors
Click the Replication Status Viewer tab, and then select Errors Only.  
One or more of the following errors may be reported in the environment: -2146893022, 1908, 1256, 8606 and error 5

#### :warning: ALERT
If error 8614 is observed, you will need to perform the steps in Exercise 6 at some point in the lab.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/611506.jpg





### View the Replication Error Guide
Click the Replication Error Guide tab.   
Click on the message text for any error. A list of DCs with that replication status is displayed in the bottom pane.  
Click on any error code. Our recommended troubleshooting content on TechNet is displayed (if the machine has Internet access).   
Click the Detected Errors Summary tab to see the previous results.





### Task 2: View forest-wide replication health
In this task, you will use repadmin.exe to display the AD replication results for the Contoso environment, and output it to a CSV file for later analysis.   
Use Win8Client to perform the following task.

#### :warning: ALERT
Repadmin /?:showrepl  
"Displays the replication status when specified domain controller last attempted to inbound replicate Active Directory partitions.  
  
Status is reported for each source DC that the destination has an inbound connection object from, grouped by partition. SHOWREPL helps administrators understand the replication topology and replication failures.  
  
The REPADMIN console must have RPC network connectivity to all DC's targeted by the DCLIST parameter."

#### :bulb: KNOWLEDGE
Use the Repadmin /showrepl command to display replication status for one or more DCs specified with the DSA\_LIST parameter. Use**Repadmin /listhelp** from a command prompt, or see this section in the appendix for details about DSA\_LIST options.  
**Repadmin /showrepl usage examples:**  
• Return replication status for DC1:  
Repadmin /showrepl DC1  
• Return replication status for all DCs that reside in the Boulder site:  
Repadmin /showrepl site:Boulder  
• Return replication status for all DCs in the forest and output to a CSV format into a file called showrepl.csv:  
Repadmin /showrepl \* /csv >showrepl.csv




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_Win8Client



### Collect Replication Data
Open a command prompt and type the following command, and then press ENTER:   
repadmin /showrepl \* /csv >showrepl.csv



#### :calling: COMMAND TypeText
```
repadmin /showrepl * /csv >showrepl.csv
```


### Confirm Results
Take note of any errors reported on-screen. You will typically see an LDAP error 81 for any DC the tool is unable to collect replication results. Since two LDAP errors are displayed on screen, we failed to collect data from two DCs. (this is due to an issue you will resolve in Exercise 3)





### Open the results file in Excel
At the command prompt, type showrepl.csv to open the showrepl.csv file in Microsoft Excel. If you are prompted to activate Office, click Cancel.  
  
Within Microsoft Excel: from the Home menu, click Format as Table in the Styles section and click any of the table designs.





### Simplify the data and review
Hide column A and column G, by right clicking the column headers and select Hide  
Reduce the width of other columns so that column K, Last Failure Status is visible.  
In the Last Failure Time column, click the down arrow and deselect 0  
This filters the spreadsheet so just the replication errors are displayed.

#### :bulb: KNOWLEDGE
Repldiag.exe (available from CodePlex) can also be used to create an Excel importable XML file with this information.





### Use the spreadsheet to answer the questions below

**What replication errors are present?**  
(Use column K)  
**When was replication last successful?**  
(Use column J)






### Task 3: Use PowerShell and RepAdmin
In this task, you will get replication status with repadmin and display it using PowerShell. This eliminates the need to use Microsoft Excel to display and filter the results.  
  
Use DC1 to perform the following task.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Open PowerShell and RedAdmin
1. Open a PowerShell prompt and type the following commands, and then press ENTER:  
Repadmin /showrepl \* /csv | convertfrom-csv | out-gridview



#### :calling: COMMAND TypeText
```
Repadmin /showrepl * /csv | convertfrom-csv | out-gridview
```


### Filter the results
Select **Add criteria** and check **Last Failure Status**. Select **Add**.  
  
From the "and Last Failure Status contains" filter criteria, select the blue-underlined word "contains" and select "does not equal". Type 0 in the text box.

#### :bulb: KNOWLEDGE
It is a good idea to view an unfiltered report initially to see both what is working and not working. To filter the output to just replication errors:

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/611516.jpg





### Review the results and answer the questions below
When did DC1 last successfully replicate the ROOT partition from DC2?  
  
What impact do the current AD replication failures on DC1 have on the environment?






# Troubleshoot and resolve AD replication error -2146893022
>LODSProperties
>* IntroductionUri = https://lodmanuals.blob.core.windows.net/manuals/Ignite 2016/Videos/IDL3089/Exercise 2- Troubleshoot and Resolve AD Replication Error 2146893022 (Safety).mp4

## INTRODUCTION MESSAGE
Why is time accuracy important to Active Directory? It's not just about Kerberos and authentication.  
AD replication errors -2146893022, 8614 and 8606 routinely go hand in hand. When all are seen together \)or at least the first two), it is a good indicator that the domain controller's time changed by greater than tombstone lifetime.  
Consider:  
• Machine account passwords change every 30 days by default  
• Tombstone Lifetime is usually 60 or 180 days  
  
Pay attention to:  
• DCs without time safeguards in place  
• Virtualized DC guests configured to sync time with their hosts  
  
For more info: see "Fixing When Your Domain Traveled Back In Time, the Great System Time Rollback to the Year 2000" article linked to in the References section.
## COMPLETION MESSAGE

### Task 1: View symptoms of the problem
In this task you will use repadmin and the system event log to identify symptoms of time problems on your domain controllers





### View the error in repadmin results
Use one of the methods from exercise 1 to review the replication status for DC1. Verify the last replication status result from DC2 lists error -2146893022 / the target principal name is incorrect.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/611519.JPG





### Test LDAP Connectivity from DC2
From DC2, attempt to use repadmin to bind to DC1:  
Repadmin /bind DC1  
  
What error is displayed?  
"LDAP Error 82\)0x52): Local Error…"



#### :calling: COMMAND TypeText
```
Repadmin /bind DC1
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC2



### Attempt to force AD Replication
Attempt to initiate AD replication from DC1 to DC2 using repadmin:  
Repadmin /replicate dc2 dc1 "dc=root,dc=contoso,dc=com"  
  
You should see an error like the following:  
DsReplicaSync\)) failed with status -2146893022 \)0x80090322):  
 The target principal name is incorrect  
  


#### :bulb: KNOWLEDGE
Repadmin /replicate syntax:  
repadmin /replicate <Dest\_DCs> <Source DC> <Naming Context> \[/force\] \[/async\] \[/full\] \[/addref\] \[/readonly\]



#### :calling: COMMAND TypeText
```
Repadmin /replicate dc2 dc1 "dc=root,dc=contoso,dc=com"
```


### View the error in the system event log
On DC2, open up the event viewer \)eventvwr.msc), and review the system event log for event ID 4. You can also see this event via Server Manager's Local Server node, EVENTS pane on DC2.

#### :bulb: KNOWLEDGE
Among other things, the text of event ID 4 indicates the problem can be caused by, "the target service account password is different than what is configured on the Kerberos Key Distribution Center for that target service".  
For this scenario, that means:  
• DC1s computer account password is different than the password stored in AD for DC1 on the KDC \)DC2)

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/611694.jpg




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC2



### Task 2: Determine the cause of the failure
In this task, you will use repadmin /showobjmeta to determine if DC1's computer account password matches what is stored on DC2.   
  
Perform this task on DC1

#### :bulb: KNOWLEDGE
How you can quickly identify if attributes on a given object are the same on a given set of DCs: repadmin /showobjmeta  
The /showobjmeta parameter is used to display the replication metadata for a given object. If the attribute values of a given object are in-sync amongst DCs, the version information reported in the output will be the same when compared with the replication metadata from other DCs.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Open a command prompt on DC1
Open a command prompt on DC1





### Retrieve replication metadata part 1

Obtain replication metadata of the source DCs computer object from both DCs. This requires two separate commands.   
Type the below command into the command prompt and then press Enter. You will enter the second command in the next step.  
  
Repadmin /showobjmeta dc1 "cn=dc1,ou=domain controllers,dc=root,dc=contoso,dc=com" >dc1objmeta.txt






#### :calling: COMMAND TypeText
```
Repadmin /showobjmeta dc1 "cn=dc1,ou=domain controllers,dc=root,dc=contoso,dc=com" >dc1objmeta.txt
Repadmin /showobjmeta dc2 "cn=dc1,ou=domain controllers,dc=root,dc=contoso,dc=com" >>dc1objmeta.txt
```


### Retrieve replication metadata part 2
This is the second command required to obtain replication metadata of the source DCs computer object from both DCs.   
Type the below command into the command prompt and then press Enter.   
  
  
Repadmin /showobjmeta dc2 "cn=dc1,ou=domain controllers,dc=root,dc=contoso,dc=com" >>dc1objmeta.txt



#### :calling: COMMAND TypeText
```
Repadmin /showobjmeta dc2 "cn=dc1,ou=domain controllers,dc=root,dc=contoso,dc=com" >>dc1objmeta.txt
```


### Review the object metadata
Open up the dc1objmeta.txt file with Notepad.exe and observe the version number differences for the password related attributes: \)dBCSPwd, UnicodePWD, NtPwdHistory, PwdLastSet and lmPwdHistory). The version number is the second to last column with the heading of "Ver".  
  
The replication metadata reveals that DC2 has old password information for DC1. The Kerberos operation failed because DC1 was unable to decrypt the Service Ticket presented by DC2. Review the Kerberos details section for Exercise 2 in the appendix for more information.

#### :warning: ALERT
The version numbers that are displayed may not match the numbers in the attached chart. The important point is that the two numbers are different from each other.

#### :bulb: KNOWLEDGE
The version numbers that are displayed may not match the numbers in the chart above. The important point is that the two numbers are different from each other.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/612646.JPG





### Task 3: Resolve the failure
The KDC running on DC2 cannot be used for Kerberos operations with DC1 since DC2 has old password information for DC1. In this task, you will force DC2 to use the KDC on DC1 so that the replication operation will complete.  
  
Perform this task on DC2.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC2



### Stop the Kerberos KDC Service
From DC2 stop the Kerberos Key Distribution Center service:  
  
Net stop kdc



#### :calling: COMMAND TypeText
```
Net stop kdc
```


### Initiate root partition replication
Initiate replication of the Root partition using repadmin  
  
Repadmin /replicate dc2 dc1 "dc=root,dc=contoso,dc=com"

#### :warning: ALERT
If replication still fails, clear any cached Kerberos tickets for the Local System account on DC2 using the klist.exe utility from a command prompt: Klist.exe -li 0x3e7 purge  
\)The logon ID for Local System is 0x3e7)

#### :bulb: KNOWLEDGE
If replication of the root partition was successful, DC2 now has the updated password information for DC1, so DC2 can now be used for KDC operations. You can confirm this if you like by running the two repadmin /showobjmeta commands from task 2.



#### :calling: COMMAND TypeText
```
Repadmin /replicate dc2 dc1 "dc=root,dc=contoso,dc=com"
```


### Start the KDC service on DC2
Start the Kerberos Key Distribution Center service on DC2.  
  
Net start kdc

#### :bulb: KNOWLEDGE
An alternative method to resolve this issue:  
In this exercise, we simply stop the KDC to force DC2 to use DC1 as the KDC. We know this will work because DC1 has the updated password. This is a small environment, so doing the same thing in a larger environment may not be an option since we cannot control what new KDC will be contacted. If all other KDCs are up to date, this will work. Another method to resolve this is to reset DC1's computer account password manually and specify DC2 as the DC to update with the information. The following command will cause DC1 to reset its computer account password and update DC2 with this information:  
On DC1 open a command prompt and enter the following command:  
Netdom resetpwd /server:192.168.10.2 /userd:root\\administrator /passwordd:adrepl123!  
The command instructs DC1 to change its password and tell the DC specified with the /server parameter the new password.



#### :calling: COMMAND TypeText
```
Net start kdc
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC2




# Troubleshoot and resolve AD replication error 1908
>LODSProperties
>* IntroductionUri = https://lodmanuals.blob.core.windows.net/manuals/Ignite 2016/Videos/IDL3089/Exercise 3- Troubleshoot and Resolve AD Replication Error 1908.mp4

## INTRODUCTION MESSAGE
**1908 | Could not find the domain controller for this domain.**  
In this exercise, you will review the netlogon.log to determine the cause of AD replication error 1908. Nltest.exe will be used to enable verbose Netlogon logging as well as exercising the DC locator function of Netlogon. You will then use dcdiag.exe and the DNS management snap-in to further diagnose and resolve the problem.  
  
**Scenario**  
• DC1, DC2 and TRDC1 are failing to replicate from ChildDC1 with error 1908 "Could not find the domain controller for this domain".   
o This error is returned when there is a failure by Netlogon to locate a DC that is advertising the KDC flag.  
Perform the following steps from DC1.
## COMPLETION MESSAGE

### Task 1: Determine the cause of the failure
Perform these tasks on DC1.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Enable verbose logging for netlogon
Perform this task on DC1.  
  
Enable verbose logging for Netlogon by executing the following command on DC1:  
Nltest /dbflag:2080ffff  
  
This enables additional detail to be logged to the Netlogon.log file located in the C:\\Windows\\Debug directory.



#### :calling: COMMAND TypeText
```
Nltest /dbflag:2080ffff
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Initiate replication from ChildDC1
On DC1, initiate replication from ChildDC1 the the below command and press Enter:  
repadmin /replicate dc1 childdc1 "dc=child,dc=root,dc=contoso,dc=com"  
  
Results should look similar to this:  
DSReplicaSync\)) failed with status 1908 \)0x774):  
 Could not find the domain controller for this domain.



#### :calling: COMMAND TypeText
```
repadmin /replicate dc1 childdc1 "dc=child,dc=root,dc=contoso,dc=com"
```


### Test KDC Connectivity
From DC1, test Netlogon's ability to locate a KDC in the child.root.contoso.com domain  
Nltest /dsgetdc:child /kdc  
  
Expected output as below:   
Getting DC name failed: Status = 1355 0x54b ERROR\_NO\_SUCH\_DOMAIN



#### :calling: COMMAND TypeText
```
Nltest /dsgetdc:child /kdc
```


### Check if any DCs are available in the child domain
Execute the same command without the /KDC option to see if Netlogon can find any DC in the child domain  
Nltest /dsgetdc:child  
Output should look similar to this:  
"Getting DC name failed: Status = 1355 0x54b ERROR\_NO\_SUCH\_DOMAIN"



#### :calling: COMMAND TypeText
```
Nltest /dsgetdc:child
```


### Review first nltest results
Open up the Netlogon.log file in \)C:\\Windows\\Debug\\Netlogon.log), and go to the bottom of the file to see the most recent entries.  
  
Look for the entry that begins with DSGetDcName function called.   
There will be multiple similar entries. Find the entry in the log that has the same parameters you specified in the first nltest command. \)Dom:child and Flags: KDC)   
If you have trouble locating the call, place your cursor on the last line of text and search up for KDC.  
   
  
The entry will look similar to the following:   
DsGetDcName function called: client PID=2176, Dom:child Acct:\)null) Flags: KDC  
View the results of the operation \)everything with the same thread ID) that follows. The last entry will begin with DsGetDcName function returns.

#### :bulb: KNOWLEDGE
There will be multiple similar entries. Find the entry in the log that has the same parameters you specified in the first nltest command. \)Dom:child and Flags: KDC)   
If you have trouble locating the call, place your cursor on the last line of text and search up for KDC.  
  
  
The entry will look similar to the following:   
DsGetDcName function called: client PID=2176, Dom:child Acct:\)null) Flags: KDC  
View the results of the operation \)everything with the same thread ID) that follows. The last entry will begin with DsGetDcName function returns.  
  
In the Netlogon log output, you can see a DNS lookup failure for a KDC SRV record in the Child domain.  
How do the domain controllers in root.contoso.com resolve names for the Child domain? \)Open up the DNS management snap-in in order to determine the answer)  
  
  
Can you ping child.root.contoso.com?

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/612667.JPG





### Test the DNS delegation settings
Test the DNS delegation settings with Dcdiag.exe  
dcdiag /test:dns /dnsdelegation >dnstest.txt  
  
Open up dnstest.txt to see the results of the DNS delegation test.

#### :bulb: KNOWLEDGE
Are there any failures reported for the DNS delegation test?   
  
What is the cause of the failure to locate a KDC in the child domain?





### Task 2: Resolve the AD replication failure
In this task, you will fix the broken DNS delegation for the child domain.  
Perform this task on DC1.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Select the affected DNS domain zone
Open up the DNS management snap-in \)dnsmgmt.msc)  
  
Expand Forward Lookup Zones, expand root.contoso.com and select child





### Remove the incorrect NS record
Open up the properties of the \)same as parent folder) NS record  
  
Select the entry for lamedc1.child.contoso.com and then select Remove





### Add the correct NS record
Select Add  
  
In the Server fully qualified domain name \)FQDN) text box, type: childdc1.child.root.contoso.com   
  
In the IP Addresses of this NS record section, type the IP address of ChildDC1: 192.168.10.11  
  
Select OK and then select OK again. Select Yes to the dialogue window that opens up asking if you want to delete the glue record lamedc1.child.contoso.com 192.168.10.1\]





### Test KDC location with nltest
Use nltest to verify we are able to locate a KDC in the child domain.  
Nltest /dsgetdc:child /kdc /force  
  
The /force option is used to ensure the Netlogon cache is not used.



#### :calling: COMMAND TypeText
```
Nltest /dsgetdc:child /kdc /force
```


### Test AD replication
Initiate replication using repadmin:  
Repadmin /replicate dc1 childdc1 "dc=child,dc=root,dc=contoso,dc=com"

#### :warning: ALERT
Use repadmin /syncall to quickly initiate AD replication in this lab environment:  
Synchronizes a specified domain controller with all replication partners.  
 By default, if no directory partition is provided in the naming context  
 parameter, the command performs its operations on the configuration  
 directory partition.  
\[SYNTAX\]  
/syncall <DSA> \[<Naming Context>\] \[<flags>\]  
  
The following flags are supported  
/a Abort if any server is unavailable.  
/A Sync all naming contexts which are held on the home server.  
/d Identify servers by distinguished name in messages.  
/e Enterprise, cross sites.  
/h Print this help screen.  
/i Iterate indefinitely.  
/I Perform showreps on each server pair in path instead of syncing.  
/j Synchronize adjacent servers only.  
/p Pause for possible user abort after every message.  
/P Push changes outward from home server.  
/q Run in quiet mode, suppress call back messages.  
/Q Run in very quiet mode, report fatal errors only.  
/s Do not synchronize.  
/S Skip initial server response check.  
  
By default /syncall does not cross site boundaries  
  
Lab specific examples  
Synchronizes the target dc with all its partners, all partitions including ones cross-site, displaying the partners by DN rather than GUID.  
  
repadmin /syncall DC1 /Aed  
repadmin /syncall DC2 /Aed  
repadmin /syncall ChildDC1 /Aed  
repadmin /syncall ChildDC2 /Aed  
repadmin /syncall TRDC1 /Aed

#### :bulb: KNOWLEDGE
Initiate replication on DC1 from CHILDDC1 using Active Directory Sites and Services:  
a. Open up Active Directory Sites and Services \)dssite.msc)  
b. Expand Sites, Boulder, Servers and DC1  
c. Select DC1's NTDS Settings object  
d. Right click the <automatically generated> connection object from server CHILDDC1 and choose Replicate Now  
e. Expand DC2's server object and initiate replication from CHILDDC1  
f. Expand TRDC1's server object and initiate replication on TRDC1 from CHILDDC1

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/612676.jpg



#### :calling: COMMAND TypeText
```
Repadmin /replicate dc1 childdc1 "dc=child,dc=root,dc=contoso,dc=com"
```


### Use repadmin /syncall to quickly initiate AD repli
Use repadmin /syncall to quickly initiate AD replication in the lab environment.    
  
repadmin /syncall DC1 /Aed  
repadmin /syncall DC2 /Aed  
repadmin /syncall ChildDC1 /Aed  
repadmin /syncall ChildDC2 /Aed  
repadmin /syncall TRDC1 /Aed  
  
   
  
We initiate replication manually after correcting an error so we can see verify the issue is resolved.



#### :calling: COMMAND TypeText
```
repadmin /syncall DC1 /Aed
repadmin /syncall DC2 /Aed
repadmin /syncall ChildDC1 /Aed
repadmin /syncall ChildDC2 /Aed
repadmin /syncall TRDC1 /Aed
```


### Refresh forest-wide replication status
Switch to Win8Client  
  
Refresh the forest-wide replication status using adreplstatus or repadmin.   
  
If using adreplstatus, ensure you click the Configuration/Scope Settings tab and then check Re-Discover Environment before refreshing the replication status. This is required, because the broken DNS delegation caused an error in the tool's initial discovery of the Child domain.

#### :warning: ALERT
It may take five minutes or more for ChildDC1 errors to stop appearing when refreshing replication status using the AD Replication Status tool.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_Win8Client



### Observe the replicastion status
Observe that you now have replication status from the child domain DCs. At this point, the only replication error displayed is error 8606.   
  
If errors other than 8606 or 8614 are displayed:  
Ensure you have initiated AD replication from DCs where it was failing before, and then run repadmin /showrepl or adreplstatus again

#### :bulb: KNOWLEDGE
Note that error 1256 is no longer displayed. This was cleared up by resolving the first set of errors. Error 1256 is logged when the replication task is cancelled after a failure to replicate the Schema, configuration, or domain partitions for other replication errors. For this reason, you should always troubleshoot the error that led to the replication task being cancelled \)do not bother troubleshooting 1256).






# Troubleshoot and Resolve AD Replication Error 8606
>LODSProperties
>* IntroductionUri = https://lodmanuals.blob.core.windows.net/manuals/Ignite 2016/Videos/IDL3089/Exercise 4- Troubleshoot and Resolve AD Replication Error 8606.mp4

## INTRODUCTION MESSAGE
**8606 | Insufficient attributes were given to create an object**  
In this exercise, you will use repadmin.exe to identify lingering objects. You will then use repadmin or repldiag.exe to remove lingering objects from the Contoso forest and resolve AD replication error 8606 in the process. This replication status is an indication that one or more lingering objects exist on the source DC.   
  
 **Tip:** This section is jargon intense, a Lingering Object Glossary is provided in the Appendix for your reference.  
  
**Lingering object:** An object that is present on one DC, but has been deleted and garbage collected on one or more DCs. Error 8606 is logged when the source DC sends an update of one or more attributes for an object that does not exist on the destination DC.
## COMPLETION MESSAGE

### Review the Scenario
Read the scenario below, then read the knowledge entry and review the screenshots to provide context for this exercise.  
  
Scenario:  
• AD replication of the Root partition from DC2 to DC1 fails with error, "Insufficient attributes were given to create an object"  
.• AD replication of the Root partition from TRDC1 to other GCs hosting a read-only copy of the partition fail with the same error.  
• DC2 and TRDC1 are DCs that have at least one lingering object in the root.contoso.com partition  
• DC1 reports error 8606 replicating from DC2

#### :bulb: KNOWLEDGE
There are many methods to remove lingering objects. This exercise presents the two most common:   
• Repldiag /removelingeringobjects  
• Repadmin /removelingeringobjects  
Other methods are listed in the appendix.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613851.jpg
>* ShowAutomatically = No





### Task 1: Lingering object symptoms
Perform this task on DC1  
  
Manually initiate replication between DC1 and DC2 \)have DC1 pull from DC2):  
Repadmin /replicate dc1 dc2 "dc=root,dc=contoso,dc=com"  
  


#### :warning: ALERT
Replication fails with the following error:  
DsReplicaSync\)) failed with status 8606 \)0x219e):Insufficient attributes were given to create an object. This object may not exist because it may have been deleted and already garbage collected.  
  
Event 1988 is logged in the Directory Service event log.

#### :bulb: KNOWLEDGE
AD replication status 8606 and event ID 1988 are good indicators of lingering objects \)when the DCs are configured for Strict Replication Consistency). It is important to note, however, that AD replication may complete successfully \)and not log an error) from a DC containing lingering objects since replication is based on changes. If there are no changes to any of the lingering objects, there is no reason to replicate them and they will continue to exist. For this reason, when cleaning up lingering objects, do not just clean up the DCs logging the errors; instead, assume that all DCs may contain them, and clean them up as well.



#### :calling: COMMAND TypeText
```
Repadmin /replicate dc1 dc2 “dc=root,dc=contoso,dc=com”
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Find Error 1988 in the Directory Services log
Review the Directory Services event log on DC1 for event 1988 using event viewer \)eventvwr.msc) or PowerShell  
  
Get-WinEvent -LogName "Directory Service" -MaxEvents 5 | fl

#### :bulb: KNOWLEDGE
Event 1988 only reports the first lingering object encountered during the replication attempt. There are usually many more lingering objects present on the source DC. Use repadmin /removelingeringobjects with the /advisory\_mode switch to have all lingering objects reported.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613853.jpg



#### :calling: COMMAND TypeText
```
Get-WinEvent -LogName "Directory Service" -MaxEvents 5 | fl
```


### Review the details of event 1988.
3. Identify the following from event 1988 \)they are needed later in the exercise):  
• Object GUID  
• Source DC  
• Partition DN

#### :bulb: KNOWLEDGE
How can you translate the DNS alias provided in the event to the host name of the DC?





### Task 2: Lingering Object analysis
In this task, you will use repadmin to return replication metadata for the lingering object identified in event ID 1988. The repadmin output will allow you to identify DCs containing the lingering object reported in the event.  
  
Perform this task on DC1 and DC2.




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_DC1



### Obtain the ObjectGUID
Obtain the ObjectGUID reported in the event on DC1. \)see the attached screenshot for location of ObjectGUID)

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613856.jpg





### Find all copies of this object
Identify all DCs that have a copy of this object using repadmin /showobjmeta  
Repadmin /showobjmeta \* "<GUID=5ca6ebca-d34c-4f60-b79c-e8bd5af127d8>" >obj.txt



#### :calling: COMMAND TypeText
```
Repadmin /showobjmeta * "<GUID=5ca6ebca-d34c-4f60-b79c-e8bd5af127d8>" >obj.txt
```


### Review the output
Open obj.txt. Any DC that returns replication metadata for this object are DCs containing one or more lingering objects. DCs that do not have a copy of the object report status 8439, "The distinguished name specified for this replication operation is invalid".  
  
Which DCs return replication metadata for the object?

#### :bulb: KNOWLEDGE
This is a good method to conduct a quick spot check of DCs containing the lingering object reported in the event. It is NOT a good method to discover all lingering objects. For more information, see the Lingering Object discovery section of the appendix.





### Obtain DC1's DSA ObjectGUID
Obtain the DSA object GUID on DC1  
Repadmin /showrepl DC1 >showrepl.txt  
  
The DSA object GUID is at the top of the output and will look like this:  
DSA object GUID: 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e

#### :bulb: KNOWLEDGE
In order to use the /removelingeringobjects command you need to know three things:  
1. You need to know which DCs contain lingering objects  
2. Which partition the lingering object resides in  
3. The DSA Object GUID of a good reference DC that hosts that partition that does not contain lingering objects





### Verify lingering objects on DC2
In the following command, you will verify the existence of lingering objects on DC2 by comparing its copy of the ROOT partition with DC1.  
  
Run the following repadmin command \)ensure you use the /advisory\_mode parameter)  
Repadmin /removelingeringobjects DC2 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com" /Advisory\_Mode



#### :calling: COMMAND TypeText
```
Repadmin /removelingeringobjects DC2 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com" /Advisory_Mode
```


### Review the results of in the EventLog
Review the Directory Service event log on DC2. If there are any lingering objects present, each one will be reported in its own event ID 1946. The total count of lingering objects for the partition checked is reported in event 1942.





### Task 3: Remove lingering objects
In this task, you will remove the lingering objects using either repldiag or repadmin.  
  
You will run commands to remove lingering objects from all partitions even though only one lingering object was discovered in the prior task.  
  
Perform this task on Win8Client and ChildDC2.

#### :warning: ALERT
When lingering objects are discovered, assume they are present on all DCs in all partitions. Do not just clean up the DCs reporting the errors. Repldiag automates the majority of the cleanup work. See the Lingering Object discovery and cleanup section in the appendix for more information.

#### :bulb: KNOWLEDGE
• Repldiag requires a well-connected topology. It will fail to run in environments that suffer from poor network connectivity \*.  
• Always check for the latest version on CodePlex:  
http://activedirectoryutils.codeplex.com/  
\* There is a hidden parameter that allows the tool to continue in spite of topology issues, but do not use it without recognizing the ramifications: Use of the /BypassStabilityCheck parameter will likely result in a failure to fully clean up the environment.





### Run Repldiag
The following command will check for and remove lingering objects from all DCs for all partitions \)except Schema)  
  
From Win8Client, run the following from an elevated command prompt  
Repldiag /removelingeringobjects



#### :calling: COMMAND TypeText
```
Repldiag /removelingeringobjects
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_Win8Client



### Verify successful completion
2. Verify that AD replication completes successfully. \)error 8606 is no longer logged)  
repadmin /replicate dc1 dc2 "dc=root,dc=contoso,dc=com"



#### :calling: COMMAND TypeText
```
repadmin /replicate dc1 dc2 "dc=root,dc=contoso,dc=com"
```


### Confirm the objects were deleted
Rerun the repadmin /showobjmeta command executed in Task 2 to see if the object was removed from all DCs  
Repadmin /showobjmeta \* "<GUID=5ca6ebca-d34c-4f60-b79c-e8bd5af127d8>" >obj.txt  
  
Notice the RODC in the child domain still contains the object.

#### :warning: ALERT
At the time of this writing, Replidag \)v 2.0.4947.18978) does not remove lingering objects from RODCs. \)It was developed prior to the existence of RODCs.) This functionality has been requested.



#### :calling: COMMAND TypeText
```
Repadmin /showobjmeta * "<GUID=5ca6ebca-d34c-4f60-b79c-e8bd5af127d8>" >obj.txt
```


### Remove the lingering objects from RODCs
Run the following command to clean up the RODC \)childdc2).  
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com"



#### :calling: COMMAND TypeText
```
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com"
```


### Review the results
Review the Directory Service event log on ChildDC2 for the results of the lingering object removal request. Since this server runs a minimal server interface, you will need to open Event Viewer from the command prompt by typing eventvwr.msc.  
  
• Review the details of event ID 1939, which reports the status of the lingering object removal process.

#### :bulb: KNOWLEDGE
If this were a production environment, you would also run the repadmin /removelingeringobjects command for the remaining partitions on the RODC\)s).

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613867.jpg





#### :bulb: KNOWLEDGE###
These are the commands that repldiag automates for you:
Repadmin /removelingeringobjects childdc1.child.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "cn=configuration,dc=root,dc=contoso,dc=com"  

Repadmin /removelingeringobjects childdc1.child.root.contoso.com 3fe45b7f-e6b1-42b1-bcf4-2561c38cc3a6 "cn=configuration,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects childdc1.child.root.contoso.com 0b457f73-96a4-429b-ba81-1a3e0f51c848 "cn=configuration,dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects childdc1.child.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=forestdnszones,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects childdc1.child.root.contoso.com 3fe45b7f-e6b1-42b1-bcf4-2561c38cc3a6 "dc=forestdnszones,dc=root,dc=contoso,dc=com"  
 
Repadmin /removelingeringobjects childdc1.child.root.contoso.com 0b457f73-96a4-429b-ba81-1a3e0f51c848 "dc=forestdnszones,dc=root,dc=contoso,dc=com"

repadmin /removelingeringobjects dc1.root.contoso.com 3fe45b7f-e6b1-42b1-bcf4-2561c38cc3a6 "dc=root,dc=contoso,dc=com"

repadmin /removelingeringobjects dc1.root.contoso.com 3fe45b7f-e6b1-42b1-bcf4-2561c38cc3a6 "dc=domaindnszones,dc=root,dc=contoso,dc=com"

You do not need to clean up reference DCs for the Child, TreeRoot or their DomainDNSZones partitions. This is because there is only one DC in each domain that hosts a writable copy of the partition. The schema partition is not checked or cleaned up because you cannot delete objects from the schema.

Now that the reference DCs are cleaned up. Clean up all remaining DCs against the reference DCs

Repadmin /removelingeringobjects dc1.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "cn=configuration,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects dc2.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "cn=configuration,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "cn=configuration,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects trdc1.treeroot.fabrikam.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "cn=configuration,dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects dc1.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=forestdnszones,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects dc2.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=forestdnszones,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=forestdnszones,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects trdc1.treeroot.fabrikam.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=forestdnszones,dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects childdc1.child.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects dc2.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects trdc1.treeroot.fabrikam.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects dc2.root.contoso.com 70ff33ce-2f41-4bf4-b7ca-7fa71d4ca13e "dc=domaindnszones,dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects dc1.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=child,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects dc2.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=child,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=child,dc=root,dc=contoso,dc=com"  
  
Repadmin /removelingeringobjects trdc1.treeroot.fabrikam.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=child,dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects childdc2.child.root.contoso.com 0c559ee4-0adc-42a7-8668-e34480f9e604 "dc=domaindnszones,dc=child,dc=root,dc=contoso,dc=com"

Repadmin /removelingeringobjects childdc1.child.root.contoso.com 0b457f73-96a4-429b-ba81-1a3e0f51c848 "dc=treeroot,dc=fabrikam,dc=com"  
  
Repadmin /removelingeringobjects childdc2.child.root.contoso.com 0b457f73-96a4-429b-ba81-1a3e0f51c848 "dc=treeroot,dc=fabrikam,dc=com"  
  
Repadmin /removelingeringobjects dc1.root.contoso.com 0b457f73-96a4-429b-ba81-1a3e0f51c848 "dc=treeroot,dc=fabrikam,dc=com"  
  
Repadmin /removelingeringobjects dc2.root.contoso.com 0b457f73-96a4-429b-ba81-1a3e0f51c848 "dc=treeroot,dc=fabrikam,dc=com"






# Troubleshoot and resolve AD replication error 8453
>LODSProperties
>* IntroductionUri = https://lodmanuals.blob.core.windows.net/manuals/Ignite 2016/Videos/IDL3089/Exercise 5- Troubleshoot and Resolve AD Replication Error 8453.mp4

## INTRODUCTION MESSAGE
**8453 | "Replication access was denied"**  
  
In this exercise, you will use repadmin and the Directory Service event log to see the symptoms of AD replication error 8453. A special DCDIAG test is used to identify the cause of the failure. You will then use ADSIEdit.msc to resolve the problem.  
  
Scenario  
• There is an RODC in the Child domain ChildDC2 that is not advertising as a global catalog server.
## COMPLETION MESSAGE
The next exercise is optional and the lab environment likely does not have the issue.Please click the "Exit Lab Environment" button once you have completed all exercises to your liking.   
  
Ensure you save a copy of the manual for later reference and please take the the survey as it won't be available at a later time, and we really value your feedback.
### Task 1: Symptoms of error 8453
Perform the following task on ChildDC2.  
  
From an elevated prompt run:  
Repadmin /showrepl childdc2 >repl.txt



#### :calling: COMMAND TypeText
```
Repadmin /showrepl childdc2 >repl.txt
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_ChildDC2



### Review the results
Review the repl.txt file to see the symptoms of this issue.

#### :bulb: KNOWLEDGE
a. Notice at the top of the output that this DC is failing to advertise as GC.  
Boulder\\CHILDDC2   
DSA Options: IS\_GC DISABLE\_OUTBOUND\_REPL IS\_RODC   
 WARNING: Not advertising as a global catalog.  
  
b. The DC=treeroot,DC=fabrikam,DC=com partition is missing from the inbound neighbors section \)as it is not replicated from any DC).  
  
c. The bottom of the output reveals that it is unable add a replication link for the TreeRoot partition with error 8453, "Replication access was denied."  
Source: Boulder\\TRDC1   
\*\*\*\*\*\*\* 1 CONSECUTIVE FAILURES since 2014-01-14 15:37:34   
Last error: 8453 \)0x2105): Replication access was denied.   
Naming Context: DC=treeroot,DC=fabrikam,DC=com





### Review Event Log entries on ChildDC2
Review the Directory Service event log on ChildDC2 for event 1926. It shows the partition we failed to establish a replication link with and the error in the Additional Data section.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613884.jpg





### Run DCDIAG Checks on ChildDC2
From ChildDC2 run the DCDIAG test that checks for security related problems:  
Dcdiag /test:checksecurityerror  
  
As reported in the output, error 8453 is because the Enterprise Read-only Domain Controllers security group does not have the "Replicating Directory Changes" permission.

#### :bulb: KNOWLEDGE
This access control entry is added during the RODCPREP process. Sometimes the infrastructure master for a given domain is not available when the command executes, and the entry is never added. RODCs must have this permission in order to replicate the partition.  
  
Another common scenario where you encounter this error:  
  
An administrator manually initiates AD replication: Error 8453 is displayed when attempting to initiate replication from a DC in another domain for which we do not have the "replicating directory changes" control access right. This is not actually an AD replication failure; it is just a failure on the Admin initiated operation. The Enterprise Administrators group has this right by default, and will not see this error.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613885.JPG



#### :calling: COMMAND TypeText
```
Dcdiag /test:checksecurityerror
```


### Task 2: Resolve error 8453
In this task, you will add the missing access control entry to the TreeRoot partition.





### Open ADSIEDIt
On TRDC1, Open up ADSIEDIT \)ADSIEdit.msc) and connect to the Default Naming Context.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613887.jpg




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_TRDC1



### Open the properties of the domain
Right-click DC=treeroot,DC=fabrikam,DC=com, and then click Properties.  
  
Select the Security tab.





### Review the permissions on the partition.
Review the permissions on this partition. Notice there are no entries for the Enterprise Read-Only Domain Controllers group.





### Add RODCs to the Access Control List
Click Add.  
  
In the Enter the object names to select box, type:   
ROOT\\Enterprise Read-only Domain Controllers  
  
Click the Check Names button and then choose OK if the object picker resolves the name.





### Set permissions for the RODCs
In the Permissions for Enterprise Read-only Domain Controllers dialog box, clear the Allow boxes that are automatically checked:  
• Read  
• Read domain password & lockout policies  
• Read Other domain parameters  
  
Select the Allow box next to "Replicating Directory Changes" and then click OK.

#### :camera: SCREENSHOT
>LODSProperties
>* Uri = screens/613891.jpg





### Force replication topology rebuild
10. Manually initiate the KCC to immediately recalculate the inbound replication topology on ChildDC2 \)this will force it to attempt to add the TreeRoot partition again).  
  
Repadmin /kcc childdc2



#### :calling: COMMAND TypeText
```
Repadmin /kcc childdc2
```

#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_ChildDC2



### Verify replication was successful.
Verify ChildDC2 is able to replicate the TreeRoot partition.

#### :bulb: KNOWLEDGE
The next exercise is optional and the lab environment likely does not have the issue.  
Please click the "Exit Lab Environment" button once you have completed all exercises to your liking. Ensure you save a copy of the manual for later reference and please take the the survey as it won't be available at a later time, and we really value your feedback.






# OPTIONAL: Troubleshoot and resolve AD replication error 8614
>LODSProperties
>* IntroductionUri = https://lodmanuals.blob.core.windows.net/manuals/Ignite 2016/Videos/IDL3089/Exercise 6- Troubleshoot and Resolve AD Replication Error 8614.mp4

## INTRODUCTION MESSAGE
8614 | The directory service cannot replicate with this server because the time since the last replication with this server has exceeded the tombstone lifetime.  
  
Error 8614 is logged when a destination DC has not replicated with a source DC over an existing replication connection for longer than tombstone lifetime.   
  
In this exercise, you will use repadmin to resolve AD replication error 8614 in a supported manner.   
Perform this exercise from Win8Client.
## COMPLETION MESSAGE

### Check Replication Status
1. Switch to ADREPL\_Win8Client VM.  Run the AD Replication Status tool or repadmin /showrepl \* /csv.   
  
Review the output. If AD replication error 8614 is not present, then do not do this exercise.

#### :warning: ALERT
• This quarantine is put in place on a per-replica, per-partition basis so that replication with an out of date DC does not introduce lingering objects into the environment.   
• If this issue occurs in a production environment, careful consideration should be made prior to removing the replication safeguard.   
• In some cases, forceful demotion of the source DC makes more sense. See the content linked in the appendix for more information.  
• Large jumps in system time \)forward or backward) are common causes of this issue




#### :computer: ACTIONS
>LODSProperties
>* VM = ADREPL_Win8Client



### Set strict replication consistency
Ensure Strict Replication consistency is set on all DCs  
Repadmin /regkey \* \+strict  
  
In the output of the above command, verify status for all DCs: registry key set  
"Strict Replication Consistency" REG\_DWORD 0x0000001 \)1)



#### :calling: COMMAND TypeText
```
Repadmin /regkey * +strict
```


### Remove lingering objects
Remove lingering objects if present using repldiag \)skip if already performed in exercise 4).  
  
Repldiag /removelingeringobjects



#### :calling: COMMAND TypeText
```
Repldiag /removelingeringobjects
```


### Repair DCs that failed to replicate
Run the following command on destination DCs that fail to replicate from source DCs with error 8614: \)replace DestinationDCName with the actual DC name)  
Repadmin /regkey DestinationDCName \+AllowDivergent  
  
In this lab environment, it is safe to just temporarily set the registry value on all DCs  
Repadmin /regkey \* \+AllowDivergent  
  
Verify status from all DCs:  
"Allow Replication With Divergent and Corrupt Partner" REG\_DWORD 0x0000001 \)1)

#### :warning: ALERT
Do not run the following command without first verifying that Strict replication consistency is enabled.



#### :calling: COMMAND TypeText
```
Repadmin /regkey DestinationDCName +AllowDivergent
Repadmin /regkey * +AllowDivergent
```


### Trigger Replication
Initiate replication to all destination DCs from all source DCs where replication failed with status 8614





### Confirm error 8614 is not occurring
Use repadmin /showrepl \* /csv or the AD Replication Status tool to verify error 8614 is no longer logged in the environment



#### :calling: COMMAND TypeText
```
repadmin /showrepl * /csv
```


### Reset replication safeguards
Delete the registry value so that the replication quarantine safeguards are back in place  
Repadmin /regkey \* -AllowDivergent



#### :calling: COMMAND TypeText
```
Repadmin /regkey * -AllowDivergent
```



