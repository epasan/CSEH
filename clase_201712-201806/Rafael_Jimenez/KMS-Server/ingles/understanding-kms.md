## 2. Understanding KMS

KMS activates computers on a local network, eliminating the need for individual computers to connect to Microsoft. To do this, KMS uses a client–server topology. KMS client computers can locate KMS host computers by using Domain Name System \(DNS\) or a static configuration. KMS clients contact the KMS host by using remote procedure call \(RPC\). KMS can be hosted on computers that are running the Windows Vista, Windows 7, Windows Server 2003, Windows Server 2008, or Windows Server 2008 R2 operating systems.

### 2.1. Minimum Computer Requirements

When planning for KMS activation, the network must meet or exceed the activation threshold, or the minimum number of qualifying computers that KMS requires. You must also understand how the KMS host tracks the number of computers on the network.

### 2.2. KMS Activation Thresholds

KMS can activate both physical computers and virtual machines. To qualify for KMS activation, a network must meet the activation threshold: KMS hosts activate client computers only after meeting this threshold. To ensure that the activation threshold is met, a KMS host counts the number of computers that are requesting activation on the network. For computers running Windows Server 2008 or Windows Server 2008 R2, the activation threshold is five. For computers running Windows Vista or Windows 7, the activation threshold is 25. The thresholds include client computers and servers that are running on physical computers or virtual machines.

A KMS host responds to each valid activation request from a KMS client with the count of how many computers have contacted the KMS host for activation. Clients that receive a count below their activation threshold are not activated. For example, if the first two computers that contact the KMS host are running Windows 7, the first receives an activation count of 1, and the second receives an activation count of 2. If the next computer is a Windows 7 virtual machine, it receives an activation count of 3, and so on. None of these computers is activated, because computers running Windows 7 must receive an activation count ≥25 to be activated. KMS clients in the grace state that are not activated because the activation count is too low connect to the KMS host every two hours to get the current activation count and will be activated when the threshold is met.

If the next computer that contacts the KMS host is running Windows Server 2008 R2, it receives an activation count of 4, because activation counts are a combination of computers running Windows Server 2008 R2 and Windows 7. If a computer running Windows Server 2008 or Windows Server 2008 R2 receives an activation count that is ≥5, it is activated. If a computer running Windows 7 receives an activation count ≥25, it is activated.

### 2.3. Activation Count Cache

To track the activation threshold, the KMS host keeps a record of the KMS client computers that request activation. The KMS host gives each KMS client computer a client machine identification \(CMID\) designation, and the KMS host saves each CMID in a table. Each activation request remains in the table for 30 days. When a client computer renews its activation, the cached CMID is removed from the table, a new record is created, and the 30-day period begins again. If a KMS client computer does not renew its activation within 30 days, the KMS host removes the corresponding CMID from the table and reduces the activation count by one.

The KMS host caches twice the number of CMIDs that KMS clients require to help ensure that the CMID count does not drop below the activation threshold. For example, on a network with client computers running Windows 7, the KMS activation threshold is 25, so the KMS host caches the CMIDs of the most recent 50 activations. The KMS activation threshold for Windows Server 2008 R2 is 5. A KMS host that is contacted only by KMS client computers running Windows Server 2008 R2 would cache the 10 most recent CMIDs. If a client computer running Windows 7 later contacts that KMS host, KMS increases the cache size to 50 to accommodate the higher threshold. KMS never reduces the cache size.

### 2.4. How KMS Works

KMS activation requires TCP/IP connectivity. By default, KMS hosts and client computers use DNS to publish and find the KMS service. The default settings can be used, which require little to no administrative action, or KMS hosts and client computers can be manually configured based on network configuration and security requirements.

KMS Activation Renewal  
KMS activations are valid for 180 days—the activation validity interval. To remain activated, KMS client computers must renew their activation by connecting to the KMS host at least once every 180 days. By default, KMS client computers attempt to renew their activation every seven days. If KMS activation fails , the client will retry every two hours. After a client computer’s activation is renewed, the activation validity interval begins again.

### 2.5. Publication of the KMS Service

The KMS service uses service \(SRV\) resource records \(RR\) in DNS to store and communicate the locations of KMS hosts. KMS hosts use the DNS dynamic update protocol , if available, to publish the KMS SRV RRs. If dynamic update is not available or the KMS host does not have rights to publish the RRs, the DNS records must be published manually, or you must configure client computers to connect to specific KMS hosts.

