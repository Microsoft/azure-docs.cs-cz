---
title: SAP HANA horizontálního navýšení kapacity s Azure NetApp Files na RHEL | Microsoft Docs
description: Průvodce vysokou dostupností pro SAP NetWeaver v Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/05/2021
ms.author: radeltch
ms.openlocfilehash: 8dfbdb338416511de403733ce61b7b2472190963
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916265"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Tento článek popisuje, jak nasadit vysoce dostupný SAP HANA systém v konfiguraci se škálováním na více systémů pomocí úsporného režimu v Azure Red Hat Enterprise Linux virtuálních počítačů (VM) pomocí [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) pro sdílené svazky úložiště.  

V ukázkových konfiguracích, instalačních příkazech atd. instance HANA je **03** a ID systému Hana je **HN1**. Příklady jsou založené na HANA 2,0 SP4 a Red Hat Enterprise Linux pro SAP 7,6. 

> [!NOTE]
> Tento článek obsahuje odkazy na *Hlavní* a *podřízené* výrazy, které Microsoft už nepoužívá. Po odebrání těchto podmínek ze softwaru je odebereme z tohoto článku.


Než začnete, přečtěte si následující poznámky a dokumenty SAP:

* [Dokumentace k Azure NetApp Files][anf-azure-doc] 
* Poznámka [1928533] pro SAP obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* SAP Note [2015553]: seznam požadavků pro nasazení softwaru SAP podporovaného SAP v Azure
* Poznámka SAP [2002167] má doporučená nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP Poznámka [2009879] obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux
* SAP Note [2178632]: obsahuje podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498]: obsahuje požadovanou verzi agenta hostitele SAP pro Linux v Azure.
* SAP Note [2243692]: obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure
* SAP Note [1999351]: obsahuje další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* Poznámka SAP poznámky [1900823]: obsahuje informace o požadavcích na úložiště SAP HANA.
* [Komunitní wikiweb pro SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): obsahuje všechny požadované poznámky SAP pro Linux
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Průvodce Red Hat Enterprise Linux sítě](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Dokumentace k RHEL specifické pro Azure:
  * [Instalace SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]


## <a name="overview"></a>Přehled

Jednou z metod pro dosažení vysoké dostupnosti HANA je konfigurace automatického převzetí služeb při selhání hostitele. Chcete-li konfigurovat automatické převzetí služeb při selhání hostitele, přidejte jeden nebo více virtuálních počítačů do systému HANA a nakonfigurujte je jako pohotovostní uzly. Když aktivní uzel neproběhne úspěšně, převezme pohotovostní uzel automaticky. V předkládaných konfiguracích s virtuálními počítači Azure dosáhnete automatického převzetí služeb při selhání pomocí [systému souborů NFS na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> Pohotovostní uzel potřebuje přístup ke všem svazkům databáze. Svazky HANA musí být připojené jako názvů NFSv4 svazky. Vylepšený mechanizmus zapůjčení na základě zapůjčení souborů v protokolu názvů NFSv4 se používá k `I/O` monitorování zón. 

> [!IMPORTANT]
> Chcete-li vytvořit podporovanou konfiguraci, je nutné nasadit svazky dat HANA a protokolů jako svazky NFSv 4.1 a připojit je pomocí protokolu NFSv 4.1. Konfigurace automatického převzetí služeb při selhání hostitele HANA s pohotovostním uzlem není v NFSv3 podporovaná.

![Přehled vysoké dostupnosti SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

V předchozím diagramu, který následuje SAP HANA doporučení pro síť, jsou tři podsítě zastoupeny v jedné virtuální síti Azure: 
* Pro komunikaci klientů
* Pro komunikaci se systémem úložiště
* Pro interní komunikaci mezi uzly HANA

Svazky NetApp Azure jsou v samostatné podsíti, [delegované na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

V tomto příkladu konfigurace jsou podsítě:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (delegovaná podsíť pro Azure NetApp Files)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Azure NetApp Files 

Než budete pokračovat v nastavení infrastruktury Azure NetApp Files, Seznamte se s [Azure NetApp Files dokumentaci][anf-azure-doc]. 

Azure NetApp Files je k dispozici v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Podívejte se, jestli vybraná oblast Azure nabízí Azure NetApp Files.  

Informace o dostupnosti Azure NetApp Files podle oblasti Azure najdete v tématu [Azure NetApp Files dostupnosti podle oblasti Azure][anf-avail-matrix].  

Než začnete s nasazením Azure NetApp Files, požádejte o [registraci pro Azure NetApp Files instrukcí pro][anf-register]Azure NetApp Files. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure NetApp Files  

V následujících pokynech se předpokládá, že jste už nasadili službu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Azure NetApp Files prostředky a virtuální počítače, kde budou připojené prostředky Azure NetApp Files, musí být nasazené ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem.  

1. Pokud jste ještě neimplementovali prostředky, požádejte o [registraci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Podle pokynů v části [Vytvoření účtu NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)vytvořte ve vybrané oblasti Azure účet NetApp.  

3. Nastavte fond kapacit Azure NetApp Files podle pokynů v části [nastavení fondu kapacity Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  

   Architektura HANA uvedená v tomto článku používá jeden Azure NetApp Files fond kapacit na úrovni služeb úrovně *Ultra* . Pro úlohy HANA v Azure doporučujeme použít Azure NetApp Files [úroveň služeb](../../../azure-netapp-files/azure-netapp-files-service-levels.md)pro *Ultra* nebo *Premium* .  

4. Delegování podsítě na Azure NetApp Files, jak je popsáno v pokynech [delegování podsítě na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Nasaďte Azure NetApp Files svazky podle pokynů v tématu [vytvoření svazku NFS pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

   Když nasazujete svazky, ujistěte se, že jste vybrali verzi **nfsv 4.1** . Nasaďte svazky v určené Azure NetApp Files [podsíti](/rest/api/virtualnetwork/subnets). IP adresy svazků Azure NetApp se přiřazují automaticky. 
   
   Pamatujte, že prostředky Azure NetApp Files a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem. Například **HN1**-data-Mnt00001, **HN1**-log-mnt00001 atd. jsou názvy svazků **a NFS://10.9.0.4/HN1-data**-mnt00001, NFS://10.9.0.4/**HN1**-log-mnt00001 atd. jsou cesty k souborům pro Azure NetApp Files svazky.  

   * Volume **HN1**-data-mnt00001 (NFS://10.9.0.4/**HN1**-data-mnt00001)
   * Volume **HN1**-data-mnt00002 (NFS://10.9.0.4/**HN1**-data-mnt00002)
   * Volume **HN1**-log-mnt00001 (NFS://10.9.0.4/**HN1**-log-mnt00001)
   * Volume **HN1**-log-mnt00002 (NFS://10.9.0.4/**HN1**-log-mnt00002)
   * svazek **HN1**– Shared (NFS://10.9.0.4/**HN1**– Shared)
   
   V tomto příkladu jsme pro každý datový svazek HANA a objem protokolu používali samostatný svazek Azure NetApp Files. Pro zajištění vyšší nákladů optimalizovaných pro menší nebo neproduktivní systémy je možné umístit všechna připojení dat na jeden svazek a všechny protokoly se připojí na jiný jednotlivý svazek.  

### <a name="important-considerations"></a>Důležité informace

Vzhledem k tomu, že vytváříte Azure NetApp Files pro SAP HANA možností horizontálního navýšení kapacity při použití uzlů, pamatujte na následující důležité informace:

- Minimální fond kapacit je 4 tebibytes (TiB).  
- Minimální velikost svazku je 100 gibibajtech (GiB).
- Azure NetApp Files a všechny virtuální počítače, na kterých budou připojené Azure NetApp Files svazky, musí být ve stejné virtuální síti Azure nebo v [partnerských virtuálních sítích](../../../virtual-network/virtual-network-peering-overview.md) ve stejné oblasti.  
- Vybraná virtuální síť musí mít podsíť, která je delegovaná na Azure NetApp Files.
- Propustnost Azure NetApp Filesho svazku je funkcí kvóty svazku a úrovně služeb, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Při určování velikosti svazků Azure NetApp pro HANA se ujistěte, že výsledná propustnost splňuje požadavky na systém HANA.  
- Pomocí Azure NetApp Files [zásady exportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)můžete řídit povolené klienty, typ přístupu (jen pro čtení i zápis, jen pro čtení atd.). 
- Funkce Azure NetApp Files ještě nezohledňují zóny. V současné době není tato funkce nasazena ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.  

> [!IMPORTANT]
> U SAP HANA úloh je nízká latence kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Určení velikosti databáze HANA v Azure NetApp Files

Propustnost Azure NetApp Filesho svazku je funkce velikosti svazku a úrovně služby, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Při návrhu infrastruktury pro SAP v Azure si pamatujte na některé minimální požadavky na úložiště SAP, které se přeloží na minimální propustnost:

- Čtení a zápis v/Hana/log 250 megabajtů za sekundu (MB/s) s velikostí 1 MB I/O.  
- Aktivita čtení minimálně 400 MB/s pro/Hana/data pro velikosti vstupně-výstupních operací 16-MB a 64-MB  
- Aktivita zápisu alespoň 250 MB/s pro/Hana/data s velikostí I/O 16 MB a 64-MB. 

[Omezení propustnosti Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) na 1 TIB kvót:
- Premium Storage vrstva – 64 MiB/s  
- Úroveň úložiště úrovně Ultra – 128 MiB/s  

Aby splňovala požadavky na minimální propustnost SAP pro data a protokol, a pokyny pro/Hana/Shared, budou Doporučené velikosti:

| Svazek | Velikost<br>Premium Storage úroveň | Velikost<br>Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | 1xRAM na 4 pracovní uzly | 1xRAM na 4 pracovní uzly | V3 nebo v 4.1 |

Konfigurace SAP HANA pro rozložení prezentovaná v tomto článku, která využívá Azure NetApp Files úrovni úložiště Ultra Storage, by byla:

| Svazek | Velikost<br>Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | V3 nebo v 4.1 |

> [!NOTE]
> Níže uvedená doporučení pro nastavení velikosti Azure NetApp Files jsou zaměřená na splnění minimálních požadavků, které SAP doporučuje pro poskytovatele infrastruktury. V reálných scénářích nasazení a úloh nemusí být tyto velikosti dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte je na základě požadavků konkrétního zatížení.  

> [!TIP]
> Velikost svazků Azure NetApp Files lze dynamicky měnit bez nutnosti *odpojení* svazků, zastavení virtuálních počítačů nebo zastavení SAP HANA. Tento přístup umožňuje flexibilní splnění očekávané i nepředvídatelné propustnosti aplikace.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Nasazení virtuálních počítačů se systémem Linux prostřednictvím Azure Portal

Nejprve je třeba vytvořit svazky Azure NetApp Files. Pak proveďte následující kroky:
1. Vytvořte [podsítě virtuální sítě Azure](../../../virtual-network/virtual-network-manage-subnet.md) ve [virtuální síti Azure](../../../virtual-network/virtual-networks-overview.md). 
1. Nasaďte virtuální počítače. 
1. Vytvořte další síťová rozhraní a připojte síťová rozhraní k odpovídajícím virtuálním počítačům.  

   Každý virtuální počítač má tři síťová rozhraní, která odpovídají třem podsítím virtuální sítě Azure ( `client` `storage` a `hana` ). 

   Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux v Azure s několika síťovými kartami](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> U SAP HANA úloh je nízká latence kritická. Abyste dosáhli nízké latence, spolupracujte s vaším zástupcem Microsoftu, abyste zajistili, že virtuální počítače a Azure NetApp Files svazky budou nasazeny v těsné blízkosti. Při [připojování nového SAP HANA systému](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) , který používá Azure NetApp Files SAP HANA, odešlete potřebné informace. 
 
V dalších pokynech se předpokládá, že jste už vytvořili skupinu prostředků, virtuální síť Azure a tři podsítě virtuální sítě Azure: `client` `storage` a `hana` . Když nasadíte virtuální počítače, vyberte podsíť klienta, aby bylo síťové rozhraní klienta primárním rozhraním virtuálních počítačů. Budete taky muset nakonfigurovat explicitní trasu k Azure NetApp Files delegované podsíti přes bránu podsítě úložiště. 

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je SAP – certifikováno pro SAP HANA na specifických typech virtuálních počítačů, které používáte. Seznam SAP HANA certifikovaných typů virtuálních počítačů a verzí operačních systémů pro tyto typy najdete na webu [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Kliknutím na podrobnosti o typu tohoto virtuálního počítače získáte úplný seznam SAP HANA podporovaných verzí operačního systému pro daný typ.  

1. Vytvořte skupinu dostupnosti pro SAP HANA. Ujistěte se, že jste nastavili maximální doménu aktualizace.  

2. Vytvořte tři virtuální počítače (**hanadb1**, **hanadb2**, **hanadb3**) pomocí následujících kroků:  

   a. Použijte Red Hat Enterprise Linux image v galerii Azure, která je podporovaná pro SAP HANA. V tomto příkladu jsme použili image RHEL-SAP-HA 7,6.  

   b. Vyberte skupinu dostupnosti, kterou jste předtím vytvořili pro SAP HANA.  

   c. Vyberte podsíť klientské virtuální sítě Azure. Vyberte možnost [akcelerovaná síť](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   Když nasadíte virtuální počítače, název síťového rozhraní se vygeneruje automaticky. V těchto pokynech pro zjednodušení budeme odkazovat na automaticky generovaná síťová rozhraní, která jsou připojená k podsíti virtuální sítě Azure Client, jako **hanadb1-Client**, **hanadb2-Client** a **hanadb3-Client**. 

3. Vytvořte tři síťová rozhraní, jednu pro každý virtuální počítač, pro `storage` podsíť virtuální sítě (v tomto příkladu **hanadb1-Storage**, **hanadb2-Storage** a **hanadb3-Storage**).  

4. Vytvořte tři síťová rozhraní, jednu pro každý virtuální počítač, pro `hana`  podsíť virtuální sítě (v tomto příkladu **hanadb1-Hana**, **hanadb2-Hana** a **hanadb3-Hana**).  

5. Připojte nově vytvořená virtuální síťová rozhraní k odpovídajícím virtuálním počítačům pomocí následujících kroků:  

    a. Přejít na virtuální počítač v [Azure Portal](https://portal.azure.com/#home).  

    b. V levém podokně vyberte **Virtual Machines**. Vyfiltrujte název virtuálního počítače (například **hanadb1**) a potom vyberte virtuální počítač.  

    c. V podokně **Přehled** vyberte možnost zastavit, čímž **zrušíte** přidělení virtuálního počítače.  

    d. Vyberte **sítě** a pak připojte síťové rozhraní. V rozevíracím seznamu **připojit síťové rozhraní** vyberte již vytvořená síťová rozhraní pro `storage` `hana` podsítě a.  
    
    e. Vyberte **Uložit**. 
 
    f. Opakujte kroky b až e pro zbývající virtuální počítače (v našem příkladu  **hanadb2** a **hanadb3**).
 
    například Virtuální počítače teď ponechte v zastaveném stavu. V dalším kroku povolíte [urychlené síťové služby](../../../virtual-network/create-vm-accelerated-networking-cli.md) pro všechna nově připojená síťová rozhraní.  

6. Pomocí následujících kroků povolte akcelerované sítě pro další síťová rozhraní `storage` pro `hana` podsítě a:  

    a. Otevřete [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) v [Azure Portal](https://portal.azure.com/#home).  

    b. Spusťte následující příkazy, aby bylo možné zrychlit sítě pro další síťová rozhraní, která jsou připojena k `storage` `hana` podsítím a.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Spusťte virtuální počítače pomocí následujících kroků:  

    a. V levém podokně vyberte **Virtual Machines**. Vyfiltrujte název virtuálního počítače (například **hanadb1**) a pak ho vyberte.  

    b. V podokně **Přehled** vyberte možnost **Spustit**.  

## <a name="operating-system-configuration-and-preparation"></a>Konfigurace a příprava operačního systému

Pokyny v dalších částech jsou předem opraveny s jednou z následujících částí:
* **[A]**: platí pro všechny uzly
* **[1]**: platí pouze pro uzel 1
* **[2]**: platí pouze pro uzel 2
* **[3]**: platí pouze pro uzel 3

Pomocí následujících kroků nakonfigurujte a připravte operační systém:

1. **[A]** Udržujte hostitelské soubory na virtuálních počítačích. Zahrnout položky pro všechny podsítě Do tohoto příkladu byly přidány následující položky `/etc/hosts` .  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** přidejte síťovou trasu, aby komunikace se Azure NetApp Filesa prochází přes síťové rozhraní úložiště.  

   V tomto příkladu se použije `Networkmanager` ke konfiguraci další síťové trasy. V následujících pokynech se předpokládá, že síťové rozhraní úložiště je `eth1` .  
   Nejprve určete název připojení zařízení `eth1` . V tomto příkladu je název připojení zařízení `eth1` `Wired connection 1` .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Potom nakonfigurujte další trasu na Azure NetApp Files delegovanou síť prostřednictvím `eth1` .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Restartujte virtuální počítač, aby se změny aktivovaly.  

3. **[A]** nainstalujte balíček klienta NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Připravte operační systém na spouštění SAP HANA v Azure NetApp se systémem souborů NFS, jak je popsáno v tématu [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]. Vytvořte konfigurační soubor */etc/sysctl.d/NetApp-Hana.conf* pro nastavení konfigurace NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** vytvořit konfigurační soubor */etc/sysctl.d/MS-AZ.conf* s dalšími nastaveními optimalizace.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> Vyhněte se nastavení net.ipv4.ip_local_port_range a net.ipv4.ip_local_reserved_ports explicitně v konfiguračních souborech sysctl, aby mohl hostitelský agent SAP spravovat rozsahy portů. Další podrobnosti najdete v tématu SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

5. **[A]** upravte nastavení sunRPC podle doporučení v [aplikacích SAP NetApp na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat pro konfiguraci Hana.

    Nakonfigurujte RHEL podle popisu v tématu SAP Note [2292690], [2455582], [2593824] a <https://access.redhat.com/solutions/2447641> .

    > [!NOTE]
    > Při instalaci HANA 2,0 SP04 budete muset nainstalovat balíček `compat-sap-c++-7` , jak je popsáno v tématu SAP note [2593824], než budete moct nainstalovat SAP HANA. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Připojení Azure NetApp Filesch svazků

1. **[A]** vytvoření přípojných bodů pro databázové svazky Hana  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** vytvořte adresáře pro/usr/SAP na **HN1**-Shared pro daný uzel.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** ověřte nastavení domény NFS. Ujistěte se, že je doména nakonfigurovaná jako výchozí doména Azure NetApp Files, tj. **`defaultv4iddomain.com`** a mapování je nastavené na **nikdo**.  

    > [!IMPORTANT]
    > Ujistěte se, že jste na virtuálním počítači nastavili doménu systému souborů NFS, `/etc/idmapd.conf` aby odpovídala výchozí konfiguraci domény v Azure NetApp Files: **`defaultv4iddomain.com`** . Pokud dojde k neshodě mezi konfigurací domény v klientovi NFS (tj. virtuálním počítačem) a serverem NFS, tj. konfigurací Azure NetApp, pak se budou zobrazovat oprávnění k souborům na svazcích Azure NetApp, které jsou připojené k virtuálním počítačům `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** ověřte `nfs4_disable_idmapping` . Měl by být nastaven na **Y**. Pokud chcete vytvořit adresářovou strukturu `nfs4_disable_idmapping` , kde se nachází, spusťte příkaz Mount. V/sys/modules nebudete moct ručně vytvořit adresář, protože přístup je vyhrazený pro jádro nebo ovladače.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Další podrobnosti o tom, jak změnit `nfs4_disable_idmapping` parametr, naleznete v tématu https://access.redhat.com/solutions/1749883 .

6. **[A]** připojte sdílené svazky Azure NetApp Files.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** připojte svazky specifické pro uzel v **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** připojte svazky specifické pro uzel v **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** připojte svazky specifické pro uzel v **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** ověřte, že jsou všechny svazky Hana připojené k protokolu NFS verze **názvů NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Instalace  

V tomto příkladu pro nasazení SAP HANA v konfiguraci škálování na více instancí s pohotovostním uzlem s Azure jsme použili HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Příprava instalace HANA

1. **[A]** před instalací Hana nastavte kořenové heslo. Po dokončení instalace můžete kořenové heslo zakázat. Spustit jako `root` příkaz `passwd`  

2. **[1]** ověřte, že se můžete přihlásit přes SSH do **hanadb2** a **hanadb3**, aniž by se zobrazila výzva k zadání hesla.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** nainstalujte další balíčky, které se vyžadují pro Hana 2,0 SP4. Další informace najdete v tématu SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Změňte vlastnictví SAP HANA `data` a `log` adresářů na **HN1** ADM.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** dočasně zakáže bránu firewall, aby nebránila instalaci Hana. Po dokončení instalace HANA ji můžete znovu povolit. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Instalace HANA

1. **[1]** nainstalujte SAP HANA podle pokynů v [Průvodci instalací a aktualizací SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). V tomto příkladu nainstalujeme SAP HANA horizontálního navýšení kapacity s hlavním, jedním pracovním a jedním pohotovostním uzlem.  

   a. Spusťte program **hdblcm** z adresáře instalačního softwaru Hana. Použijte `internal_network` parametr a předejte adresní prostor pro podsíť, který se používá pro interní komunikaci mezi uzly v Hana.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Do příkazového řádku zadejte následující hodnoty:

     * Pro **zvolit akci**: zadejte **1** (pro instalaci).
     * **Další součásti pro instalaci**: zadejte **2, 3**
     * Pro instalační cestu: stiskněte klávesu ENTER (výchozí hodnota je/Hana/Shared).
     * **Název místního hostitele**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * V části chcete **Přidat hostitele do systému?**: zadejte **y** .
     * Pro **názvy hostitelů oddělených čárkami, které se mají přidat**: zadejte **hanadb2, hanadb3**
     * Pro **kořenové uživatelské jméno** [root]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro role pro hostitele hanadb2: zadejte **1**  (pro pracovního procesu).
     * Pro **skupinu převzetí služeb při selhání hostitele** pro hostitele hanadb2 [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **číslo oddílu úložiště** pro hostitele hanadb2 [<<assign automatically>>]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **skupinu Worker** pro hanadb2 hostitele [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **možnost Vybrat role** pro hostitele hanadb3: zadejte **2** (pro pohotovostní režim)
     * Pro **skupinu převzetí služeb při selhání hostitele** pro hostitele hanadb3 [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **skupinu Worker** pro hanadb3 hostitele [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **SAP HANA ID systému**: zadejte **HN1** .
     * **Číslo instance** [00]: zadejte **03**
     * Pro **skupinu Local Host Worker** [default]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **možnost vybrat využití systému/zadejte index [4]**: zadejte **4** (pro vlastní).
     * Pro **umístění datových svazků** [/Hana/data/HN1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **umístění svazků protokolu** [/Hana/log/HN1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Chcete-li **omezit maximální přidělení paměti?** [n]: zadejte **n**
     * Pro **název hostitele certifikátu pro hostitele hanadb1** [hanadb1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **název hostitele certifikátu pro hostitele hanadb2** [hanadb2]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **název hostitele certifikátu pro hostitele hanadb3** [hanadb3]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * **Heslo správce systému (hn1adm)**: zadejte heslo.
     * **Heslo uživatele systémové databáze (systém)**: zadejte heslo systému.
     * Pro **potvrzení hesla uživatele systémové databáze (systém)**: zadejte heslo systému
     * Pro **restartování systému po restartování počítače?** [n]: zadejte **n** 
     * Chcete **pokračovat (a/n)**: ověřit souhrn a pokud vše vypadá dobře, zadejte **y** .


2. **[1]** ověřit global.ini  

   Zobrazte global.ini a ujistěte se, že je nastavená konfigurace pro interní SAP HANA komunikace mezi uzly. Ověřte část **komunikace** . Měl by mít adresní prostor pro `hana` podsíť a `listeninterface` měl by být nastaven na `.internal` . Ověřte část **internal_hostname_resolution** . Měl by mít IP adresy pro virtuální počítače HANA, které patří do `hana` podsítě.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** přidejte mapování hostitele, aby se zajistilo, že se IP adresy klientů použijí pro komunikaci klientů. Přidejte oddíl `public_host_resolution` a přidejte odpovídající IP adresy z klientské podsítě.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** restartujte SAP HANA, aby se změny aktivovaly.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** ověřte, zda klientské rozhraní bude používat IP adresy z `client` podsítě pro komunikaci.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Informace o tom, jak ověřit konfiguraci, najdete v tématu SAP Note [2183363 – konfigurace SAP HANA interní sítě](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** znovu povolit bránu firewall.  
   - Zastavit HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Opětovné povolení brány firewall
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Otevřete potřebné porty brány firewall.

       > [!IMPORTANT]
       > Vytvořte pravidla brány firewall, která umožní komunikaci mezi uzly HANA a klientský provoz. Požadované porty jsou uvedené na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem. V tomto scénáři s použitým systémovým číslem 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Spustit HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Pokud chcete optimalizovat SAP HANA pro základní úložiště Azure NetApp Files, nastavte následující parametry SAP HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**zapnuto**
   - `async_write_submit_active`**zapnuto**
   - `async_write_submit_blocks`**vše**

   Další informace najdete v tématu [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]. 

   Počínaje systémy SAP HANA 2,0 můžete nastavit parametry v `global.ini` . Další informace najdete v tématu SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Pro systémy SAP HANA 1,0 a novější verze SPS12 a novější lze tyto parametry nastavit během instalace, jak je popsáno v tématu SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Úložiště, které používá Azure NetApp Files, má omezení velikosti souboru na 16 terabajtů (TB). SAP HANA nemá implicitně informace o omezení úložiště a při dosažení limitu velikosti souboru 16 TB nebude automaticky vytvářet nový datový soubor. Jak SAP HANA se pokusí zvětšit soubor nad rámec 16 TB, tento pokus bude mít za následek chyby a nakonec v případě havárie serveru indexu. 

   > [!IMPORTANT]
   > Chcete-li zabránit SAP HANA pokusit se o zvětšení datových souborů nad rámec [velikosti 16 TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) subsystému úložiště, nastavte v nástroji následující parametry `global.ini` .  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 Další informace najdete v tématu SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Upozorňujeme na SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Test SAP HANA převzetí služeb při selhání 

1. Simulujte selhání uzlu na SAP HANA pracovním uzlu. Postupujte následovně: 

   a. Před simulací havárie uzlu spusťte následující příkazy jako **HN1** ADM pro zachycení stavu prostředí:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Chcete-li simulovat selhání uzlu, spusťte následující příkaz jako kořen v pracovním uzlu, který je v tomto případě **hanadb2** :  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Sledujte systém pro dokončení převzetí služeb při selhání. Po dokončení převzetí služeb při selhání Zachyťte stav, který by měl vypadat takto:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Když se uzel vyskytne v jádře jádra, vyhněte se prodlevám při převzetí služeb při selhání SAP HANA nastavením `kernel.panic` na 20 sekund na *všech* virtuálních počítačích Hana. Konfigurace se provádí v `/etc/sysctl` . Změny aktivujete restartováním virtuálních počítačů. Pokud se tato změna neprovede, převzetí služeb při selhání může trvat 10 nebo více minut, než se uzel dostaná v případě nouzového jádra.  

2. Pomocí následujícího postupu ukončete názvový server:

   a. Před testem zkontrolujte stav prostředí spuštěním následujících příkazů jako **HN1** ADM:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Spusťte následující příkazy jako **HN1** ADM na aktivním hlavním uzlu, který je v tomto případě **hanadb1** :  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Pohotovostní uzel **hanadb3** převezme jako hlavní uzel. Zde je po dokončení testu převzetí služeb při selhání proveden stav prostředků:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Restartujte instanci HANA na **hanadb1** (to znamená na stejném virtuálním počítači, kde byl názvový server ukončen). Uzel **hanadb1** se znovu připojí k prostředí a zachová se jeho pohotovostní role.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po zahájení SAP HANA v **hanadb1** očekávat následující stav:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Znovu ukončete názvový server na aktuálně aktivním hlavním uzlu (to znamená na uzlu **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   **Hanadb1** uzlu obnoví roli hlavního uzlu. Po dokončení testu převzetí služeb při selhání bude mít tento stav následující:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Začněte SAP HANA na **hanadb3**, která bude připravena sloužit jako pohotovostní uzel.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po zahájení SAP HANA v **hanadb3** bude tento stav vypadat takto:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuálních počítačích)][sap-hana-ha].
