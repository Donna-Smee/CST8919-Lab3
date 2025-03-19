

# CST8919 - Lab 3
Donna Ha - 041174159

## Overview of Lab Tasks

## Challenges Encountered

## Task 1-2: Prepare Your Ubuntu Server and Install Grafana

### 1\. Creating an Ubuntu Virtual Machine on Azure: No redundancy, Standard Security, Ubuntu Server 24.04 (Later changed to 22.04)

![][image1]

Use SSH (remember to download key) to connect to the VM later  
![][image2]

![][image3]

Virtual Machine Overview:  
![][image4]

### 2\. Add inbound security rule to the Virtual Machine’s Network Security Group (NSG)

Should have Port 3000 enabled, protocol: TCP, Action: Allow, Priority: 310  
![][image5]

New inbound rule successfully added to NSG.  
![][image6]

### 3\. Connect to the VM using SSH (copy the IP address)

Using Visual Studio code, create a new host using IP address and private key downloaded in previous steps  
![][image7]

Connected  
![][image8]

### 4\. Install Grafana on the VM

sudo apt-get update && sudo apt-get upgrade not working because the size of the VM is too small.  
![][image9]

![][image10]

Changing the size from B1 to B2s  
![][image11]

![][image12]

sudo apt-get update && sudo apt-get upgrade is now working  
![][image13]

Installing Grafana  
![][image14]

sudo systemctl daemon-reload gives an error saying “Grafana.service does not exist”.  
I do the commands one by one this time.  
In this command :  

```
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a
/etc/apt/sources.list.d/grafana.list
``` 
there is an error saying there is no such file or directory for source.list.d/grafana.list.   
To fix the issue, make a directory using the following command:  
``` 
sudo mkdir -p /etc/apt/sources.list.d/
```

![][image15]

The previous wget command was not working, so I switched with this command:  
``` 
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

```

![][image16]

Everything now installs correctly.  
![][image17]

Error: cannot start server because Unit file Grafana.service does not exist  
![][image18]

![][image19]  
(Changed grafana capital G to lower to fix error)

Grafana status:  
![][image20]

![][image21]

Grafana is now running on the VM  
![][image22]

## Task 3: Connect Grafana To Azure Monitor

### 1\. FAILED: Set managed Identity for Grafana data source set up

   
For VM, go to Security \> Identity, then enable (turn status to ON)  
![][image23]

Set roles (not shown because they were incorrect), then restart Grafana server  
![][image24]

Set managed\_identity\_enabled \= true in /etc/grafana/grafana.ini  
![][image25]

![][image26]

Restart server  
![][image27]

![][image28]

![][image29]

Go to add data source on Grafana  
![][image30]

Add Azure Monitor as a data source  
![][image31]

Authentication needed 3 different IDs that were unavailable in the current subscription.  
![][image32]

Tried to enable insights on the VM   
![][image33]

![][image34]

![][image35]

Created default workspace  
![][image36]

Added role **monitoring reader** to monitoring workspace  
![][image37]

![][image38]

![][image39]

Added role to current subscription  
![][image40]

![][image41]

![][image42]

![][image43]

![][image44]

Even with a VM restart, the managed identity authentication did not appear on Grafana.   
![][image45]

### 2\. SUCCESS: Set managed Identity for Grafana data source set up

Note: After switching the CDO subscription, I followed the same steps (minus the role assignment), so I did not re screenshot those steps.

Request quota increase for VM size B2s\_v2  
![][image46]  
New Virtual Machine overview  
![][image47]

Set managed identity to ON  
![][image48]

Go to Microsoft Entra ID to find and copy the necessary IDs that Grafana authentication requires (I never was able to switch to managed identity)

To get the IDs, go to Microsoft Entra ID \> App registrations \> New registration, then create a new registration.   
![][image49]

