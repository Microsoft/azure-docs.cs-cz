---
title: Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure | Dokumentace Microsoftu
description: Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: sedusch
ms.openlocfilehash: 9ce95bcf15d0186c1baea3df407d0fc0c4200f45
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115472"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

Existují dvě možnosti, jak nastavit Pacemaker clusteru v Azure. Můžete použít buď agenta monitorování geografických zón, které se postará o restartování neúspěšných uzlu prostřednictvím rozhraní API služby Azure nebo můžete použít SBD zařízení.

Zařízení SBD vyžaduje jeden další virtuální počítač, který funguje jako cílový server iSCSI a poskytuje SBD zařízení. Tento cílový server iSCSI může ale být sdíleny s další clustery Pacemaker. Výhodou použití zařízení s SBD je rychlejší převzetí služeb při selhání a pokud používáte SBD zařízení místně, nevyžaduje žádné změny na tom, jak používáte pacemaker cluster. Monitorování geografických zón SBD můžete dál používat agenta Azure ohrazení jako záložní mechanismus ochranu v případě, že cílový server iSCSI není k dispozici.

Pokud nechcete investovat do dalšího virtuálního počítače, můžete použít také agenta Azure. Nevýhodou je, že převzetí služeb při selhání můžete provést mezi 10 až 15 minut v případě zdroje stop selže nebo uzlech clusteru nemůže komunikovat které mezi sebou už.

