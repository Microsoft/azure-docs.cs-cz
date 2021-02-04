---
title: Nastavení Pacemaker v RHEL v Azure | Microsoft Docs
description: Nastavení Pacemaker na Red Hat Enterprise Linux v Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2021
ms.author: radeltch
ms.openlocfilehash: 631ebcd41e50a6b8f9e049a646394e572c0b15f7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549316"
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

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Nejprve si přečtěte následující poznámky a dokumenty SAP:

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze.
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Poznámka SAP Poznámka [2002167] obsahuje doporučená nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP Poznámka [2009879] obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Replikace SAP HANA systému v clusteru Pacemaker](https://access.redhat.com/articles/3004101)
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Zásady podpory pro RHEL clustery s vysokou dostupností – SBD a fence_sbd](https://access.redhat.com/articles/2800691)
* Dokumentace k RHEL specifické pro Azure:
  * [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Co je třeba zvážit při přijímání RHEL 8 – vysoké dostupnosti a clusterů](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Konfigurace SAP S/4HANA ASCS/OLAJÍCÍCH pomocí samostatného serveru fronty 2 (ENSA2) v Pacemaker v RHEL 7,6](https://access.redhat.com/articles/3974941)
  * [RHEL pro nabídky SAP v Azure](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Instalace clusteru

![Pacemaker on RHEL – přehled](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat nepodporuje sledovací zařízení emulované softwarem. Red Hat nepodporuje SBD na cloudových platformách. Podrobnosti najdete v tématu [zásady podpory pro RHEL clustery s vysokou dostupností – SBD a fence_sbd](https://access.redhat.com/articles/2800691).
> Jediným podporovaným mechanismem pro Pacemaker Red Hat Enterprise Linux clusterů v Azure je agent Azure plot.  

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** zaregistrovat. Tento krok není nutný, pokud používáte image RHEL SAP s podporou vysoké dostupnosti.  

   Zaregistrujte virtuální počítače a připojte je ke fondu, který obsahuje úložiště pro RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Připojením fondu k imagi Azure Marketplace PAYG RHEL se vám bude efektivně považovat za vaše využití RHEL: jednou pro PAYG image a jednou pro RHEL nárok ve fondu, který připojíte. Pro zmírnění tohoto problému Azure teď poskytuje image RHEL BYOS. Další informace najdete [tady](../redhat/byos.md).  

1. **[A]** povolte RHEL pro úložiště SAP. Tento krok není nutný, pokud používáte image RHEL SAP s podporou vysoké dostupnosti.  

   Aby bylo možné nainstalovat požadované balíčky, povolte následující úložiště.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** instalace RHEL ha Add-On

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Doporučujeme, aby následující verze agenta Azure plot (nebo novější) mohly zákazníkům těžit z rychlejšího převzetí služeb při selhání, pokud dojde k selhání prostředku nebo pokud uzly clusteru nemůžou vzájemně komunikovat.  
   > RHEL 7,7 nebo novější použít nejnovější dostupnou verzi balíčku plot-agenti  
   > RHEL 7,6: plot-Agents-4.2.1-11.el7_6.8  
   > RHEL 7,5: plot-Agents-4.0.11-86.el7_5.8  
   > RHEL 7,4: plot-Agents-4.0.11-66.el7_4.12  
   > Další informace najdete v článku o [virtuálním počítači Azure spuštěném jako RHELý člen clusteru s vysokou dostupností, který může být ve velkém čase, nebo když se virtuální počítač vypne, neprojde nebo](https://access.redhat.com/solutions/3408711)neuplyne.

   Podívejte se na verzi agenta Azure plot. V případě potřeby ho aktualizujte na verzi, která se rovná nebo je vyšší než výše uvedená.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Pokud potřebujete aktualizovat agenta Azure plot a při použití vlastní role, nezapomeňte aktualizovat vlastní roli tak, aby zahrnovala akci **stavu PowerOff**. Podrobnosti najdete v tématu [Vytvoření vlastní role pro agenta plotu](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.  

   >[!IMPORTANT]
   > Pokud používáte názvy hostitelů v konfiguraci clusteru, je důležité mít spolehlivé rozlišení názvu hostitele. Pokud názvy nejsou k dispozici a to může vést k prodlevám při převzetí služeb při selhání clusteru, komunikace clusteru selže.
   > Výhodou použití/etc/hosts je to, že váš cluster bude nezávislý na službě DNS, což může být jen jeden bod selhání.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Změna hesla hacluster na stejné heslo

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** přidání pravidel brány firewall pro Pacemaker

   Do všech komunikací clusteru mezi uzly clusteru přidejte následující pravidla brány firewall.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** povolit základní Clusterové služby

   Spuštěním následujících příkazů povolte službu Pacemaker a spusťte ji.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** vytvořit cluster Pacemaker

   Spuštěním následujících příkazů ověřte uzly a vytvořte cluster. Nastavte token na 30000 pro povolení údržby paměti při zachovávání. Další informace najdete v [tomto článku pro Linux][virtual-machines-linux-maintenance].  
   
   Pokud vytváříte cluster na **RHEL 7. x**, použijte následující příkazy:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Pokud vytváříte cluster na **RHEL 8. X**, použijte následující příkazy:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Ověřte stav clusteru spuštěním následujícího příkazu:  
   <pre><code> # Run the following command until the status of both nodes is online
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
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** nastavte očekávané hlasy. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Při sestavování clusteru s více uzly, který je clusterem s více než dvěma uzly, nenastavujte hlasy na 2.    

1. **[1]** povolí souběžné akce plotu.

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>Vytvoření zařízení STONITH

Zařízení STONITH používá instanční objekt k autorizaci proti Microsoft Azure. Pomocí těchto kroků můžete vytvořit instanční objekt.

1. Přejděte na <https://portal.azure.com>.
1. Otevřete okno Azure Active Directory  
   Přejít na vlastnosti a poznamenejte si ID adresáře. Toto je **ID tenanta**.
1. Klikněte na Registrace aplikací
1. Klikněte na nová registrace.
1. Zadejte název, vyberte účty pouze v tomto adresáři organizace. 
2. Vyberte typ aplikace "Web", zadejte adresu URL pro přihlášení (například http: \/ /localhost) a klikněte na Přidat.  
   Přihlašovací adresa URL se nepoužívá a může to být libovolná platná adresa URL.
1. Vyberte certifikáty a tajné klíče a pak klikněte na nový tajný klíč klienta.
1. Zadejte popis nového klíče, vyberte možnost "nikdy vyprší platnost" a klikněte na tlačítko Přidat.
1. Nastavte uzel na hodnotu. Používá se jako **heslo** instančního objektu.
1. Vyberte Přehled. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno (**přihlašovací ID** v následujících krocích) instančního objektu.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** vytvoření vlastní role pro agenta plotu

Objekt služby nemá ve výchozím nastavení oprávnění pro přístup k prostředkům Azure. Musíte přidělit oprávnění instančního objektu pro spuštění a zastavení (napájení) všech virtuálních počítačů v clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete ji vytvořit pomocí [PowerShellu](../../../role-based-access-control/role-assignments-powershell.md) nebo rozhraní příkazového [řádku Azure CLI](../../../role-based-access-control/role-assignments-cli.md) .

Pro vstupní soubor použijte následující obsah. Je potřeba upravit obsah pro vaše předplatná, která jsou, nahraďte c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 ID vašeho předplatného. Pokud máte jenom jedno předplatné, odeberte druhou položku v AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** přiřazení vlastní role k instančnímu objektu

Přiřaďte vlastní roli "role ochrany systému Linux" vytvořenou v poslední kapitole objektu služby. Nepoužívejte již vlastníka role.

1. Přejděte na https://portal.azure.com.
1. Otevřete okno všechny prostředky.
1. Vyberte virtuální počítač prvního uzlu clusteru.
1. Klikněte na řízení přístupu (IAM).
1. Klikněte na přidat přiřazení role.
1. Vyberte roli "role agenta pro ochrannou část Linux".
1. Zadejte název aplikace, kterou jste vytvořili výše.
1. Kliknutí na Uložit

Opakujte výše uvedené kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]** vytvoření zařízení STONITH

Po úpravě oprávnění pro virtuální počítače můžete nakonfigurovat zařízení STONITH v clusteru.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> Možnost ' pcmk_host_map ' je požadována pouze v příkazu, pokud názvy hostitelů RHEL a názvy virtuálních počítačů Azure nejsou stejné. Zadejte mapování ve formátu název **hostitele: VM-Name**.
> Přečtěte si část tučné v příkazu. Další informace najdete v tématu o [tom, jaký formát mám použít k určení mapování uzlů na zařízení stonith v pcmk_host_map](https://access.redhat.com/solutions/2619961)

Pro RHEL **7. X** použijte následující příkaz ke konfiguraci ochranného zařízení:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

Pro RHEL **8. X** použijte následující příkaz ke konfiguraci ochranného zařízení:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> Operace monitorování a oplocení jsou rozserializovány. Výsledkem je, že pokud už existuje již běžící operace monitorování a současná událost, dojde k převzetí služeb při selhání clusteru z důvodu již běžící operace monitorování.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** povolit použití zařízení STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Agent Azure plotu vyžaduje odchozí připojení k veřejným koncovým bodům, jak je popsáno, spolu s možnými řešeními ve veřejných koncových bodech [pro virtuální počítače s využitím Standard interního nástroje](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