Once the application is registered, go to the overview page to copy the Directory (tenant) ID, and the Application (client) ID. To get the client secret, go to Certificates and Secrets, click new client secret, and create a new secret. Once added, it will give a client secret value to copy.   
![][image50]

Enter the IDs in the Grafana authentication  
![][image51]

Error: did not work originally because I copied the wrong client secret value, and also it did not have the correct permissions.  
![][image52]

Added **Monitoring Reader** role for the monitor workspace.  
![][image53]

Added **Log Analytics Reader** role for the monitor workspace. (This is important and fixed the permissions error)  
![][image54]

Grafana authentication now works.  
![][image55]

## Task 4: Create a Dashboard in Grafana

### 1\. Click on create dashboard/visualization, then add the azure monitor as the data source

![][image56]

After the dashboard is created, the queries section shows the resource as my subscription, but it does not see anything in it. (Error: I cannot click on the VM which should be the source)  
![][image57]

Attempting to fix the previous error by enabling Diagnostic settings (and installing Python 2 on the VM)  
![][image58]

Previous error is fixed by adding the subscription as a **Log Analytics Reader** role as well.  
![][image59]

All resources now show and the VM can be selected.  
![][image60]

### 2\. Select the VM as the resource in the dashboard

![][image61]

### 3\. Create a dashboard (using Disk Read/Write as the metrics) and customize the panel with thresholds, colors, and labels.

![][image62]

![][image63]

![][image64]

![][image65]

![][image66]

![][image67]

![][image68]

![][image69]

![][image70]

Label, placement change, table instead of list, values include last and range  
![][image71]

## Task 5: Delete Resources

![][image72]


[image1]: images/image_1.png
[image2]: images/image_2.png
[image3]: images/image_3.png
[image4]: images/image_4.png
[image5]: images/image_5.png
[image6]: images/image_6.png
[image7]: images/image_7.png
[image8]: images/image_8.png
[image9]: images/image_9.png
[image10]: images/image_10.png
[image11]: images/image_11.png
[image12]: images/image_12.png
[image13]: images/image_13.png
[image14]: images/image_14.png
[image15]: images/image_15.png
[image16]: images/image_16.png
[image17]: images/image_17.png
[image18]: images/image_18.png
[image19]: images/image_19.png
[image20]: images/image_20.png
[image21]: images/image_21.png
[image22]: images/image_22.png
[image23]: images/image_23.png
[image24]: images/image_24.png
[image25]: images/image_25.png
[image26]: images/image_26.png
[image27]: images/image_27.png
[image28]: images/image_28.png
[image29]: images/image_29.png
[image30]: images/image_30.png
[image31]: images/image_31.png
[image32]: images/image_32.png
[image33]: images/image_33.png
[image34]: images/image_34.png
[image35]: images/image_35.png
[image36]: images/image_36.png
[image37]: images/image_37.png
[image38]: images/image_38.png
[image39]: images/image_39.png
[image40]: images/image_40.png
[image41]: images/image_41.png
[image42]: images/image_42.png
[image43]: images/image_43.png
[image44]: images/image_44.png
[image45]: images/image_45.png
[image46]: images/image_46.png
[image47]: images/image_47.png
[image48]: images/image_48.png
[image49]: images/image_49.png
[image50]: images/image_50.png
[image51]: images/image_51.png
[image52]: images/image_52.png
[image53]: images/image_53.png
[image54]: images/image_54.png
[image55]: images/image_55.png
[image56]: images/image_56.png
[image57]: images/image_57.png
[image58]: images/image_58.png
[image59]: images/image_59.png
[image60]: images/image_60.png
[image61]: images/image_61.png
[image62]: images/image_62.png
[image63]: images/image_63.png
[image64]: images/image_64.png
[image65]: images/image_65.png
[image66]: images/image_66.png
[image67]: images/image_67.png
[image68]: images/image_68.png
[image69]: images/image_69.png
[image70]: images/image_70.png
[image71]: images/image_71.png
[image72]: images/image_72.png