![Pacemaker na SLES – přehled](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Při plánování a nasazení Linuxu Pacemaker Clusterované uzly a SBD zařízení, je nezbytné pro celkovou spolehlivost úplná konfigurace clusteru, který součástí směrování mezi virtuálními počítači a virtuálních počítačů, který je hostitelem SBD zařízení neprochází přes jiná zařízení, jako jsou [síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/). V opačném případě problémy a události údržby pomocí síťového virtuálního zařízení může mít negativní dopad na stabilitu a spolehlivost na celkové konfiguraci clusteru. Pokud se chcete vyhnout tyto překážky nedefinují pravidla směrování síťových virtuálních zařízení nebo [pravidla směrování definované uživatele](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) této směrovat provoz mezi uzly clusteru a SBD zařízení prostřednictvím síťových virtuálních zařízení a podobná zařízení při plánování a nasazení Linuxu Pacemaker Clusterované uzly a SBD zařízení. 
>


## <a name="sbd-fencing"></a>Monitorování geografických zón SBD

Pokud chcete použít SBD zařízení pro monitorování geografických zón, postupujte podle těchto kroků.

### <a name="set-up-an-iscsi-target-server"></a>Nastavení cílového serveru iSCSI

Nejdřív je potřeba vytvořit iSCSI cílový virtuální počítač, pokud jeden už není nutné. cílový server iSCSI je sdílet s více clustery Pacemaker.

1. Nasazení nového SLES 12 SP1 nebo vyšší virtuálního počítače a připojte se k počítači přes ssh. Tento počítač nemusí být velké. Velikost virtuálního počítače jako Standard_E2s_v3 nebo Standard_D2s_v3 je dostačující.

1. Aktualizace SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. Odebrání balíčků

   Aby se zabránilo známým problémem se systémem targetcli a SLES 12 SP3, odinstalujte následující balíčky. Můžete ignorovat chyby týkající se balíčky, které se nenašel
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. Instalace balíčků cíl iSCSI

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Zapněte službu iSCSI target

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Vytvoření zařízení iSCSI na cílovém serveru iSCSI

Připojte nový datový disk k iSCSI cílový virtuální počítač, který lze použít pro tento cluster. Datový disk může být malá jako 1 GB a je nutné umístit do účtu služby Premium Storage nebo Managed Disks úrovně Premium, abyste využili výhod [jednotné SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Spuštěním následujícího příkazu **cíli iSCSI virtuálního počítače** k vytvoření disku iSCSI pro nový cluster. V následujícím příkladu **cl1** slouží k identifikaci nový cluster a **prod-cl1-0** a **prod-cl1-1** jsou názvy hostitelů z uzlů clusteru. Nahraďte jejich názvy hostitelů uzlů clusteru.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>Nastavit SBD zařízení

Připojte se k zařízení iSCSI, který byl vytvořen v posledním kroku z clusteru.
Spuštěním následujících příkazů na uzlech nového clusteru, který chcete vytvořit.
Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Připojit k zařízení iSCSI

   Nejprve povolte iSCSI a SBD služby.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Změnit název iniciátoru do prvního uzlu

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změnit obsah souboru tak, aby odpovídaly seznamy ACL, které jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Změnit název iniciátoru na druhém uzlu

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změnit obsah souboru tak, aby odpovídaly seznamy ACL, které jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Restartujte službu iSCSI

   Nyní restartujte službu iSCSI na použití změny
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Připojení zařízení iSCSI. V následujícím příkladu 10.0.0.17 je IP adresa cílového serveru iSCSI a 3260 je výchozím portem. <b>IQN.2006 04.cl1.local:cl1</b> je cílový název, který je uveden při spuštění prvního příkazu.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Ujistěte se, že zařízení iSCSI je k dispozici a Všimněte si, v názvu zařízení (v následujícím příkladu/dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Teď načtěte ID zařízení iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   Příkaz seznamu tři identifikátory zařízení. Doporučujeme, abyste pomocí ID, který začíná scsi-3, v příkladu výše to je
   
   **/dev/disk/by-ID/SCSI-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  Vytvořit SBD zařízení

   ID zařízení zařízení iSCSI použijte k vytvoření nového zařízení SBD na prvním uzlu clusteru.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Přizpůsobit SBD config

   Otevřete konfigurační soubor SBD

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Změňte vlastnost SBD zařízení, povolit integraci pacemaker a změnit režim spouštění SBD.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Vytvoření konfiguračního souboru softdog

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Nyní načtení modulu

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalace clusteru

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Aktualizovat SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Zpřístupnění ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]**  Zpřístupnění ssh

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Zpřístupnění ssh

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Ohrazení instalace agentů
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele   

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech. Výhodou použití/Etc/Hosts, že je váš cluster nezávisle na DNS, který může být kritickým prvkem způsobujícím selhání příliš.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Instalaci clusteru
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Přidat uzel do clusteru
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Hacluster Změna hesla na stejné heslo

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Corosync použít jiné přenosu a přidání seznamu konfigurace. Cluster nefunguje jinak.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Pokud hodnoty nejsou zde nebo jiné, přidejte do souboru následující tučného písma obsahu. Ujistěte se, že chcete-li změnit token 30000 umožňující Údržba pro zachování paměti. Zobrazit [tohoto článku pro Linux] [ virtual-machines-linux-maintenance] nebo [Windows] [ virtual-machines-windows-maintenance] další podrobnosti.
   
   <pre><code> 
   [...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Potom restartujte službu corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]**  Změnit výchozí nastavení pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Vytvoření zařízení využitím techniky STONITH

Využitím techniky STONITH zařízení využívá instanční objekt služby k autorizaci s Microsoft Azure. Postupujte podle těchto kroků můžete vytvořit instanční objekt služby.

1. Přejděte na <https://portal.azure.com>.
1. Otevře se okno Azure Active Directory  
   Přejděte do vlastností a poznamenejte si ID adresáře. Toto je **ID tenanta**.
1. Klikněte na možnost registrace aplikací
1. Klikněte na tlačítko Přidat.
1. Zadejte název, vyberte typ aplikace "Aplikace webového rozhraní API", zadejte přihlašovací adresu URL (například http://localhost) a klikněte na tlačítko Vytvořit
1. Adresa URL přihlašování se nepoužívá a může být jakákoliv platná adresa URL
1. Vyberte novou aplikaci a na kartě nastavení klikněte na tlačítko klíče
1. Zadejte popis pro nový klíč, vyberte "Je platné stále" a klikněte na Uložit
1. Poznamenejte si hodnotu. Používá se jako **heslo** pro instanční objekt
1. Poznamenejte si ID aplikace. Se používá jako uživatelské jméno (**přihlašovací ID** v následujících krocích) instanční objekt služby

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Vytvořit vlastní roli pro agent síťové izolace

Instanční objekt služby nemá oprávnění pro přístup k prostředkům Azure ve výchozím nastavení. Je potřeba udělit oprávnění instančního objektu pro spuštění a zastavení (uvolníte) všechny virtuální počítače v clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete vytvořit pomocí [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

Použijte následující obsah vstupního souboru. Je potřeba upravit obsah, který je pro vaše předplatná, c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 nahraďte ID vašeho předplatného. Pokud máte pouze jedno předplatné, odeberte v AssignableScopes druhou položku.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Přiřazení vlastní role k Instančnímu objektu

Přiřazení vlastní role "Linux ohrazení agenta roli", který byl vytvořen v kapitole poslední k Instančnímu objektu. Už nepoužívají role vlastníka!

1. Přejděte na https://portal.azure.com.
1. Otevřete v okně všechny prostředky
1. Vyberte virtuální počítač na prvním uzlu clusteru
1. Klikněte na řízení přístupu (IAM)
1. Klikněte na tlačítko Přidat.
1. Vyberte roli "Linux ohrazení agenta roli"
1. Zadejte název aplikace, kterou jste vytvořili výše
1. Klikněte na tlačítko OK

Opakujte předchozí kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Umožňuje vytvářet devices využitím techniky STONITH

Až budete upravovat oprávnění pro virtuální počítače, můžete nakonfigurovat zařízení využitím techniky STONITH v clusteru.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Vytvořit ohrazení topologie pro monitorování geografických zón SBD

Pokud chcete použít SBD zařízení, nadále doporučujeme používat agenta Azure jako zálohu pro případ, že cílový server iSCSI není k dispozici.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** Povolit použití zařízení s využitím techniky STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Další postup
* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
