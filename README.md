# FusionComputeLab2
This is the second part of configuring the Huawei FusionCompute Solution as part of the HCIA Cloud Computing V4.0 Lab.

# Machines Informations
* Desktop: Isetcom07
* Network : - Network IP: 172.16.16.0/24
            - Gateway : 172.16.16.1/24
* CNA01 : - IP: 172.16.16.227
* VRM : - IP: 172.16.16.247

Informations source: https://wiki.openstack.org/wiki/FusionCompute

# Accessing the FusionCompute Dashboard
## Logging In 
![image](https://user-images.githubusercontent.com/91763346/236562136-a4abf79b-ac6f-4bef-ba7b-285435861316.png)

This step is fairly easy, we should just access the **https://VRM_IP** and in our case it's the **https://172.16.16.247**.

To access the platform for the first time we can use the default credentials

* **User**: admin
* **Password**: IaaS@PORTAL-CLOUD8!

## Changing the Password

![image](https://user-images.githubusercontent.com/91763346/236562646-a1c790e7-b4cf-47c2-8ac9-732e40856815.png)

We are are then prompted to set a new password.

# FusionCompute Resources Configuration
## 1. Creating a Cluster

![image](https://user-images.githubusercontent.com/91763346/236563883-bfbae17e-ddbb-40b1-b3ab-ddc0d246c080.png)

A cluster, as defined by Huawei, is a group of hosts. When a host is added to a cluster, the host's resources become part of the cluster's resources. The cluster manages the resources of all hosts within it.

In our case the name of the cluster is going to be : **isetcom07_cluster**.
We can turn off **Host Fault Features** & **Access Control Features**.

## 2. Adding Hosts

![image](https://user-images.githubusercontent.com/91763346/236565108-2b63e8d6-c246-4031-bdc0-d796c406412c.png)

In this step we're going to add our **RUNNING** **CNA01** Computing Node.

![image](https://user-images.githubusercontent.com/91763346/236565306-51343742-3572-419f-b4ec-135f00bc3e05.png)

* Name: CNA01
* IP Address: 172.16.16.227

We're not going to use BMC unlike what's shown in the picture.

![image](https://user-images.githubusercontent.com/91763346/236565642-471272a6-d75a-4467-99c1-cebd12e4b7b5.png)

Now we can click on Confirm.

* **IMPORTANT**: We should wait a few minutes so that the icon changes from **RED** to **BLUE** so we can know that it has been added succesfully.

* Binding Network Ports to Hosts

## 3. Configuring Network Resources
* Creating a DVS

Each host connects to a distributed virtual switch (DVS), which functions as a physical switch. In the downstream direction, the DVS connects to VMs through a virtual port. In the upstream direction, it connects to the NIC on the host that provides the VM resources. 
Therefore, the host network can communicate with the VM network.

![image](https://user-images.githubusercontent.com/91763346/236567836-8671a697-7a70-4b0f-8552-150ed6b10f2b.png)

After FusionCompute is installed, the system automatically creates a default DVS
ManagementDVS, and automatically adds an uplink Mgnt_Aggr.

![image](https://user-images.githubusercontent.com/91763346/236568326-572ceff0-ed71-40ba-9849-9d2763466180.png)

In our case:
* DVS Name: DVS01
* Switch Type: Common
* Add Uplinks:  Ticked.

![image](https://user-images.githubusercontent.com/91763346/236568510-a48fa451-0ca8-404e-b035-a8c524046d32.png)

* Adding an Uplink

![image](https://user-images.githubusercontent.com/91763346/236568756-f48e8621-c681-4d8d-95ca-87707a54d3f1.png)

Let's add an Uplink Group to the default **Mgnt_Aggr**

![image](https://user-images.githubusercontent.com/91763346/236568852-26418d92-575d-4205-b381-baf52e55cd35.png)

The final Result should be something like this:
![image](https://user-images.githubusercontent.com/91763346/236568938-9b32d87e-1575-4ab9-b574-7864bf8e3ad9.png)

* Adding a VLAN Pool

![image](https://user-images.githubusercontent.com/91763346/236569064-e967c820-472c-4ec5-864a-4c1494edbb70.png)

To keep the hosts connected across the network and isolated, we need to specify a VLAN Pool.

In this case, we chose **VLAN 16 -> 18**

![image](https://user-images.githubusercontent.com/91763346/236569446-96fb5c37-c97b-4a48-8b1f-6811bf7dda39.png)

The final result should look something like this:

![image](https://user-images.githubusercontent.com/91763346/236569516-9fb53aaf-9161-49f3-b57c-8ec6be963791.png)

* Adding Port Groups

![image](https://user-images.githubusercontent.com/91763346/236569783-0e04719f-2610-4258-8f9c-d125fd2fb387.png)

Simply, Port Groups are many DVS ports that have the same attributes and those are used to specify the VMs connected to it.

![image](https://user-images.githubusercontent.com/91763346/236569817-60487b9e-7053-45ab-9884-742d6d114b21.png)

The Name of the PORT group should be within the VLAN Range in which we just configured.

![image](https://user-images.githubusercontent.com/91763346/236569963-cfc284b2-f619-404c-953a-9a58d6f5061e.png)

* **Important**: The same process should used with another port group but this time, the port type needs to be trunk.

![image](https://user-images.githubusercontent.com/91763346/236570102-8e9934aa-9435-45eb-94b6-c8160371aafa.png)

Now we need to set the same VLAN ID we just put.

![image](https://user-images.githubusercontent.com/91763346/236570171-da62feef-2a9b-424e-a3ff-28afc615b9be.png)

* Adding Storage Resources

![image](https://user-images.githubusercontent.com/91763346/236570416-44029383-ae35-4ef2-9a00-bb315165e8b6.png)

Now we're going to configure our NAS Storage

![image](https://user-images.githubusercontent.com/91763346/236570452-72690617-b5b4-4269-bcf5-d56b73dfdbe2.png)

Since the NAS is hosted on our **HOST** Machine "Isetcom07" the config should be:
* **Type**: NAS
* **Storage IP Address**: 172.16.16.207
* **Associate Host**: Ticked

Now let's associate it to the whole Cluster (isetcom07_cluster).

![image](https://user-images.githubusercontent.com/91763346/236570666-43dbd76e-b845-4944-90b1-2cd0233bfadc.png)

## Creating VMs
* Creating a Bare VM

![image](https://user-images.githubusercontent.com/91763346/236570987-683bfc75-7bc6-4ece-b5a3-ba3e1d664b08.png)

Right Click on the Cluster and Choose **Create VM**:

![image](https://user-images.githubusercontent.com/91763346/236571148-3a13b540-a9ff-4b3e-8cbb-f24add7b9dfb.png)

![image](https://user-images.githubusercontent.com/91763346/236571081-89ee6905-639d-4661-a22f-69bd16ef6964.png)

Let's select our computing resource which is **CNA01** in our case the host we configured earlier.

![image](https://user-images.githubusercontent.com/91763346/236571242-8e14e3c7-a3a3-487d-bc3c-2931cdece45e.png)

Now we need to specify the Datastore that has been detected by the NAS and that's the one we configured earlier.

![image](https://user-images.githubusercontent.com/91763346/236571449-5576d66f-316b-4c5d-bc04-97a93383a237.png)

Now it's important to set the mode to **Thin Porvisioning**

![image](https://user-images.githubusercontent.com/91763346/236571564-46c9f1bf-217a-4bab-a39d-9f6dc137f5b8.png)

Finally we can confirm the settings.

![image](https://user-images.githubusercontent.com/91763346/236571643-a0f17cdb-cc1a-4d77-93eb-60c2685a18aa.png)
