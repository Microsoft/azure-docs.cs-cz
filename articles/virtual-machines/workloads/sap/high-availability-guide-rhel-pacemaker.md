---
title: Nastavení Pacemaker na Red Hat Enterprise Linux v Azure | Dokumentace Microsoftu
description: Nastavení Pacemaker na Red Hat Enterprise Linux v Azure
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
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: 6001681376011d03f7417d2099c0765e1a6cb5ae
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750114"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Nastavení Pacemaker na Red Hat Enterprise Linux v Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance

> [!NOTE]
> Pacemaker na Red Hat Enterprise Linux používá Agent síťové izolace Azure k plotu uzlem clusteru, pokud je to nutné. Převzetí služeb při selhání může trvat až 15 minut, pokud selže zdroje stop nebo uzlech clusteru nemůže komunikovat které mezi sebou už. Další informace najdete v článku [spuštění jako člen RHEL vysokou dostupnost clusteru virtuálních počítačů Azure trvat velmi dlouho bude ohraničených nebo selže nebo vyprší časový limit před virtuální počítač vypne monitorování geografických zón](https://access.redhat.com/solutions/3408711)

Přečtěte si následující poznámky SAP a Paper nejprve:

* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP.
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure.
  * Podporované SAP software a operační systém (OS) a kombinace databáze.
  * Požadovaná verze SAP jádra pro Windows a Linux v Microsoft Azure.
* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2002167] má doporučené nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP [2009879] obsahuje pokyny pro SAP HANA pro Red Hat Enterprise Linux
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Azure Virtual Machines, plánování a implementace SAP na platformě Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [Replikace systému SAP HANA v pacemaker clusteru](https://access.redhat.com/articles/3004101)
* Dokumentace ke službě obecné RHEL
  * [Přehled doplňků vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňku vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Konkrétní RHEL dokumentace k Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – Microsoft Azure Virtual Machines jako členů clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7.4 (a novější) vysokou dostupnost clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="cluster-installation"></a>Instalace clusteru

![Pacemaker na RHEL – přehled](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Zaregistrovat

   Registrace virtuálních počítačů a připojí k fondu, který obsahuje úložiště pro RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]**  Povolit RHEL for SAP úložišť

   Abyste mohli nainstalovat požadované balíčky, povolte následující úložiště.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]**  Nainstalovat doplněk pro vysokou dostupnost RHEL

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech. Výhodou použití/Etc/Hosts, že je váš cluster nezávisle na DNS, který může být kritickým prvkem způsobujícím selhání příliš.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Hacluster Změna hesla na stejné heslo

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Přidat pravidla brány firewall pro pacemaker

   Přidáte následující pravidla brány firewall pro veškerou komunikaci clusteru mezi uzly clusteru.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]**  Povolit základní cluster services

   Spusťte následující příkazy, které chcete povolit službu Pacemaker a spusťte ji.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]**  Pacemaker vytvoření clusteru

   Spusťte následující příkazy k ověření uzly a vytvoření clusteru. Nastavte token 30000 umožňující Údržba pro zachování paměti. Další informace najdete v tématu [tohoto článku pro Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   
   # Run the following command until the status of both nodes is online
   sudo pcs status
   
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]**  Nastavit očekávané hlasů

   <pre><code>sudo pcs quorum expected-votes 2
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

Instanční objekt služby nemá oprávnění pro přístup k prostředkům Azure ve výchozím nastavení. Je potřeba udělit oprávnění instančního objektu pro spuštění a zastavení (uvolníte) všechny virtuální počítače v clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete vytvořit pomocí [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

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
1. Klikněte na tlačítko Přidat přiřazení role
1. Vyberte roli "Linux ohrazení agenta roli"
1. Zadejte název aplikace, kterou jste vytvořili výše
1. Kliknutí na Uložit

Opakujte předchozí kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Umožňuje vytvářet devices využitím techniky STONITH

Až budete upravovat oprávnění pro virtuální počítače, můžete nakonfigurovat zařízení využitím techniky STONITH v clusteru.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Použijte následující příkaz pro konfiguraci zařízení ohrazení.

> [!NOTE]
> Možnost 'pcmk_host_map' je potřeba jenom v příkazu, pokud názvy hostitelů RHEL a názvy uzlů Azure nejsou identické. Přečtěte si část tučně v příkazu.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Povolit použití zařízení s využitím techniky STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Další postup

* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
