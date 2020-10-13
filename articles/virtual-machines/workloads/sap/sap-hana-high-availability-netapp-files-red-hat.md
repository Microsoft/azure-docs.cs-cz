---
title: Vysoká dostupnost SAP HANA škálování pomocí ANF na RHEL | Microsoft Docs
description: Zajištění vysoké dostupnosti SAP HANA s ANF na virtuálních počítačích Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2020
ms.author: radeltch
ms.openlocfilehash: ce24bf541c5a71c50bb34f5e42aa3452f01b871c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978165"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Vysoká dostupnost SAP HANA škálování s využitím Azure NetApp Files na Red Hat Enterprise Linux

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

Tento článek popisuje, jak nakonfigurovat replikaci SAP HANA systému v nasazení se škálováním na více systémů, když jsou systémy souborů HANA připojené přes systém souborů NFS, pomocí Azure NetApp Files (ANF). V ukázkových příkazech konfigurace a instalace, číslo instance **03**a ID systému Hana **HN1** se používají. Replikace SAP HANA se skládá z jednoho primárního uzlu a alespoň jednoho sekundárního uzlu.

V případě, že kroky v tomto dokumentu jsou označeny následujícími předponami, význam je následující:

- **[A]**: Tento krok platí pro všechny uzly.
- **[1]**: Tento krok platí jenom pro Uzel1.
- **[2]**: Tento krok platí jenom pro Uzel2.
 
Nejprve si přečtěte následující poznámky a dokumenty SAP:

