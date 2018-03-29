---
title: Nastavení kardiostimulátor na SUSE Linux Enterprise Server v Azure | Microsoft Docs
description: Nastavení kardiostimulátor na SUSE Linux Enterprise Server v Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: 2982c8ba534b9a93a021a9d3a3819b904f09abc7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Nastavení kardiostimulátor na SUSE Linux Enterprise Server v Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Existují dvě možnosti nastavit cluster kardiostimulátor v Azure. Můžete použít buď vymezení agenta, který má na starosti restartování uzlu prostřednictvím rozhraní API služby Azure, který selhal, nebo můžete použít SBD zařízení.

SBD vyžaduje, aby jeden další virtuální počítač, který funguje jako cílový server iSCSI a poskytuje zařízení s SBD. Tento cílový server iSCSI může ale být sdíleny s další kardiostimulátor clustery. Výhodou použití zařízení s SBD je rychlejší převzetí služeb při selhání a pokud používáte SBD zařízení místní, nevyžaduje žádné změny na tom, jak používáte kardiostimulátor cluster. Vymezení SBD můžete dál používat agenta Azure ochranná jako záložní plotu mechanismus v případě, že cílový server iSCSI není k dispozici.

Pokud nechcete investovat do jeden další virtuální počítač, můžete taky agenta ochranná Azure. Nevýhodou je, že převzetí služeb při selhání může trvat mezi 10 až 15 minut, pokud selže zastavení prostředků nebo uzly clusteru nemůže komunikovat které mezi sebou už.