> Note   DNS changes may take time to propagate to all DNS hosts, depending on the complexity and topology of the network.

### 2.6. Client Discovery of the KMS Service

By default, KMS clients query DNS for KMS service information. The first time a KMS client queries DNS for KMS service information, it randomly chooses a KMS host from the list of SRV RRs that DNS returns.

The address of a DNS server containing the SRV RRs can be listed as a suffixed entry on KMS clients, which allows advertisement of SRV RRs for KMS in one DNS server and KMS clients with other primary DNS servers to find it.

Priority and weight parameters can be added to the **DnsDomainPublishList** registry value for KMS. Establishing KMS host priority groupings and weighting within each group allows you to specify which KMS host the clients should try first and balances traffic among multiple KMS hosts. Only Windows 7 and Windows Server 2008 R2 _provide_ the priority and _weight_ parameters.

If the KMS host that a client selects does not respond, the KMS client removes that KMS host from its list of SRV RRs and randomly selects another KMS host from the list. When a KMS host responds, the KMS client caches the name of the KMS host and uses it for subsequent activation and renewal attempts. If the cached KMS host does not respond on a subsequent renewal, the KMS client discovers a new KMS host by querying DNS for KMS SRV RRs.

By default, client computers connect to the KMS host for activation by using anonymous RPCs through **TCP port 1688.** \(You can change the default port.\) After establishing a TCP session with the KMS host, the client sends a single request packet. The KMS host responds with the activation count. If the count meets or exceeds the activation threshold for that operating system, the client is activated and the session is closed. The KMS client uses this same process for renewal requests. The communication each way is 250 bytes.

### 2.7. Activating the First KMS Host

KMS hosts on the network need to install a KMS key, and then be activated with Microsoft. Installation of a KMS key enables the Key Management Service on the KMS host. After installing the KMS key, complete the activation of the KMS host by telephone or online. Beyond this initial activation, a KMS host does not communicate any information to Microsoft.

KMS keys are only installed on KMS hosts, never on individual KMS clients. Windows 7 and Windows Server 2008 R2 have safeguards to help prevent inadvertently installing KMS keys on KMS client computers. Any time users try to install a KMS key

### 2.8. Activating Subsequent KMS Hosts

Each KMS key can be installed on up to six KMS hosts, which can be physical computers or virtual machines. After activating a KMS host, the same host can be reactivated up to nine more times with the same key.

If the organization needs more than six KMS hosts, You can request additional activations for the organization’s KMS key by calling the Activation Call Center to request an exception. For more information, see the Volume Licensing Web site at [http://go.microsoft.com/fwlink/?LinkID=73076](http://go.microsoft.com/fwlink/?LinkID=73076).

### 2.9. Upgrading Existing KMS Hosts

KMS hosts that are running Windows Server 2003, Windows Vista, or Windows Server 2008 can be configured to support KMS clients running Windows 7 and Windows Server 2008 R2. For Windows Vista and Windows Server 2008, it is necessary to update the KMS host with a package with files that support the expanded KMS client. This package is available through the Microsoft Download Center at [http://www.microsoft.com/downloads](http://www.microsoft.com/downloads). Once the package is installed on the KMS host, a KMS key that is designed to support Windows 7 and Windows Server 2008 R2 can be installed and activated as described earlier in this guide. The KMS key that supports the new versions of the Windows operating systems also provides support for the previous Volume Licensing editions of Windows that are acting as KMS clients.

In the case of updating a Windows Server 2003 KMS host, all necessary files are contained within the KMS 1.2 downloadable package, which is available through the Microsoft Download Center at [http://www.microsoft.com/downloads](http://www.microsoft.com/downloads).

### 2.10. Planning KMS Clients

By default, computers that are running Volume Licensing editions of Windows Vista, Windows 7, Windows Server 2008, and Windows Server 2008 R2 are KMS clients , and no additional configuration is needed. KMS clients can locate a KMS host automatically by querying DNS for SRV RRs that publish the KMS service. If the network environment does not use SRV RRs, a KMS client can be manually configured to use a specific KMS host.

To manually configure KMS clients, follow the steps in the section titled, “Manually Specifying a KMS Host,” later in this guide.

### 2.11. Activating as a Standard User

Windows 7 and Windows Server 2008 R2 do not require administrator privileges for activation. However, this change does not allow standard user accounts to remove Windows 7 or Windows Server 2008 R2 from the activated state. An administrator account is still required for other activation- or license-related tasks, such as “rearm.”

