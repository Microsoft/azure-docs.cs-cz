---
title: Nastavení kardiostimulátoru na RHEL v Azure | Dokumenty společnosti Microsoft
description: Nastavení kardiostimulátoru na Red Hat Enterprise Linux u Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264476"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Nastavení kardiostimulátoru na Red Hat Enterprise Linux u Azure

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

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Nejprve si přečtěte následující poznámky a dokumenty SAP:

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
  * Podporovaný software SAP a operační systém (OS) a databázové kombinace.
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu (tento článek)][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [Replikace systému SAP HANA v clusteru kardiostimulátoru](https://access.redhat.com/articles/3004101)
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – sbd a fence_sbd](https://access.redhat.com/articles/2800691)
* Dokumentace RHEL specifické pro Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – virtuální počítače Microsoft Azure jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace vysoce dostupnosti red hatového linuxového linuxu 7.4 (a novějšího) v Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Konfigurace SAP S/4HANA ASCS/ERS pomocí samostatného serveru enqueue server 2 (ENSA2) v kardiostimulátoru na RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalace clusteru

![Kardiostimulátor na RHEL přehled](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat nepodporuje software-emulovaný hlídacího psa. Red Hat nepodporuje SBD na cloudových platformách. Podrobnosti naleznete v [tématu Zásady podpory pro clustery s vysokou dostupností RHEL – sbd a fence_sbd](https://access.redhat.com/articles/2800691).
> Jediným podporovaným mechanismem oplocení pro clustery Red Hat Enterprise Linux v Azure je agent plotu Azure.  

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Registr

   Zaregistrujte své virtuální počítače a připojte je do fondu, který obsahuje úložiště pro RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Všimněte si, že připojením fondu k image Azure Marketplace PAYG RHEL se vám bude efektivně účtovat dvojité poplatky za využití RHEL: jednou pro bitovou kopii PAYG a jednou pro nárok RHEL ve fondu, který připojíte. Chcete-li to zmírnit, Azure teď poskytuje image BYOS RHEL. Více informací je k dispozici [zde](../redhat/byos.md).

1. **[A]** Povolit RHEL pro repo spozasystémsap

   Chcete-li nainstalovat požadované balíčky, povolte následující úložiště.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Instalace doplňku RHEL HA

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Doporučujeme následující verze agenta Azure Fence (nebo novější) pro zákazníky využívat rychlejší převzetí služeb při selhání, pokud dojde k selhání zastavení prostředků nebo uzly clusteru nemůže komunikovat, které navzájem už:  
   > RHEL 7.6: plot-agenti-4.2.1-11.el7_6.8  
   > RHEL 7.5: plot-agenti-4.0.11-86.el7_5.8  
   > RHEL 7.4: plot-agenti-4.0.11-66.el7_4.12  
   > Další informace najdete v článku [virtuální počítač Azure spuštěný jako člen clusteru s vysokou dostupností RHEL trvá velmi dlouho, než se bude oplocení, nebo se nezdaří nebo časový plán oplocení před vypnutím virtuálního počítače](https://access.redhat.com/solutions/3408711).

   Zkontrolujte verzi agenta plotu Azure. V případě potřeby jej aktualizujte na verzi, která se rovná nebo je vyšší než výše uvedené.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Pokud potřebujete aktualizovat agenta Azure Fence a pokud používáte vlastní roli, nezapomeňte aktualizovat vlastní roli tak, aby zahrnovala akci **powerOff**. Podrobnosti viz [Vytvoření vlastní role agenta plotu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech. Výhodou použití /etc/hosts je, že váš cluster se stane nezávislým na DNS, což může být také jediný bod selhání.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Změna hesla hacluster na stejné heslo

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Přidání pravidel brány firewall pro kardiostimulátor

   Přidejte následující pravidla brány firewall do veškeré komunikace clusteru mezi uzly clusteru.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Povolení základních clusterových služeb

   Spusťte následující příkazy, abyste povolili službu Pacemaker a spusťte ji.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Vytvoření clusteru kardiostimulátoru

   Spusťte následující příkazy k ověření uzlů a vytvoření clusteru. Nastavte token na 30000, aby údržbu zachování paměti. Další informace naleznete v [tomto článku pro Linux][virtual-machines-linux-maintenance].

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

1. **[A]** Nastavit očekávané hlasy

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Vytvoření zařízení STONITH

Zařízení STONITH používá instanční objekt k autorizaci proti Microsoft Azure. Podle těchto kroků vytvořte instanční objekt.

1. Přejděte na <https://portal.azure.com>.
1. Otevření okna Služby Azure Active Directory  
   Přejděte na vlastnosti a poznamenejte si ID adresáře. Toto je **ID klienta**.
1. Klikněte na Registrace aplikací.
1. Klikněte na Nová registrace.
1. Zadejte název, vyberte možnost Účty pouze v tomto adresáři organizace. 
2. Vyberte typ aplikace "Web", zadejte přihlašovací adresu\/URL (například http: /localhost) a klepněte na tlačítko Přidat  
   Přihlašovací adresa URL se nepoužívá a může se jedná o libovolnou platnou adresu URL.
1. Vyberte certifikáty a tajné klíče a klikněte na Nový tajný klíč klienta.
1. Zadejte popis nového klíče, vyberte "Nikdy nevyprší" a klikněte na Přidat.
1. Poznamenejte si hodnotu. Používá se jako **heslo** pro instanční objekt
1. Vyberte Přehled. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno **(login ID** v níže uvedených krocích) instančního objektu

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Vytvoření vlastní role pro agenta plotu

Instanční objekt nemá oprávnění k přístupu k prostředkům Azure ve výchozím nastavení. Je třeba udělit instanční objekt oprávnění ke spuštění a zastavení (vypnutí) všechny virtuální počítače clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete ji vytvořit pomocí [PowerShellu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) nebo [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Pro vstupní soubor použijte následující obsah. Obsah je třeba přizpůsobit vašim předplatným, které je, nahradit c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 s ID vašeho předplatného. Pokud máte pouze jedno předplatné, odeberte druhou položku v AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Přiřazení vlastní role instančnímu objektu service instančního objektu

Přiřaďte vlastní roli "Linux Fence Agent Role", která byla vytvořena v poslední kapitole instančního objektu. Už nepoužívejte roli vlastníka!

1. Přejděte na https://portal.azure.com.
1. Otevření okna Všechny prostředky
1. Výběr virtuálního počítače prvního uzlu clusteru
1. Klikněte na ovládací prvek přístupu (IAM)
1. Klikněte na Přidat přiřazení role.
1. Vyberte roli "Linux Fence Agent Role"
1. Zadejte název aplikace, kterou jste vytvořili výše.
1. Kliknutí na Uložit

Opakujte výše uvedené kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]** Vytvoření zařízení STONITH

Po úpravě oprávnění pro virtuální počítače můžete nakonfigurovat zařízení STONITH v clusteru.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Ke konfiguraci plotového zařízení použijte následující příkaz.

> [!NOTE]
> Možnost "pcmk_host_map" je vyžadována pouze v příkazu, pokud názvy hostitelů RHEL a názvy uzlů Azure nejsou identické. Viz tučná část příkazu.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Povolení používání zařízení STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