![Kardiostimulátor na SLES – přehled](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>Vymezení SBD

Pokud chcete pomocí SBD zařízení pro vymezení, postupujte podle těchto kroků.

### <a name="set-up-an-iscsi-target-server"></a>Nastavení cílového serveru iSCSI

Nejprve musíte vytvořit virtuálnímu počítači cíle iSCSI, pokud nemáte jednu již. cílové servery iSCSI je možné sdílet s více kardiostimulátor clusterů.

1. Nasaďte nový SLES 12 SP1 nebo vyšší virtuální počítač a připojte se k počítači přes ssh. Tento počítač nemusí být velký. Velikost virtuálního počítače jako Standard_E2s_v3 nebo Standard_D2s_v3 je dostačující.

1. Aktualizace SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. Instalovat balíčky cíl iSCSI

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Zapněte službu iSCSI target

   <pre><code>   
   sudo systemctl enable target
   sudo systemctl enable targetcli
   sudo systemctl start target
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Vytvoření zařízení iSCSI na cílovém serveru iSCSI

Připojte nový datový disk k virtuálnímu počítači iSCSI cíle, které je možné pro tento cluster. Datový disk může být malá jako 1 GB a musí být umístěny na prémiový účet úložiště nebo Disk spravované Premium, abyste mohli využívat výhod [jednotné SLA k Virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Spusťte následující příkaz **cíle iSCSI virtuálního počítače** k vytvoření disku iSCSI pro nový cluster. V následujícím příkladu **cl1** slouží k identifikaci do nového clusteru a **produkčnímu cl1-0** a **produkčnímu cl1-1** jsou názvy hostitelů uzlů clusteru. Je nahradíte názvy hostitelů uzly clusteru.

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
sudo systemctl restart target
</code></pre>

### <a name="set-up-sbd-device"></a>Nastavit SBD zařízení

Připojení k zařízení iSCSI, který byl vytvořen v posledním kroku z clusteru.
Spusťte následující příkazy na uzlech nového clusteru, který chcete vytvořit.
Následující položky jsou předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Připojit k zařízení iSCSI

   Nejprve povolte službu iSCSI a SBD služby.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Změnit název iniciátoru na prvním uzlu

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změnit obsah souboru tak, aby odpovídaly seznamy ACL můžete použít při vytváření zařízení iSCSI na cílovém serveru iSCSI

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Změnit název iniciátoru na druhém uzlu

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změnit obsah souboru tak, aby odpovídaly seznamy ACL můžete použít při vytváření zařízení iSCSI na cílovém serveru iSCSI

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Restartujte službu iSCSI

   Teď restartujte službu iSCSI na použití změny
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Připojte zařízení iSCSI. V následujícím příkladu 10.0.0.17 je IP adresa cílového serveru iSCSI a 3260 je výchozím portem. <b>IQN.2006 04.cl1.local:cl1</b> název cíl, který je uveden při spuštění prvního příkazu.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Ujistěte se, že je zařízení iSCSI k dispozici a Poznámka provádí název zařízení (v následujícím příkladu/dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   ID zařízení iSCSI nyní načtěte.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   Příkaz seznam tří ID zařízení. Doporučujeme, abyste pomocí ID, který začíná scsi-3, v příkladu výše to je
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  Vytvořte SBD zařízení

   ID zařízení v zařízení iSCSI použijte k vytvoření nového zařízení SBD na prvním uzlu clusteru.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Přizpůsobit SBD konfigurace

   Otevřete konfigurační soubor SBD

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Změňte vlastnost SBD zařízení, povolit integraci kardiostimulátor a změnit režim spouštění SBD.

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

   Nyní načíst modul

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalace clusteru

Následující položky jsou předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]** Update SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Přístupu ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]**  Přístupu ssh

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

1. **[1]**  Přístupu ssh

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Nainstalovat HA rozšíření
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele   

   Můžete buď použít DNS server, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak chcete použít soubor/etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech. Výhodou použití/Etc/Hosts, že je váš cluster nezávislé na DNS, který může být příliš jediný bod selhání.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky, které se/etc/hosts. Změnit IP adresu a název hostitele tak, aby odpovídaly prostředí   
   
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

1. **[A]**  Změnit heslo hacluster na stejné heslo

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Konfigurace corosync používají jiné přenos a přidání seznamu. Cluster nefunguje jinak.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Do souboru přidejte následující tučné obsah, pokud hodnoty nejsou zde nebo jiný.
   
   <pre><code> 
   [...]
     <b>token:          5000
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

1. **[1]**  Změnit výchozí nastavení kardiostimulátor

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Vytvoření STONITH zařízení

STONITH zařízení používá objekt služby k autorizaci s Microsoft Azure. Postupujte podle těchto kroků můžete vytvořit objekt služby.

1. Přejděte na <https://portal.azure.com>.
1. Otevřete okno Azure Active Directory  
   Přejděte k vlastnostem a poznamenejte si ID adresáře. Toto je **ID klienta**.
1. Klikněte na možnost registrace aplikace
1. Klikněte na tlačítko Přidat.
1. Zadejte název, vyberte typ aplikace "Aplikace webového rozhraní API", zadejte adresu URL přihlašování (například http://localhost) a klikněte na možnost vytvořit
1. Adresa URL přihlašování se nepoužívá a může být libovolná platná adresa URL
1. Vyberte nové aplikace a na kartě nastavení klikněte na klíče
1. Zadejte popis pro nový klíč, vyberte "Je platné stále" a klikněte na Uložit
1. Poznamenejte si hodnotu. Použije se jako **heslo** pro objekt služby
1. Poznamenejte si ID aplikace. Se používá jako uživatelské jméno (**přihlašovacího ID** v následujících krocích) instančního objektu

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Vytvořit vlastní roli pro ochrannou agenta

Objekt služby nemá oprávnění pro přístup k prostředkům Azure ve výchozím nastavení. Musíte poskytnout oprávnění objektu služby spuštění a zastavení (zrušit přidělení) všechny virtuální počítače v clusteru. Pokud jste již nevytvořili vlastní roli, můžete vytvořit pomocí [prostředí PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell#create-a-custom-role) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli#create-a-custom-role)

Použijte následující obsah pro vstupní soubor. Je nutné přizpůsobit obsah ke svým předplatným, nahraďte c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 ID předplatného. Pokud máte pouze jedno předplatné, odeberte druhou položku AssignableScopes.

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

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Přiřadíte roli vlastní instančního objektu

Přiřadíte vlastní role "Linux ochranná agenta Role" který byl vytvořen v poslední kapitoly instanční objekt. Už nepoužívejte roli vlastníka!

1. Přejděte na https://portal.azure.com.
1. Otevře se okno všechny prostředky
1. Vyberte virtuální počítač na prvním uzlu clusteru
1. Klikněte na řízení přístupu (IAM)
1. Klikněte na tlačítko Přidat.
1. Vyberte roli "Linux ochranná agenta Role"
1. Zadejte název aplikace, kterou jste vytvořili výše
1. Klikněte na tlačítko OK

Opakujte předchozí kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Vytvořit STONITH zařízení

Poté, co jste upravili oprávnění pro virtuální počítače, můžete nakonfigurovat zařízení STONITH v clusteru.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Vytvořit ochranná topologie pro vymezení SBD

Pokud chcete pomocí SBD zařízení, doporučujeme pomocí agenta Azure ochranná jako zálohu, v případě, že cílový server iSCSI není k dispozici.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** Povolit používání STONITH zařízení

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Další postup
* [Azure virtuálních počítačů, plánování a implementace pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP][dbms-guide]
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure naleznete v tématu [vysokou dostupnost z SAP HANA ve virtuálních počítačích Azure (VM)][sap-hana-ha]
