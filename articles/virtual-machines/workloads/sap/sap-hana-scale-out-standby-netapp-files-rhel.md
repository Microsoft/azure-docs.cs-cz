---
title: Sap HANA horizontální navýšení kapacity s pohotovostním režimem se soubory Azure NetApp na RHEL| Dokumenty společnosti Microsoft
description: Průvodce vysokou dostupností pro SAP NetWeaver na Red Hat Enterprise Linux u souborů Azure NetApp pro aplikace SAP
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
ms.openlocfilehash: ace30b044682cb4e62a0f81da90f21a77be1edd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372972"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na Red Hat Enterprise Linux 

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
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Tento článek popisuje, jak nasadit vysoce dostupný systém SAP HANA v horizontálním navýšení kapacity konfigurace s pohotovostním režimem na virtuálních počítačích Azure Red Hat Enterprise Linux (VM), pomocí [Souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) pro sdílené svazky úložiště.  

V ukázkových konfiguracích, příkazech instalace a tak dále je instance HANA **03** a ID systému HANA je **HN1**. Příklady jsou založeny na HANA 2.0 SP4 a Red Hat Enterprise Linux pro SAP 7.6. 

Než začnete, podívejte se na následující poznámky a dokumenty SAP:

* [Dokumentace ke službě Azure NetApp Files][anf-azure-doc] 
* SAP Poznámka [1928533] obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* POZNÁMKA SAP [2015553]: Uvádí předpoklady pro nasazení softwaru SAP podporovaný SAP v Azure.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632]: Obsahuje podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure
* SAP Note [2191498]: Obsahuje požadovanou verzi SAP Host Agent pro Linux v Azure
* SAP Note [2243692]: Obsahuje informace o licencování SAP na Linuxu v Azure
* SAP Note [1999351:]Obsahuje další informace o řešení potíží pro rozšíření rozšířeného monitorování Azure pro SAP
* SAP Note [1900823]: Obsahuje informace o požadavcích na úložiště SAP HANA
* [SAP Community Wiki:](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)Obsahuje všechny požadované POZNÁMKY SAP pro Linux
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Průvodce sítěmi Red Hat Enterprise Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Dokumentace RHEL specifické pro Azure:
  * [Instalace SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure](https://access.redhat.com/solutions/3193782)
* [Aplikace NetApp SAP v Microsoft Azure pomocí souborů Azure NetApp][anf-sap-applications-azure]


## <a name="overview"></a>Přehled

Jednou z metod pro dosažení vysoké dostupnosti HANA je konfigurace automatického převzetí služeb při selhání hostitele. Chcete-li nakonfigurovat automatické převzetí služeb při selhání hostitele, přidejte jeden nebo více virtuálních počítačů do systému HANA a nakonfigurujte je jako pohotovostní uzly. Pokud se aktivní uzel nezdaří, automaticky převezme pohotovostní uzel. V prezentované konfiguraci s virtuálními počítači Azure dosáhnete automatického převzetí služeb při selhání pomocí [systému souborů NFS v souborech Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

> [!NOTE]
> Pohotovostní uzel potřebuje přístup ke všem svazkům databáze. Svazky HANA musí být připojeny jako svazky NFSv4. Vylepšený blokovací mechanismus založený na zapůjčení souborů v `I/O` protokolu NFSv4 se používá pro oplocení. 

> [!IMPORTANT]
> Chcete-li vytvořit podporovanou konfiguraci, musíte nasadit svazky dat HANA a protokolovat jako svazky nfsv4.1 a připojit je pomocí protokolu NFSv4.1. Konfigurace automatického převzetí služeb při selhání hostitele HANA s pohotovostním uzlem není podporována systémem NFSv3.

![Přehled vysoké dostupnosti sap netweaveru](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

V předchozím diagramu, který následuje podle doporučení sítě SAP HANA, jsou v rámci jedné virtuální sítě Azure reprezentovány tři podsítě: 
* Pro komunikaci s klienty
* Pro komunikaci s úložným systémem
* Pro interní komunikaci mezi uzlovacími předpisy HANA

Svazky Azure NetApp jsou v samostatné podsíti [delegované na soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

V tomto příkladu konfigurace jsou podsítě:  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 (delegovaná podsíť na soubory Azure NetApp)

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

   Při nasazování svazků nezapomeňte vybrat verzi **NFSv4.1.** Nasazení svazků v určené [podsíti](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Soubory Azure NetApp . 
   
   Nezapomeňte, že prostředky Azure NetApp Files a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerem. Například **HN1**-data-mnt00001, **HN1**-log-mnt00001 a tak dále, jsou názvy svazků a nfs://10.9.0.4/**HN1**-data-mnt00001, nfs://10.9.0.4/**HN1**-log-mnt00001 a tak dále, jsou cesty k souborům pro svazky souborů Azure NetApp.  

   * svazek **HN1**-data-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * svazek **HN1**-data-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * svazek **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * svazek **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * svazek **HN1**-shared (nfs://10.9.0.4/**HN1**-shared)
   
   V tomto příkladu jsme použili samostatný svazek souborů Azure NetApp pro každý svazek HANA a protokolu. Pro nákladově optimalizovanější konfiguraci v menších nebo neproduktivních systémech je možné umístit všechna připojení dat na jeden svazek a všechny protokoly na jiný jediný svazek.  

### <a name="important-considerations"></a>Důležité informace

Při vytváření souborů Azure NetApp pro SAP HANA horizontální navýšení kapacity s scénářem stand by nodes si uvědomte následující důležité aspekty:

- Bazén s minimální kapacitou je 4 tebibytes (TiB).  
- Minimální velikost svazku je 100 gibibytes (GiB).
- Soubory Azure NetApp a všechny virtuální počítače, kde se připojí svazky souborů Azure NetApp, musí být ve stejné virtuální síti Azure nebo ve [virtuálních sítích s partnerským vztahem](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ve stejné oblasti.  
- Vybraná virtuální síť musí mít podsíť, která je delegovaná na soubory Azure NetApp.
- Propustnost svazku souborů Azure NetApp je funkce kvóty svazku a úrovně služeb, jak je popsáno na [úrovni služby pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Při dimenzování svazků HANA Azure NetApp se ujistěte, že výsledná propustnost splňuje požadavky systému HANA.  
- Pomocí [zásad exportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)souborů Azure NetApp můžete řídit povolené klienty, typ přístupu (jen pro čtení a zápis, jen pro čtení a tak dále). 
- Funkce Soubory Azure NetApp ještě není zónová. V současné době se tato funkce nenasadí ve všech zónách dostupnosti v oblasti Azure. Uvědomte si potenciální důsledky latence v některých oblastech Azure.  

> [!IMPORTANT]
> Pro úlohy SAP HANA je důležitá nízká latence. Spolupracujte se zástupcem Microsoftu a ujistěte se, že virtuální počítače a svazky Souborů Azure NetApp jsou nasazeny v těsné blízkosti.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Změna velikosti databáze HANA v souborech Azure NetApp

Propustnost svazku souborů Azure NetApp je funkce velikosti svazku a úrovně služby, jak je popsáno na [úrovni služby pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Při navrhování infrastruktury pro SAP v Azure si uvědomte některé minimální požadavky na úložiště od SAP, které se promítají do vlastností minimální propustnost:

- Čtení a zápis na /hana/log 250 megabajtů za sekundu (MB/s) s velikostmi vstupně-va 1 MB.  
- Čtení aktivity alespoň 400 MB/s pro /hana/data pro velikosti 16 MB a 64 MB vstupně-va.  
- Aktivita zápisu alespoň 250 MB/s pro /hana/data s velikostmi 16 MB a 64 MB vstupně-v/v. 

[Limity propustnost souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TiB kvóty svazku jsou:
- Úroveň úložiště Premium – 64 MiB/s  
- Úroveň ultra úložiště - 128 MiB/s  

Chcete-li splnit požadavky sap minimální propustnost pro data a protokol a pokyny pro /hana/shared, doporučené velikosti by:

| Svazek | Velikost<br>Úroveň úložiště Premium | Velikost<br>Úroveň úložiště Ultra | Podporovaný protokol nfs |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 1xRAM na 4 pracovní uzly | 1xRAM na 4 pracovní uzly | v3 nebo v4.1 |

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

   a. Použijte image Red Hat Enterprise Linux v galerii Azure, která je podporovaná pro SAP HANA. V tomto příkladu jsme použili obrázek RHEL-SAP-HA 7.6.  

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

3. **[A]** Přidejte síťovou trasu tak, aby komunikace se soubory Azure NetApp probíhá přes síťové rozhraní úložiště.  

   V tomto příkladu slouží `Networkmanager` ke konfiguraci další síťové trasy. Následující pokyny předpokládají, že síťové `eth1`rozhraní úložiště je .  
   Nejprve určete název `eth1`připojení zařízení . V tomto příkladu je `eth1` `Wired connection 1`název připojení pro zařízení .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Pak nakonfigurujte další trasu k `eth1`delegované síti Azure NetApp Files přes .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Restartujte virtuální počítač a aktivujte změny.  

3. **[A]** Nainstalujte balíček klienta systému NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Připravte operační systém pro spuštění SAP HANA v Azure NetApp s nfs, jak je popsáno v [SAP HANA na NetApp AFF Systems s průvodcem konfigurací systému NFS](https://www.netapp.com/us/media/tr-4435.pdf). Vytvořte konfigurační soubor */etc/sysctl.d/netapp-hana.conf* pro nastavení konfigurace NetApp.  

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
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Vytvořte konfigurační soubor */etc/sysctl.d/ms-az.conf* s dalšími nastaveními optimalizace.  

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

5. **[A]** Upravte nastavení sunrpc, jak je doporučeno v [SAP HANA na NetApp AFF Systems s nfs konfigurační průvodce](https://www.netapp.com/us/media/tr-4435.pdf).  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat pro konfiguraci HANA.

    Nakonfigurujte RHEL, jak je popsáno v poznámce SAP [2292690], <https://access.redhat.com/solutions/2447641> [2455582], [2593824] a .

    > [!NOTE]
    > Pokud instalujete HANA 2.0 SP04, budete muset nainstalovat balíček, `compat-sap-c++-7` jak je popsáno v poznámce SAP [2593824], před instalací SAP HANA. 

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
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Další podrobnosti o `nfs4_disable_idmapping` tom, https://access.redhat.com/solutions/1749883jak změnit parametr, naleznete v tématu .

6. **[A]** Připojení sdílených svazků souborů Azure NetApp.  

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

7. **[1]** Připojte svazky specifické pro uzel na **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Připojte svazky specifické pro uzel na **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Připojte svazky specifické pro uzel na **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Ověřte, zda jsou všechny svazky HANA připojeny pomocí protokolu **NFS verze NFSv4**.  

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
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Změňte vlastnictví SAP `data` `log` HANA a adresářů na **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Dočasně zakažte bránu firewall, aby nezasahovala do instalace HANA. Po dokončení instalace HANA jej můžete znovu povolit. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Instalace HANA

1. **[1]** Nainstalujte SAP HANA podle pokynů v [průvodci instalací a aktualizací SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). V tomto příkladu nainstalujeme horizontální navýšení kapacity SAP HANA s hlavním serverem, jedním pracovníkem a jedním pohotovostním uzlem.  

   a. Spusťte program **hdblcm** z instalačního softwarového adresáře HANA. Použijte `internal_network` parametr a předavěte adresní prostor pro podsíť, který se používá pro interní komunikaci mezi uzlami HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Na výzvu zadejte následující hodnoty:

     * Pro **volbu Zvolte akci**: zadejte **1** (pro instalaci)
     * Pro **další komponenty pro instalaci**: zadejte **2, 3**
     * Pro instalační cestu: stiskněte Enter (výchozí hodnota /hana/shared)
     * Místní **název hostitele**: stisknutím klávesy Enter přijměte výchozí
     * V části **Chcete do systému přidat hostitele?** **y**
     * Pro **názvy hostitelů oddělené čárkami, které chcete přidat:** zadejte **hanadb2, hanadb3**
     * Pro **kořenové uživatelské jméno** [root]: stisknutím klávesy Enter přijměte výchozí
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
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Přidejte mapování hostitelů, abyste zajistili, že ip adresy klienta budou použity pro komunikaci s klientem. Přidejte `public_host_resolution`oddíl a přidejte odpovídající adresy IP z podsítě klienta.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Chcete-li aktivovat změny, restartujte SAP HANA.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Ověřte, zda klientské rozhraní bude `client` pro komunikaci používat adresy IP z podsítě.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Informace o ověření konfigurace naleznete v tématu SAP Note [2183363 - Konfigurace interní sítě SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** Znovu povolte bránu firewall.  
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

   - Otevření potřebných portů brány firewall

       > [!IMPORTANT]
       > Vytvořte pravidla brány firewall, která umožní komunikaci mezi uzly HANA a přenosy klientů. Požadované porty jsou uvedeny na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem. V tomto scénáři s použitým číslem systému 03.

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

   - Spustit hana
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

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
   > Když uzel dojde k panice jádra, vyhněte `kernel.panic` se zpoždění s převzetí služeb při selhání SAP HANA nastavením na 20 sekund na *všech* virtuálních počítačích HANA. Konfigurace se provádí `/etc/sysctl`v . Restartujte virtuální počítače a aktivujte změnu. Pokud tato změna není provedena, převzetí služeb při selhání může trvat 10 nebo více minut, když uzel dochází k panice jádra.  

2. Zabít název serveru tím, že provádí následující kroky:

   a. Před testem zkontrolujte stav prostředí spuštěním následujících příkazů jako **hn1**adm:  

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

   b. Spusťte následující příkazy jako **hn1**adm na aktivním hlavním uzlu, což je **hanadb1** v tomto případě:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Pohotovostní uzel **hanadb3** převezme jako hlavní uzel. Zde je stav prostředku po dokončení testu převzetí služeb při selhání:  

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

   c. Restartujte instanci HANA na **hanadb1** (to znamená na stejném virtuálním počítači, kde byl zabit název serveru). Uzel **hanadb1** se znovu připojí k prostředí a zachová jeho pohotovostní roli.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po spuštění SAP HANA na **hanadb1**očekávejte následující stav:  

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

   d. Znovu, zabít název serveru na aktuálně aktivní hlavní uzel (to znamená, že na uzlu **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Uzel **hanadb1** bude pokračovat v roli hlavního uzlu. Po dokončení testu převzetí služeb při selhání bude stav vypadat takto:

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

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete [v tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure ....][sap-hana-ha]
