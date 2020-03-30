---
title: Sap HANA horizontální navýšení kapacity s pohotovostním režimem se soubory Azure NetApp na SLES | Dokumenty společnosti Microsoft
description: Průvodce vysokou dostupností pro SAP NetWeaver na SUSE Linux Enterprise Server se soubory Azure NetApp pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: c594ef3a62d45fb68002ec2b21fb89115f7a30af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565804"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Tento článek popisuje, jak nasadit vysoce dostupný systém SAP HANA v horizontálním navýšení kapacity konfigurace s pohotovostním režimu na virtuálních počítačích Azure (VM) pomocí [Souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) pro sdílené svazky úložiště.  

V ukázkových konfiguracích, příkazech instalace a tak dále je instance HANA **03** a ID systému HANA je **HN1**. Příklady jsou založeny na HANA 2.0 SP4 a SUSE Linux Enterprise Server pro SAP 12 SP4. 

Než začnete, podívejte se na následující poznámky a dokumenty SAP:

* [Dokumentace ke službě Azure NetApp Files][anf-azure-doc] 
* SAP Poznámka [1928533] obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* POZNÁMKA SAP [2015553]: Uvádí předpoklady pro nasazení softwaru SAP podporovaný SAP v Azure.
* SAP Poznámka [2205917]: Obsahuje doporučená nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [1944799]: Obsahuje sap pokyny pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [2178632]: Obsahuje podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure
* SAP Note [2191498]: Obsahuje požadovanou verzi SAP Host Agent pro Linux v Azure
* SAP Note [2243692]: Obsahuje informace o licencování SAP na Linuxu v Azure
* SAP Note [1984787]: Obsahuje obecné informace o SUSE Linux Enterprise Server 12
* SAP Note [1999351:]Obsahuje další informace o řešení potíží pro rozšíření rozšířeného monitorování Azure pro SAP
* SAP Note [1900823]: Obsahuje informace o požadavcích na úložiště SAP HANA
* [SAP Community Wiki:](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)Obsahuje všechny požadované POZNÁMKY SAP pro Linux
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [SUSE SAP HA Průvodce osvědčenými postupy][suse-ha-guide]: Obsahuje všechny požadované informace pro nastavení NetWeaver vysoké dostupnosti a SAP HANA systémové replikace v místním prostředí (které mají být použity jako obecný směrný plán, poskytují mnohem podrobnější informace)
* [SUSE Rozšíření o vysokou dostupnost 12 SP3 Poznámky k verzi][suse-ha-12sp3-relnotes]
* [Aplikace NetApp SAP v Microsoft Azure pomocí souborů Azure NetApp][anf-sap-applications-azure]
* [SAP HANA v systémech NetApp se síťovým systémem souborů (NFS):](https://www.netapp.com/us/media/tr-4435.pdf)Průvodce konfigurací, který obsahuje informace o nastavení SAP HANA pomocí služby Azure NFS by NetApp


## <a name="overview"></a>Přehled

Jednou z metod pro dosažení vysoké dostupnosti HANA je konfigurace automatického převzetí služeb při selhání hostitele. Chcete-li nakonfigurovat automatické převzetí služeb při selhání hostitele, přidejte jeden nebo více virtuálních počítačů do systému HANA a nakonfigurujte je jako pohotovostní uzly. Pokud se aktivní uzel nezdaří, automaticky převezme pohotovostní uzel. V prezentované konfiguraci s virtuálními počítači Azure dosáhnete automatického převzetí služeb při selhání pomocí [systému souborů NFS v souborech Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

> [!NOTE]
> Pohotovostní uzel potřebuje přístup ke všem svazkům databáze. Svazky HANA musí být připojeny jako svazky NFSv4. Vylepšený blokovací mechanismus založený na zapůjčení souborů v `I/O` protokolu NFSv4 se používá pro oplocení. 

> [!IMPORTANT]
> Chcete-li vytvořit podporovanou konfiguraci, musíte nasadit svazky dat HANA a protokolovat jako svazky nfsv4.1 a připojit je pomocí protokolu NFSv4.1. Konfigurace automatického převzetí služeb při selhání hostitele HANA s pohotovostním uzlem není podporována systémem NFSv3.

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

V předchozím diagramu, který následuje podle doporučení sítě SAP HANA, jsou v rámci jedné virtuální sítě Azure reprezentovány tři podsítě: 
* Pro komunikaci s klienty
* Pro komunikaci s úložným systémem
* Pro interní komunikaci mezi uzlovacími předpisy HANA

Svazky Azure NetApp jsou v samostatné podsíti [delegované na soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

V tomto příkladu konfigurace jsou podsítě:  

  - `client`10.23.0.0/24  
  - `storage`10.23.2.0/24  
  - `hana`10.23.3.0/24  
  - `anf`10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Souborů Azure NetApp 

Než budete pokračovat v instalaci infrastruktury Azure NetApp Files, seznamte se s [dokumentací k souborům Azure NetApp][anf-azure-doc]. 

Soubory Azure NetApp jsou dostupné v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Zkontrolujte, jestli vybraná oblast Azure nabízí soubory Azure NetApp.  

Informace o dostupnosti souborů Azure NetApp podle oblasti Azure najdete v tématu [Dostupnost souborů Azure NetApp podle oblasti Azure][anf-avail-matrix].  

Před nasazením souborů Azure NetApp požádejte o připojení k souborům Azure NetApp tak, že přejdete na [příkaz Zaregistrovat se k souborům Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků souborů Azure NetApp  

Následující pokyny předpokládají, že jste už virtuální [síť Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)nasadili. Prostředky Azure NetApp Files a virtuální počítače, kde se namontují prostředky Azure NetApp Files, musí být nasazené ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem.  

1. Pokud jste prostředky ještě nenasadili, požádejte o [připojení k souborům Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Vytvořte si účet NetApp ve vybrané oblasti Azure podle pokynů v části [Vytvoření účtu NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Nastavte fond kapacity souborů Azure NetApp podle pokynů v [části Nastavení fondu kapacity souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   Architektura HANA prezentovaná v tomto článku používá jeden fond kapacity souborů Azure NetApp na úrovni *služby Ultra.* Pro úlohy HANA v Azure doporučujeme použít [úroveň služby](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)Azure NetApp Files *Ultra* nebo *Premium* .  

4. Delegujte podsíť na soubory Azure NetApp, jak je popsáno v pokynech v [části Delegovat podsíť na soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Nasazení svazků souborů Azure NetApp podle pokynů v části [Vytvoření svazku systému souborů nfs pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

   Při nasazování svazků nezapomeňte vybrat verzi **NFSv4.1.** V současné době přístup k NFSv4.1 vyžaduje další whitelisting. Nasazení svazků v určené [podsíti](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Soubory Azure NetApp . 
   
   Nezapomeňte, že prostředky Azure NetApp Files a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerem. Například **HN1**-data-mnt00001, **HN1**-log-mnt00001 a tak dále, jsou názvy svazků a nfs://10.23.1.5/**HN1**-data-mnt00001, nfs://10.23.1.4/**HN1**-log-mnt00001 a tak dále, jsou cesty k souborům pro svazky souborů Azure NetApp.  

   * svazek **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * svazek **HN1**-data-mnt00002 (nfs://10.23.1.6/**HN1**-data-mnt00002)
   * svazek **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * svazek **HN1**-log-mnt00002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * svazek **HN1**-shared (nfs://10.23.1.4/**HN1**-shared)
   
   V tomto příkladu jsme použili samostatný svazek souborů Azure NetApp pro každý svazek HANA a protokolu. Pro nákladově optimalizovanější konfiguraci v menších nebo neproduktivních systémech je možné umístit všechna připojení dat a všechna připojení protokolů na jeden svazek.  

### <a name="important-considerations"></a>Důležité informace

Při vytváření souborů Azure NetApp pro SAP NetWeaver na architektuře SUSE s vysokou dostupností si uvědomte následující důležité aspekty:

- Bazén s minimální kapacitou je 4 tebibytes (TiB).  
- Minimální velikost svazku je 100 gibibytes (GiB).
- Soubory Azure NetApp a všechny virtuální počítače, kde se připojí svazky souborů Azure NetApp, musí být ve stejné virtuální síti Azure nebo ve [virtuálních sítích s partnerským vztahem](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ve stejné oblasti.  
- Vybraná virtuální síť musí mít podsíť, která je delegovaná na soubory Azure NetApp.
- Propustnost svazku souborů Azure NetApp je funkce kvóty svazku a úrovně služeb, jak je popsáno na [úrovni služby pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Při dimenzování svazků HANA Azure NetApp se ujistěte, že výsledná propustnost splňuje požadavky systému HANA.  
- Pomocí [zásad exportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)souborů Azure NetApp můžete řídit povolené klienty, typ přístupu (jen pro čtení a zápis, jen pro čtení a tak dále). 
- Funkce Soubory Azure NetApp ještě není zónová. V současné době se tato funkce nenasadí ve všech zónách dostupnosti v oblasti Azure. Uvědomte si potenciální důsledky latence v některých oblastech Azure.  
-  

> [!IMPORTANT]
> Pro úlohy SAP HANA je důležitá nízká latence. Spolupracujte se zástupcem Microsoftu a ujistěte se, že virtuální počítače a svazky Souborů Azure NetApp jsou nasazeny v těsné blízkosti.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Změna velikosti databáze HANA v souborech Azure NetApp

Propustnost svazku souborů Azure NetApp je funkce velikosti svazku a úrovně služby, jak je popsáno na [úrovni služby pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Při navrhování infrastruktury pro SAP v Azure si uvědomte některé minimální požadavky na úložiště od SAP, které se promítají do vlastností minimální propustnost:

- Povolit čtení a zápis na /hana/log 250 megabajtů za sekundu (MB/s) s velikostmi vstupně-vstupně-v. 1 MB.  
- Povolte aktivitu čtení alespoň 400 MB/s pro /hana/data pro velikosti 16 MB a 64 MB vstupně-va.  
- Povolte aktivitu zápisu alespoň 250 MB/s pro /hana/data s velikostmi 16 MB a 64 MB vstupně-v/v. 

[Limity propustnost souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TiB kvóty svazku jsou:
- Úroveň úložiště Premium – 64 MiB/s  
- Úroveň ultra úložiště - 128 MiB/s  

Chcete-li splnit požadavky sap minimální propustnost pro data a protokol a pokyny pro /hana/shared, doporučené velikosti by:

| Svazek | Velikost<br>Úroveň úložiště Premium | Velikost<br>Úroveň úložiště Ultra | Podporovaný protokol nfs |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | Maximální (512 GB, 1xRAM) na 4 pracovní uzly | Maximální (512 GB, 1xRAM) na 4 pracovní uzly | v3 nebo v4.1 |

Konfigurace SAP HANA pro rozložení, které je uvedeno v tomto článku, pomocí Azure NetApp Files Ultra Storage vrstvy, by bylo:

| Svazek | Velikost<br>Úroveň úložiště Ultra | Podporovaný protokol nfs |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TB | v4.1 |
| /hana/log/mnt00002 | 2 TB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 TB | v3 nebo v4.1 |

> [!NOTE]
> Azure NetApp soubory velikosti doporučení zde jsou zaměřeny na splnění minimální požadavky, které SAP doporučuje pro své poskytovatele infrastruktury. V reálných nasazeních zákazníků a scénářích úloh nemusí být tyto velikosti dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte se na základě požadavků konkrétního pracovního vytížení.  

> [!TIP]
> Velikost svazků Azure NetApp Files můžete změnit dynamicky, aniž byste museli *odpojit* svazky, zastavit virtuální počítače nebo zastavit SAP HANA. Tento přístup umožňuje flexibilitu při plnění očekávaných i nepředvídaných požadavků na propustnost vaší aplikace.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Nasazení virtuálních počítačů S Linuxem přes portál Azure

Nejprve je třeba vytvořit svazky souborů Azure NetApp. Potom proveďte následující kroky:
1. Vytvořte [podsítě virtuálnísítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) ve [virtuální síti Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). 
1. Nasaďte virtuální chod. 
1. Vytvořte další síťová rozhraní a připojte síťová rozhraní k odpovídajícím virtuálním mům.  

   Každý virtuální počítač má tři síťová rozhraní, která odpovídají třem`client` `storage` podsítím virtuální sítě Azure ( , a `hana`). 

   Další informace najdete [v tématu Vytvoření virtuálního počítače SIP v Azure s více kartami síťového rozhraní](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Pro úlohy SAP HANA je důležitá nízká latence. Chcete-li dosáhnout nízké latence, spolupracujte se zástupcem Microsoftu a ujistěte se, že virtuální počítače a svazky souborů Azure NetApp jsou nasazeny v těsné blízkosti. Když [zapisujete nový systém SAP HANA,](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) který používá soubory SAP HANA Azure NetApp, odešlete potřebné informace. 
 
Další pokyny předpokládají, že jste už vytvořili skupinu prostředků, virtuální síť Azure a `client` `storage` tři `hana`podsítě virtuální sítě Azure: a . Při nasazování virtuálních počítačů vyberte podsíť klienta, aby klientské síťové rozhraní bylo primárním rozhraním na virtuálních počítačích. Budete také muset nakonfigurovat explicitní trasu k delegované podsíti Soubory Azure NetApp prostřednictvím brány podsítě úložiště. 

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je sap certified pro SAP HANA na konkrétní typy virtuálních počítačů, které používáte. Seznam typů virtuálních počítačů s certifikací SAP HANA a verzí operačního systému pro tyto typy najdete na webu [platformy IaaS s certifikací SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Kliknutím do podrobností o uvedeném typu virtuálního uživatele získáte úplný seznam verzí operačního systému s podporou SAP HANA pro tento typ.  

1. Vytvořte sadu dostupnosti pro SAP HANA. Ujistěte se, že jste nastavili doménu s maximální aktualizací.  

2. Vytvořte tři virtuální počítače (**hanadb1**, **hanadb2**, **hanadb3**) následujícím postupem:  

   a. Použijte bitovou kopii SLES4SAP v galerii Azure, která je podporovaná pro SAP HANA. V tomto příkladu jsme použili obrázek SLES4SAP 12 SP4.  

   b. Vyberte sadu dostupnosti, kterou jste vytvořili dříve pro SAP HANA.  

   c. Vyberte podsíť virtuální sítě Azure klienta. Vyberte [možnost Akcelerovaná síť](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   Při nasazení virtuálních počítačů se automaticky vygeneruje název síťového rozhraní. V těchto pokynech pro jednoduchost se budeme odkazovat na automaticky generované síťová rozhraní, které jsou připojeny k podsíti virtuální sítě Azure **klienta, jako hanadb1-client**, **hanadb2-client**a **hanadb3-client**. 

3. Vytvořte tři síťová rozhraní, jedno pro `storage` každý virtuální počítač, pro podsíť virtuální sítě (v tomto příkladu **hanadb1-storage**, **hanadb2-storage**a **hanadb3-storage).**  

4. Vytvořte tři síťová rozhraní, jedno pro `hana` každý virtuální počítač, pro podsíť virtuální sítě (v tomto příkladu **hanadb1-hana**, **hanadb2-hana**a **hanadb3-hana**).  

5. Připojte nově vytvořená rozhraní virtuální sítě k odpovídajícím virtuálním počítačům následujícím postupem:  

    a. Přejděte na virtuální počítač na [webu Azure Portal](https://portal.azure.com/#home).  

    b. V levém podokně vyberte **Virtuální počítače**. Filtrujte podle názvu virtuálního počítače (například **hanadb1**) a vyberte virtuální počítač.  

    c. V podokně **Přehled** vyberte **Zastavit,** chcete-li virtuální počítač navrátit.  

    d. Vyberte **možnost Síť**a připojte síťové rozhraní. V rozevíracím seznamu **Připojit síťové rozhraní** vyberte již `storage` vytvořená síťová rozhraní pro podsítě a. `hana`  
    
    e. Vyberte **Uložit**. 
 
    f. Opakujte kroky b až e pro zbývající virtuální počítače (v našem příkladu **hanadb2** a **hanadb3**).
 
    g. Virtuální počítače prozatím ponechejte ve stavu zastavené. Dále umožníme [zrychlené vytváření sítí](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) pro všechna nově připojená síťová rozhraní.  

6. Povolte zrychlené sítě pro další `storage` `hana` síťová rozhraní pro podsítě a provedením následujících kroků:  

    a. Otevřete [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) na webu Azure [Portal](https://portal.azure.com/#home).  

    b. Spusťte následující příkazy, abyste povolili zrychlené sítě pro `storage` další `hana` síťová rozhraní, která jsou připojena k podsítím a.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Spusťte virtuální počítače následujícím postupem:  

    a. V levém podokně vyberte **Virtuální počítače**. Filtrujte podle názvu virtuálního počítače (například **hanadb1**) a vyberte ho.  

    b. V podokně **Přehled** vyberte **Spustit**.  

## <a name="operating-system-configuration-and-preparation"></a>Konfigurace a příprava operačního systému

Pokyny v následujících částech jsou předponou jedním z následujících:
* **[A]**: Platí pro všechny uzly
* **[1]**: Platí pouze pro uzel 1
* **[2]**: Platí pouze pro uzel 2
* **[3]**: Platí pouze pro uzel 3

Nakonfigurujte a připravte operační tým následujícím postupem:

1. **[A]** Udržovat hostitelské soubory na virtuálních počítačích. Zahrnout položky pro všechny podsítě. Pro tento příklad `/etc/hosts` byly přidány následující položky.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Změňte nastavení konfigurace DHCP a cloudu pro síťové rozhraní pro úložiště, abyste zabránili nechtěným změnám názvu hostitele.  

    Následující pokyny předpokládají, že síťové `eth1`rozhraní úložiště je . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** Přidejte síťovou trasu tak, aby komunikace se soubory Azure NetApp probíhá přes síťové rozhraní úložiště.  

    Následující pokyny předpokládají, že síťové `eth1`rozhraní úložiště je .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Restartujte virtuální počítač a aktivujte změny.  

3. **[A]** Připravte operační systém pro spuštění SAP HANA na NetApp Systems s NFS, jak je popsáno v [SAP HANA na NetApp AFF Systems s nfs konfiguraci průvodce](https://www.netapp.com/us/media/tr-4435.pdf). Vytvořte konfigurační soubor */etc/sysctl.d/netapp-hana.conf* pro nastavení konfigurace NetApp.  

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

4. **[A]** Vytvořte konfigurační soubor */etc/sysctl.d/ms-az.conf* s nastavením konfigurace Microsoftu pro Azure.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** Upravte nastavení sunrpc, jak je doporučeno v [SAP HANA na NetApp AFF Systems s nfs konfigurační průvodce](https://www.netapp.com/us/media/tr-4435.pdf).  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Připojení svazků souborů Azure NetApp

1. **[A]** Vytvořte přípojné body pro svazky databáze HANA.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Vytvořte adresáře specifické pro uzel pro /usr/sap na **SDÍLENÉ HN1.**  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Ověřte nastavení domény systému nfs. Ujistěte se, že doména je nakonfigurovaná jako **`defaultv4iddomain.com`** výchozí doména Azure NetApp Files, **tj.**  

    > [!IMPORTANT]
    > Ujistěte se, že jste `/etc/idmapd.conf` nastavili doménu systému souborů NFS ve **`defaultv4iddomain.com`** virtuálním počítači tak, aby odpovídala výchozí konfiguraci domény v souborech Azure NetApp: . Pokud existuje neshoda mezi konfigurací domény v klientovi nfs (tj. v rámci virtuálního počítače) a serverem nfs, `nobody`tj.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Ověřit . Měl by být nastaven na **Y**. Chcete-li vytvořit `nfs4_disable_idmapping` adresářovou strukturu, kde je umístěn, spusťte příkaz připojení. Nebudete moci ručně vytvořit adresář pod /sys/modules, protože přístup je vyhrazen pro jádro / ovladače.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** Vytvořte skupinu SAP HANA a uživatele ručně. ID skupinových sapsys a adm uživatele **HN1**musí být nastavena na stejné ID, které jsou k dispozici při zařazování. (V tomto příkladu jsou ID nastavena na **1001**.) Pokud id nejsou správně nastaveny, nebudete mít přístup ke svazkům. ID skupinových sapsys a uživatelských účtů **HN1**adm a sapadm musí být stejné na všech virtuálních počítačích.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** Připojení sdílených svazků souborů Azure NetApp.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Připojte svazky specifické pro uzel na **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Připojte svazky specifické pro uzel na **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Připojte svazky specifické pro uzel na **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Ověřte, zda jsou všechny svazky HANA připojeny pomocí protokolu **NFS verze NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Instalace  

V tomto příkladu pro nasazení SAP HANA v konfiguraci horizontálního navýšení kapacity s pohotovostním uzlem s Azure jsme použili HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Příprava na instalaci HANA

1. **[A]** Před instalací HANA nastavte kořenové heslo. Po dokončení instalace můžete root heslo zakázat. Provést `root` jako `passwd`příkaz .  

2. **[1]** Ověřte, zda se můžete přihlásit přes SSH na **hanadb2** a **hanadb3**, aniž byste byli vyzváni k zadání hesla.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Nainstalujte další balíčky, které jsou vyžadovány pro aktualizaci HANA 2.0 SP4. Další informace naleznete v poznámce SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Změňte vlastnictví SAP `data` `log` HANA a adresářů na **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalace HANA

1. **[1]** Nainstalujte SAP HANA podle pokynů v [průvodci instalací a aktualizací SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). V tomto příkladu nainstalujeme horizontální navýšení kapacity SAP HANA s hlavním serverem, jedním pracovníkem a jedním pohotovostním uzlem.  

   a. Spusťte program **hdblcm** z instalačního softwarového adresáře HANA. Použijte `internal_network` parametr a předavěte adresní prostor pro podsíť, který se používá pro interní komunikaci mezi uzlami HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. Na výzvu zadejte následující hodnoty:

     * Pro **volbu Zvolte akci**: zadejte **1** (pro instalaci)
     * Pro **další komponenty pro instalaci**: zadejte **2, 3**
     * Pro instalační cestu: stiskněte Enter (výchozí hodnota /hana/shared)
     * Místní **název hostitele**: stisknutím klávesy Enter přijměte výchozí
     * V části **Chcete do systému přidat hostitele?** **y**
     * Pro **názvy hostitelů oddělené čárkami, které chcete přidat:** zadejte **hanadb2, hanadb3**
     * Pro **kořenové uživatelské jméno** [root]: stisknutím klávesy Enter přijměte výchozí
     * Pro **root uživatelské heslo**: zadejte heslo uživatele kořenového uživatele
     * Pro role pro hostitele hanadb2: zadejte **1** (pro pracovníka)
     * V **části Skupina s podporou převzetí služeb při selhání** hostitele pro hostitele hanadb2 [default]: stisknutím klávesy Enter přijměte výchozí
     * Číslo **oddílu úložiště** pro hostitele hanadb2 [<<assign automatically>>]: stisknutím klávesy Enter přijměte výchozí
     * Pro **skupinu pracovních pracovníků** pro hostitele hanadb2 [default]: stisknutím klávesy Enter přijměte výchozí
     * Do **příkazu Vybrat role** pro hostitele hanadb3: zadejte **2** (pro pohotovostní režim).
     * V **části Skupina s podporou převzetí služeb při selhání** hostitele pro hostitele hanadb3 [default]: stisknutím klávesy Enter přijměte výchozí
     * Pro **skupinu pracovních pracovníků** pro hostitele hanadb3 [default]: stisknutím klávesy Enter přijměte výchozí
     * Pro **SAP HANA ID systému**: zadejte **HN1**
     * Pro **číslo instance** [00]: zadejte **03**
     * V **části Místní hostitelská pracovní skupina** [default]: stisknutím klávesy Enter přijměte výchozí
     * Do **pole Select System Usage / Enter index [4]**: zadejte **4** (pro vlastní)
     * Pro **umístění datových svazků** [/hana/data/HN1]: stisknutím klávesy Enter přijměte výchozí
     * Pro **umístění svazků protokolu** [/hana/log/HN1]: stisknutím klávesy Enter přijměte výchozí
     * Omezit **maximální přidělení paměti?** [n]: zadejte **n**
     * Název **hostitele certifikátu pro hostitele hanadb1** [hanadb1]: Stisknutím klávesy Enter přijměte výchozí nastavení.
     * Název **hostitele certifikátu pro hostitele hanadb2** [hanadb2]: Stisknutím klávesy Enter přijměte výchozí nastavení.
     * Název **hostitele certifikátu pro hostitele hanadb3** [hanadb3]: Stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **správce systému (hn1adm) Heslo**: zadejte heslo
     * Pro **uživatele systémové databáze (systém) Heslo**: zadejte heslo systému
     * Pro **potvrzení systémové databáze Uživatele (systém) Heslo:** zadejte heslo systému
     * Pro **restart systému po restartu počítače?** [n]: zadejte **n** 
     * **Pro Chcete pokračovat (a/n)**: ověřit souhrn a pokud vše vypadá dobře, zadejte **y**


2. **[1]** Ověření global.ini  

   Zobrazte global.ini a ujistěte se, že konfigurace pro interní komunikaci mezi mezi uznami SAP HANA je na místě. Ověřte **část komunikace.** Měl by mít adresní `hana` prostor pro `listeninterface` podsíť `.internal`a měl by být nastaven na . Ověřte oddíl **internal_hostname_resolution.** Měl by mít IP adresy pro virtuální počítače HANA, které patří do `hana` podsítě.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Přidejte mapování hostitelů, abyste zajistili, že ip adresy klienta budou použity pro komunikaci s klientem. Přidejte `public_host_resolution`oddíl a přidejte odpovídající adresy IP z podsítě klienta.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Chcete-li aktivovat změny, restartujte SAP HANA.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Ověřte, zda klientské rozhraní bude `client` pro komunikaci používat adresy IP z podsítě.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Informace o ověření konfigurace naleznete v tématu SAP Note [2183363 - Konfigurace interní sítě SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Chcete-li optimalizovat SAP HANA pro základní úložiště souborů Azure NetApp, nastavte následující parametry SAP HANA:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**všechny**

   Další informace naleznete [v tématu SAP HANA on NetApp AFF Systems with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

   Počínaje systémy SAP HANA 2.0 můžete nastavit `global.ini`parametry v . Další informace naleznete v poznámce SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Pro systémy SAP HANA 1.0 verze SPS12 a starší, tyto parametry lze nastavit během instalace, jak je popsáno v SAP Poznámka [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Úložiště, které používají soubory Azure NetApp, má omezení velikosti souboru 16 terabajtů (TB). SAP HANA není implicitně vědomi omezení úložiště a nebude automaticky vytvořit nový datový soubor při dosažení limitu velikosti souboru 16 TB. Jako SAP HANA pokusí zvětšit soubor nad 16 TB, tento pokus bude mít za následek chyby a nakonec v selhání indexového serveru. 

   > [!IMPORTANT]
   > Chcete-li zabránit sap HANA ve snaze zvětšit datové soubory nad [limit 16](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) `global.ini`TB podsystému úložiště, nastavte následující parametry v .  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 Další informace naleznete v poznámce SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Uvědomte si POZNÁMKU SAP [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Otestovat převzetí služeb při selhání SAP HANA 

1. Simulujte selhání uzlu v pracovním uzlu SAP HANA. Udělejte toto: 

   a. Před simulací selhání uzlu spusťte následující příkazy jako **adm hn1,** abyste zachytili stav prostředí:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   b. Chcete-li simulovat selhání uzlu, spusťte následující příkaz jako root v uzlu pracovního procesu, což je **hanadb2** v tomto případě:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Sledujte, že systém bude dokončen s podporou převzetí služeb při selhání. Po dokončení převzetí služeb při selhání zachyťte stav, který by měl vypadat takto:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Když uzel dojde k panice jádra, vyhněte `kernel.panic` se zpoždění s převzetí služeb při selhání SAP HANA nastavením na 20 sekund na *všech* virtuálních počítačích HANA. Konfigurace se provádí `/etc/sysctl`v . Restartujte virtuální počítače a aktivujte změnu. Pokud tato změna není provedena, převzetí služeb při selhání může trvat 10 nebo více minut, když uzel dochází k panice jádra.  

2. Zabít název serveru tím, že provádí následující kroky:

   a. Před testem zkontrolujte stav prostředí spuštěním následujících příkazů jako **hn1**adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Spusťte následující příkazy jako **hn1**adm na aktivním hlavním uzlu, což je **hanadb1** v tomto případě:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Pohotovostní uzel **hanadb3** převezme jako hlavní uzel. Zde je stav prostředku po dokončení testu převzetí služeb při selhání:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   c. Restartujte instanci HANA na **hanadb1** (to znamená na stejném virtuálním počítači, kde byl zabit název serveru). Uzel **hanadb1** se znovu připojí k prostředí a zachová jeho pohotovostní roli.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po spuštění SAP HANA na **hanadb1**očekávejte následující stav:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Znovu, zabít název serveru na aktuálně aktivní hlavní uzel (to znamená, že na uzlu **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Uzel **hanadb1** bude pokračovat v roli hlavního uzlu. Po dokončení testu převzetí služeb při selhání bude stav vypadat takto:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   e. Spusťte SAP HANA na **hanadb3**, který bude připraven sloužit jako pohotovostní uzel.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po spuštění SAP HANA na **hanadb3**vypadá stav takto:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete [v tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure ....][sap-hana-ha]