- Poznámka [1928533](https://launchpad.support.sap.com/#/notes/1928533)pro SAP obsahuje:
    - Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
    - Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
    - Podporovaný software SAP a kombinace operačního systému (OS) a databáze.
    - Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
- SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553) uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) uvádí Doporučený systém souborů pro prostředí Hana.
- Systém SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) doporučuje nastavení operačního systému pro Red Hat Enterprise Linux.
- Poznámka SAP Poznámka [2009879](https://launchpad.support.sap.com/#/notes/2009879) obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux.
- Pro SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632) najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
- V případě SAP Poznámka [2191498](https://launchpad.support.sap.com/#/notes/2191498) je požadovaná verze agenta hostitele SAP pro Linux v Azure.
- Poznámka SAP Poznámka [2243692](https://launchpad.support.sap.com/#/notes/2243692) obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
- V části SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351) najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
- [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
- [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
- [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
- [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
- [Replikace SAP HANA systému v clusteru Pacemaker.](https://access.redhat.com/articles/3004101)
- Obecná dokumentace k RHEL
    - [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Správa Add-On vysoké dostupnosti.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referenční informace Add-On vysoké dostupnosti.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Konfigurace replikace systému SAP HANA v Scale-Up v clusteru Pacemaker v případě, že jsou systémy souborů HANA ve sdílených složkách NFS](https://access.redhat.com/solutions/5156571)
- Dokumentace k RHEL specifické pro Azure:
    - [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru.](https://access.redhat.com/articles/3131341)
    - [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Nainstalujte SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Konfigurace SAP HANA Pacemaker replikace systému do clusteru s možností horizontálního navýšení kapacity, když jsou systémy souborů HANA ve sdílených složkách NFS](https://access.redhat.com/solutions/5156571)
- [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>Overview

V prostředí s možností horizontálního rozšíření kapacity jsou všechny systémy souborů pro SAP HANA připojené z místního úložiště. Nastavení vysoké dostupnosti SAP HANA replikace systému při Red Hat Enterprise Linux je publikované v příručce [nastavení SAP HANA systémové replikace v RHEL](./sap-hana-high-availability-rhel.md)

Abychom dosáhli SAP HANA vysoké dostupnosti systému pro horizontální navýšení kapacity v [Azure NetApp Files](../../../azure-netapp-files/index.yml) sdílené složky NFS, potřebujeme v clusteru další konfiguraci prostředků, aby se prostředky Hana obnovily, když jeden uzel ztratí přístup ke sdíleným složkám NFS na ANF.  Cluster spravuje připojení NFS, což umožňuje monitorovat stav prostředků. Jsou vynutily závislosti mezi připojeními systému souborů a SAP HANAmi prostředky.  

![Horizontální navýšení kapacity SAP HANA vysoké dostupnosti v ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA systémy souborů NFS jsou připojené ke sdíleným složkám NFS pomocí Azure NetApp Files na všech uzlech. Souborové systémy/Hana/data,/Hana/log a/Hana/Shared jsou pro každý uzel jedinečné. 

Připojeno na Uzel1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 on/Hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 on/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 on/Hana/Shared

Připojeno na Uzel2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 on/Hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 on/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 on/Hana/Shared

> [!NOTE]
> Souborové systémy/Hana/Shared,/Hana/data a/Hana/log se mezi těmito dvěma uzly nesdílí. Každý uzel clusteru má vlastní samostatné systémy souborů.   

Konfigurace replikace systému SAP HANA používá vyhrazeného virtuálního hostitele a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Následující seznam uvádí konfiguraci nástroje pro vyrovnávání zatížení:

- Konfigurace front-endu: IP adresa 10.32.0.10 pro HN1-DB
- Konfigurace back-endu: připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí replikace systému HANA
- Port testu paměti: port 62503
- Pravidla vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (Pokud se používá základní nástroj pro vyrovnávání zatížení Azure)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Nastavení infrastruktury souborů Azure NetApp

Než budete pokračovat se sestavou pro Azure NetApp Files infrastrukturu, Seznamte se s [dokumentaci k souborům Azure NetApp](../../../azure-netapp-files/index.yml).

Azure NetApp Files je k dispozici v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Podívejte se, jestli vybraná oblast Azure nabízí Azure NetApp Files.

Informace o dostupnosti Azure NetApp Files podle oblasti Azure najdete v tématu [Azure NetApp Files dostupnosti podle oblasti Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Než začnete s nasazením Azure NetApp Files, požádejte o [registraci pro Azure NetApp Files instrukcí pro](../../../azure-netapp-files/azure-netapp-files-register.md)Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure NetApp Files

V následujících pokynech se předpokládá, že jste už nasadili službu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Azure NetApp Files prostředky a virtuální počítače, kde budou připojené prostředky Azure NetApp Files, musí být nasazené ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem.

1. Pokud jste ještě neimplementovali prostředky, požádejte o [registraci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Podle pokynů v části [Vytvoření účtu NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)vytvořte ve vybrané oblasti Azure účet NetApp.

3.  Nastavte fond kapacit Azure NetApp Files podle pokynů v části [nastavení fondu kapacity Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

    Architektura HANA uvedená v tomto článku používá jeden Azure NetApp Files fond kapacit na úrovni služeb úrovně *Ultra* . Pro úlohy HANA v Azure doporučujeme použít Azure NetApp Files [úroveň služeb](../../../azure-netapp-files/azure-netapp-files-service-levels.md)pro *Ultra* nebo *Premium* .

4.  Delegování podsítě na Azure NetApp Files, jak je popsáno v pokynech [delegování podsítě na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Nasaďte Azure NetApp Files svazky podle pokynů v tématu [vytvoření svazku NFS pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    Při nasazování svazků nezapomeňte vybrat verzi NFSv 4.1. Nasaďte svazky v určené Azure NetApp Files podsíti. IP adresy svazků Azure NetApp se přiřazují automaticky.

    Pamatujte, že prostředky Azure NetApp Files a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem. Například hanadb1-data-mnt00001, hanadb1-log-mnt00001 atd. jsou názvy svazků a nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 a tak dále, jsou cesty k souborům pro Azure NetApp Files svazky.
    
    V **hanadb1**

    - Volume hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    V **hanadb2**

    - Volume hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Důležité informace

Vzhledem k tomu, že vytváříte Azure NetApp Files pro SAP HANA systémy škálované velikosti, mějte na paměti následující skutečnosti:

- Minimální fond kapacit je 4 tebibytes (TiB).
- Minimální velikost svazku je 100 gibibajtech (GiB).
- Azure NetApp Files a všechny virtuální počítače, na kterých budou připojené Azure NetApp Files svazky, musí být ve stejné virtuální síti Azure nebo v [partnerských virtuálních sítích](../../../virtual-network/virtual-network-peering-overview.md) ve stejné oblasti.
- Vybraná virtuální síť musí mít podsíť, která je delegována na Azure NetApp Files.
- Propustnost Azure NetApp Filesho svazku je funkcí kvóty svazku a úrovně služeb, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Když nakonfigurujete svazky NetApp HANA Azure, ujistěte se, že výsledná propustnost splňuje požadavky na systém HANA.
- Pomocí Azure NetApp Files [zásady exportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)můžete řídit povolené klienty, typ přístupu (jen pro čtení i zápis, jen pro čtení atd.).
- Funkce Azure NetApp Files ještě nezohledňuje zóny. V současné době není tato funkce nasazena ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.

> [!IMPORTANT]
> U SAP HANA úloh je nízká latence kritická. Spolupracujte s vaším zástupcem Microsoftu, abyste zajistili, že virtuální počítače a Azure NetApp Files svazky budou nasazeny v blízkosti.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Změna velikosti databáze HANA v Azure NetApp Files

Propustnost Azure NetApp Filesho svazku je funkce velikosti svazku a úrovně služby, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

Při návrhu infrastruktury pro SAP v Azure si pamatujte na některé minimální požadavky na úložiště SAP, které se přeloží na minimální propustnost:

- Čtení a zápis v/Hana/log 250 megabajtů za sekundu (MB/s) s velikostí 1 MB I/O.
- Aktivita čtení minimálně 400 MB/s pro/Hana/data pro velikosti vstupně-výstupních operací 16-MB a 64-MB
- Aktivita zápisu alespoň 250 MB/s pro/Hana/data s velikostí I/O 16 MB a 64-MB.

[Omezení propustnosti Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) na 1 TIB kvót:

- Premium Storage vrstva – 64 MiB/s.
- Úroveň úložiště Ultra Storage – 128 MiB/s.

Aby splňovala požadavky na minimální propustnost SAP pro/Hana/data a/Hana/log, a pokyny pro/Hana/Shared, budou Doporučené velikosti:

|    Svazek    | Velikost Premium Storage úrovně | Velikost úrovně Ultra Storage | Podporovaný protokol NFS |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v 4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v 4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          V3 nebo v 4.1    |


> [!NOTE]
> Níže uvedená doporučení pro nastavení velikosti Azure NetApp Files jsou zaměřená na splnění minimálních požadavků, které SAP doporučuje pro poskytovatele infrastruktury. V reálných scénářích nasazení a úloh nemusí být tyto velikosti dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte je na základě požadavků konkrétního zatížení.

> [!TIP]
> Velikost svazků Azure NetApp Files lze dynamicky měnit bez nutnosti *odpojení* svazků, zastavení virtuálních počítačů nebo zastavení SAP HANA. Tento přístup umožňuje flexibilní splnění očekávané i nepředvídatelné propustnosti aplikace.

> [!NOTE]
> Všechny příkazy pro připojení/Hana/Shared v tomto článku se zobrazují pro/Hana/Shared svazky NFSv 4.1.
> Pokud jste nasadili svazky/Hana/Shared jako svazky NFSv3, nezapomeňte upravit příkazy Mount pro/Hana/Shared pro NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Nasazení virtuálního počítače se systémem Linux prostřednictvím Azure Portal 

Nejprve je třeba vytvořit svazky Azure NetApp Files. Pak proveďte následující kroky:

1.  Vytvořte skupinu prostředků.
2.  Vytvořte virtuální síť.
3.  Vytvořte skupinu dostupnosti. Nastavte maximální doménu aktualizace.
4.  Vytvořte Nástroj pro vyrovnávání zatížení (interní). Doporučujeme standardní nástroj pro vyrovnávání zatížení.
    Vyberte virtuální síť vytvořenou v kroku 2.
5.  Vytvořte virtuální počítač 1 (**hanadb1**). 
6.  Vytvořte virtuální počítač 2 (**hanadb2**).  
7.  Při vytváření virtuálního počítače nepřidáme žádný disk, protože všechny naše přípojné body budou sdílené složky NFS z Azure NetApp Files. 
8.  Pokud používáte standardní nástroj pro vyrovnávání zatížení, postupujte podle těchto kroků konfigurace:
    1.  Nejprve vytvořte front-end fond IP adres:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond**a vyberte **Přidat**.
        1.  Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
        1.  Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.32.0.10**).
        1.  Vyberte **OK**.
        1.  Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.
    1.  Dále vytvořte fond back-end:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu**a vyberte **Přidat**.
        1.  Zadejte název nového fondu back-end (například **Hana-back-end**).
        1.  Vyberte **Přidat virtuální počítač**.
        1.  Vyberte * * virtuální počítač * *.
        1.  Vyberte virtuální počítače SAP HANA clusteru a jejich IP adresy.
        1.  Vyberte **Přidat**.
    1.  Potom vytvořte sondu stavu:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu**a vyberte **Přidat**.
        1.  Zadejte název nové sondy stavu (například **Hana-HP**).
        1.  Jako protokol a port 625**03**vyberte TCP. Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
        1.  Vyberte **OK**.
    1.  Dále vytvořte pravidla vyrovnávání zatížení:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
        1.  Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **Hana-kg**).
        1.  Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**, **Hana-back-endu** a **Hana-HP**).
        1.  Vyberte **porty ha**.
        1.  Zvyšte **časový limit nečinnosti** na 30 minut.
        1.  Ujistěte se, že jste **povolili plovoucí IP adresu**.
        1.  Vyberte **OK**.

> [!NOTE] 
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)

9. Případně, pokud váš scénář používá základní nástroj pro vyrovnávání zatížení, postupujte podle těchto kroků konfigurace:
    1.  Nakonfigurujte Nástroj pro vyrovnávání zatížení. Nejprve vytvořte front-end fond IP adres:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond**a vyberte **Přidat**.
        1.  Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
        1.  Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.32.0.10**).
        1.  Vyberte **OK**.
        1.  Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.
    1.  Dále vytvořte fond back-end:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu**a vyberte **Přidat**.
        1.  Zadejte název nového fondu back-end (například **Hana-back-end**).
        1.  Vyberte **Přidat virtuální počítač**.
        1.  Vyberte skupinu dostupnosti vytvořenou v kroku 3.
        1.  Vyberte virtuální počítače clusteru SAP HANA.
        1.  Vyberte **OK**.
    1.  Potom vytvořte sondu stavu:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu**a vyberte **Přidat**.
        1.  Zadejte název nové sondy stavu (například **Hana-HP**).
        1.  Jako protokol a port 625**03**vyberte **TCP** . Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
        1.  Vyberte **OK**.
    1.  Pro SAP HANA 1,0 vytvořte pravidla vyrovnávání zatížení:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
        1.  Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3**03**15).
        1.  Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**).
        1.  Zachovejte **protokol** nastaven na **TCP**a zadejte port 3**03**15.
        1.  Zvyšte **časový limit nečinnosti** na 30 minut.
        1.  Ujistěte se, že jste **povolili plovoucí IP adresu**.
        1.  Vyberte **OK**.
        1.  Opakujte tento postup pro port 3**03**17.
    1.  Pro SAP HANA 2,0 vytvořte pravidla vyrovnávání zatížení pro systémovou databázi:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
        1.  Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3**03**13).
        1.  Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**).
        1.  Zachovejte **protokol** nastaven na **TCP**a zadejte port 3**03**13.
        1.  Zvyšte **časový limit nečinnosti** na 30 minut.
        1.  Ujistěte se, že jste **povolili plovoucí IP adresu**.
        1.  Vyberte **OK**.
        1.  Opakujte tento postup pro port 3**03**14.
    1.  Pro SAP HANA 2,0 nejprve vytvořte pravidla vyrovnávání zatížení pro databázi tenanta:
        1.  Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
        1.  Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3**03**40).
        1.  Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **Hana-Endu**).
        1.  Zachovejte **protokol** nastaven na **TCP**a zadejte port 3**03**40.
        1.  Zvyšte **časový limit nečinnosti** na 30 minut.
        1.  Ujistěte se, že jste **povolili plovoucí IP adresu**.
        1.  Vyberte **OK**.
        1.  Opakujte tyto kroky pro porty 3**03**41 a 3**03**42.

Další informace o požadovaných portech pro SAP HANA naleznete v kapitole [připojení k databázím tenantů](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) v průvodci [SAP HANA databáze klienta](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) nebo v tématu SAP Note [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md). Viz také SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Připojit Azure NetApp Files svazek

1. **[A]** vytvoření přípojných bodů pro databázové svazky Hana 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** ověřte nastavení domény NFS. Ujistěte se, že je doména nakonfigurovaná jako výchozí doména Azure NetApp Files, tj. **defaultv4iddomain.com** a mapování je nastavené na **nikdo**.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Nezapomeňte nastavit doménu systému souborů NFS v/etc/idmapd.conf na virtuálním počítači tak, aby odpovídala výchozí konfiguraci domény v Azure NetApp Files: **defaultv4iddomain.com**. Pokud dojde k neshodě mezi konfigurací domény v klientovi NFS (tj. virtuálním počítačem) a serverem NFS, tj. konfigurací Azure NetApp, pak se oprávnění k souborům na svazcích Azure NetApp, která jsou připojená k virtuálním počítačům, zobrazí jako nikdo.
    

3. **[1]** připojení svazků specifických pro uzel v Uzel1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** připojení svazků specifických pro uzel v Uzel2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** ověřte, že jsou všechny svazky Hana připojené k protokolu NFS verze názvů NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** ověřte **nfs4_disable_idmapping**. Měl by být nastaven na **Y**. Chcete-li vytvořit adresářovou strukturu, kde je umístěn **nfs4_disable_idmapping** , spusťte příkaz Mount. V/sys/modules nebudete moct ručně vytvořit adresář, protože přístup je vyhrazený pro jádro nebo ovladače.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Další informace o tom, jak změnit parametr **nfs_disable_idmapping** , naleznete v tématu [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Instalace SAP HANA

1. **[A]** nastavte překlad názvů hostitelů pro všechny hostitele.

   Můžete buď použít server DNS, nebo upravit soubor/etc/hosts na všech uzlech. V tomto příkladu se dozvíte, jak použít soubor/etc/hosts. V následujících příkazech nahraďte IP adresu a název hostitele:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL pro konfiguraci Hana

   Konfigurace RHEL, jak je popsáno v níže uvedené části SAP – Poznámka na základě vaší verze RHEL

   - [2292690-SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: spouštění aplikací SAP s použitím RSZ 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: běžící aplikace SAP s aplikací RSZ 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: běžící aplikace SAP s aplikací RSZ 9. x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** instalace SAP HANA

   Začínáme s HANA 2,0 SPS 01, výchozí možnost je MDC. Při instalaci systému HANA bude vytvořen SYSTEMDB a tenant se stejným identifikátorem SID společně. V některých případech nechcete mít výchozího tenanta. V případě, že nechcete vytvářet počátečního tenanta společně s instalací, můžete postupovat podle poznámky SAP [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Spusťte program **hdblcm** z disku DVD Hana. Na příkazovém řádku zadejte následující hodnoty:  
    Vyberte instalaci: zadejte **1** (pro instalaci).  
    Vyberte další komponenty k instalaci: zadejte **1**.  
    Zadejte instalační cestu [/Hana/Shared]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte název místního hostitele [..]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Chcete přidat další hostitele do systému? (a/n) [n]: **n**  
    Zadejte SAP HANA ID systému: zadejte **HN1**.  
    Zadejte číslo instance [00]: zadejte **03**  
    Vyberte režim databáze/zadejte index [1]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Vyberte využití systému/zadejte index [4]: zadejte **4** (pro vlastní).  
    Zadejte umístění datových svazků [/Hana/data]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte umístění svazků protokolu [/Hana/log]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Omezit maximální přidělení paměti? [n]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte název hostitele certifikátu pro hostitele... [...]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte heslo uživatele agenta SAP hostitele (sapadm): zadejte heslo uživatele agenta hostitele.  
    Potvrďte heslo uživatele agenta SAP hostitele (sapadm): Zadejte znovu heslo uživatele agenta hostitele a potvrďte ho.  
    Zadejte heslo správce systému (hn1adm): zadejte heslo správce systému.  
    Potvrzení hesla správce systému (hn1adm): zadejte heslo správce systému znovu pro potvrzení.  
    Zadejte domovský adresář správce systému [/usr/sap/HN1/home]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte prostředí pro přihlášení správce systému [/bin/sh]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte ID uživatele správce systému [1001]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte ID skupiny uživatelů (sapsys) [79]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Zadejte heslo uživatele databáze (systém): zadejte heslo uživatele databáze.  
    Potvrďte heslo uživatele databáze (systém): zadejte heslo uživatele databáze znovu pro potvrzení.  
    Restartovat systém po restartování počítače? [n]: stisknutím klávesy Enter přijměte výchozí nastavení.  
    Chcete pokračovat? (a/n): Ověřte souhrn. Pokračujte zadáním **y** .  

4. **[A]** upgrade agenta hostitele SAP

   Stáhněte si nejnovější archiv agenta hostitele SAP z [centra softwaru SAP](https://launchpad.support.sap.com/#/softwarecenter) a spuštěním následujícího příkazu Upgradujte agenta. Nahraďte cestu k archivu, který bude odkazovat na stažený soubor:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** konfigurace brány firewall

   Vytvořte pravidlo brány firewall pro port testu služby Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Konfigurace replikace systému SAP HANA

Pomocí postupu v části nastavení [replikace systému SAP HANA](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) nakonfigurujte replikaci systému SAP HANA. 

## <a name="cluster-configuration"></a>Konfigurace clusteru

Tato část popisuje nezbytné kroky, které je potřeba ke bezproblémovému fungování clusteru, když SAP HANA nainstalovaná ve sdílených složkách NFS pomocí Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) v Azure a vytvořte pro tento server Hana základní cluster Pacemaker.

### <a name="configure-filesystem-resources"></a>Konfigurace prostředků systému souborů

V tomto příkladu mají každý uzel clusteru vlastní systémy souborů NFS systému HANA/Hana/Shared,/Hana/data a/Hana/log..   

1. **[1]** uveďte cluster do režimu údržby.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** vytvořte prostředky systému souborů pro **hanadb1** připojení.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** vytvořte prostředky systému souborů pro **hanadb2** připojení.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` do operace monitorování se přidá atribut, takže každé monitorování provede v systému souborů test pro čtení i zápis. Bez tohoto atributu operace monitorování ověří, zda je systém souborů připojen. To může být problém, protože když dojde ke ztrátě připojení, systém souborů může zůstat připojený, i když je nepřístupný.

    `on-fail=fence` do operace monitorování je také přidán atribut. V případě, že je tato možnost v uzlu neúspěšná, je tento uzel ihned ohrazen. Bez této možnosti je výchozím chováním zastavení všech prostředků, které jsou závislé na prostředku, který selhal, a pak restartujte prostředek, který selhal, a pak spusťte všechny prostředky, které jsou závislé na neúspěšném prostředku. Toto chování může trvat dlouhou dobu, pokud prostředek SAPHana závisí na neúspěšném prostředku, ale může také zcela selhat. Prostředek SAPHana nemůže být úspěšně zastaven, pokud server NFS, který je držitelem spustitelných souborů HANA, je nepřístupný.

4. **[1]** konfigurace omezení umístění

   Nakonfigurujte omezení umístění, abyste zajistili, že prostředky, které spravují hanadb1 jedinečné připojení, nikdy nejdou spustit na hanadb2 a naopak.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`Možnost je nastavena, protože jedinečné připojení pro každý uzel sdílí stejný přípojný bod. Například `hana_data1` používá přípojný bod `/hana/data` a `hana_data2` také používá přípojný bod `/hana/data` . To může způsobit falešně pozitivní operaci sondy, když se stav prostředku kontroluje při spuštění clusteru a to může způsobit zbytečné chování při obnovení. To je možné vyhnout nastavením `resource-discovery=never`

5. **[1]** konfigurace prostředků atributu

   Nakonfigurujte prostředky atributů. Tyto atributy budou nastaveny na hodnotu true, pokud jsou připojena všechna připojení k systému souborů NFS (/Hana/data,/Hana/log a/Hana/data) uzlu a budou nastavena na hodnotu false jinak.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** konfigurace omezení umístění

   Nakonfigurujte omezení umístění, aby se zajistilo, že prostředek atributu hanadb1's nikdy neběží na hanadb2 a naopak.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** vytváření omezení řazení

   Nakonfigurujte omezení řazení tak, aby se prostředky atributu uzlu spouštěly až po připojení všech připojení NFS uzlu.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Pokud vaše konfigurace obsahuje souborové systémy, mimo skupinu `hanadb1_nfs` nebo `hanadb2_nfs` , pak zahrňte `sequential=false` možnost, takže mezi systémy souborů neexistují žádné závislosti v seřazení. Všechny systémy souborů musí být spuštěny před `hana_nfs1_active` , ale nemusejí být spouštěny v libovolném pořadí, které je vzájemně relativní. Další podrobnosti najdete v tématu [návody konfigurace replikace SAP HANA systému v Scale-Up v clusteru Pacemaker v případě, že jsou systémy souborů Hana ve sdílených složkách NFS](https://access.redhat.com/solutions/5156571) .

### <a name="configure-sap-hana-cluster-resources"></a>Konfigurace prostředků clusteru SAP HANA

1. Pokud chcete vytvořit prostředky SAP HANA v clusteru, postupujte podle kroků v části [vytvoření prostředků clusteru SAP HANA](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) . Po vytvoření SAP HANAch prostředků musíme vytvořit omezení pravidla umístění mezi prostředky SAP HANA a systémy souborů (připojení NFS).

2. **[1]** konfigurace omezení mezi prostředky SAP Hana a PŘIPOJENÍmi NFS

   Omezení pravidla umístění budou nastavena, aby bylo možné spustit prostředky SAP HANA v uzlu pouze v případě, že jsou připojeny všechna připojení k systému souborů NFS uzlu.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Zkontroluje stav clusteru a všech prostředků.
   > [!NOTE]
   > Tento článek obsahuje odkazy na *podřízený*termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Tato část popisuje, jak můžete otestovat instalaci. 

1. Než začnete s testem, ujistěte se, že Pacemaker nemá žádnou neúspěšnou akci (prostřednictvím stavu počítače), nejsou k dispozici žádná neočekávaná omezení umístění (například Leftovers testu migrace) a tato replikace systému HANA je stav synchronizace, například s systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Ověření konfigurace clusteru pro scénář selhání, když uzel ztratí přístup ke sdílené složce systému souborů NFS (/Hana/Shared)

   Agenti SAP HANA prostředků závisí na binárních souborech uložených v `/hana/shared` k provádění operací během převzetí služeb při selhání. Systém souborů  `/hana/shared` je v uvedeném scénáři připojen přes systém souborů NFS.  
   Je obtížné simulovat selhání, kdy jeden ze serverů ztratí přístup ke sdílené složce systému souborů NFS. Test, který může být proveden, je znovu připojit systém souborů jako jen pro čtení.
   Tento přístup ověří, že cluster bude moci převzít služby při selhání, pokud dojde ke `/hana/shared` ztrátě přístupu k aktivnímu uzlu.     


   **Očekávaný výsledek:** Při vytváření `/hana/shared` jako systému souborů jen pro čtení dojde k `OCF_CHECK_LEVEL` selhání atributu prostředku `hana_shared1` , který provádí operaci čtení/zápisu v systému souborů, protože nebude moci zapisovat cokoli v systému souborů a provede převzetí služeb při selhání prostředku Hana.  Je očekáván stejný výsledek, pokud váš uzel HANA ztratí přístup ke sdíleným složkám systému souborů NFS. 

   Stav prostředku před spuštěním testu:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   /Hana/Shared můžete umístit do režimu jen pro čtení na aktivním uzlu clusteru pomocí příkazu níže:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 se buď restartuje, nebo stavu PowerOff, a to na základě akce nastavené v stonith ( `pcs property show stonith-action` ).  Po vypnutí serveru (hanadb1) se prostředek HANA přesune na hanadb2. Stav clusteru můžete kontrolovat z hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Doporučujeme důkladně otestovat SAP HANA konfiguraci clusteru, a to tak, že provedete testy popsané v [nastavení SAP HANA systémová replikace v RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).