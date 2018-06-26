# Prologue

This work arises from the need and curiosity to learn the use and operation of a KMS server with which you can activate and validate licenses of different Windows operating systems through the internal network.

For this reason I have documented a step by step assembly of a laboratory with the installation of a KMS server in a virtual machine **Ubuntu 16.07** or also in a **router \(Lan-Dropbox\)** with **OPEN-WRT** operating system that will allow the activations of the Windows licenses with the data and steps provided by Microsoft through its website.

**Why a KMS server?** As I mentioned before, the fact that the KMS server allows us to validate the licenses of the Windows operating systems through our internal network is a great advantage since it allows us to validate the licenses without having to go to the Internet and validate those licenses in the Microsoft servers. thus avoiding a large network traffic when validating a large number of licenses. Therefore also in a case of internet drop will allow us to validate the licenses without problem.

Even if you do not touch on this work, the implementation of a KMS server with the group policies of **Active Directory \(AD\)** or a **Windows Server Update Service \(WSUS\)** would add extra versatility and power to our environment since the validations of the licenses would be carried out automatically.

> **Important note:** This work is designed for educational use only and should never be used to validate licenses outside of a laboratory environment as it would be a violation of Microsoft's terms and conditions as it is not a server KMS legitimate. For this reason I do not choke responsible for improper use that can be made with this document.

For the installation of legitimate KMS Server Servers you can follow the information of the following links:

* [http://ivan.dretvic.com/2011/06/how-to-configure-a-kms-server-in-windows-server-2008-r2/](http://ivan.dretvic.com/2011/06/how-to-configure-a-kms-server-in-windows-server-2008-r2/)
* [https://www.windows-noob.com/forums/topic/12787-how-can-i-setup-kms-key-management-server-for-activating-windows-10/](https://www.windows-noob.com/forums/topic/12787-how-can-i-setup-kms-key-management-server-for-activating-windows-10/)



