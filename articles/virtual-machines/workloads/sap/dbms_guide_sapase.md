---
title: Nasazení DBMS virtuálních počítačů SAP ASE Azure pro úlohy SAP | Dokumenty společnosti Microsoft
description: Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 007e8d87c670376ad334c1c4e58fd93995930b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616236"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP

V tomto dokumentu popisuje několik různých oblastí, které je třeba zvážit při nasazování služby SAP ASE v Azure IaaS. Jako podmínku pro tento dokument byste si měli přečíst dokument [Aspekty nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](dbms_guide_general.md) a další chod v [pracovním vytížení SAP v dokumentaci Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Tento dokument se vztahuje na SAP ASE běžící na Linuxu a na operačních systémech Windows. Minimální podporovaná verze v Azure je SAP ASE 16.0 Patch Level 2.  Doporučujeme nasadit nejnovější verzi SAP a nejnovější úroveň opravy.  Doporučuje se minimálně SAP ASE 16.3 Patch Level 7.  Nejnovější verzi SAP naleznete v [cílené ase 16.0 Plán vydání a CR seznam informací](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Další informace o podpoře vydání s aplikacemi SAP nebo umístěním instalačního média jsou k dispozici kromě matice dostupnosti produktu SAP v těchto umístěních:

- [Poznámka podpory SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Poznámka podpory SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Poznámka podpory SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Poznámka podpory SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Poznámka: V celé dokumentaci ve světě SAP i mimo něj je název produktu odkazován jako Sybase ASE nebo SAP ASE nebo v některých případech obojí. Abychom zůstali konzistentní, používáme název **SAP ASE** v této dokumentaci.

## <a name="operating-system-support"></a>Podpora operačního systému
Matice dostupnosti produktu SAP obsahuje podporované kombinace operačního systému a jádra SAP pro každou aplikaci SAP.  Linuxové distribuce SUSE 12.x, SUSE 15.x, Red Hat 7.x jsou plně podporovány.  Oracle Linux jako operační systém pro SAP ASE není podporován.  Doporučuje se používat nejnovější dostupné verze Linuxu. Zákazníci se systémem Windows by měli používat verze Windows Server 2016 nebo Windows Server 2019.  Starší verze systému Windows, jako je například Windows 2012, jsou technicky podporovány, ale vždy se doporučuje nejnovější verze systému Windows.


## <a name="specifics-to-sap-ase-on-windows"></a>Specifika sap ase v systému Windows
Počínaje Microsoft Azure můžete migrovat stávající aplikace sap ase do virtuálních počítačů Azure. SAP ASE ve virtuálním počítači Azure umožňuje snížit celkové náklady na vlastnictví nasazení, správy a údržby podnikových aplikací díky snadné migraci těchto aplikací do Microsoft Azure. Se službou SAP ASE ve virtuálním počítači Azure můžou správci a vývojáři pořád používat stejné nástroje pro vývoj a správu, které jsou dostupné místně.

Microsoft Azure nabízí řadu různých typů virtuálních počítačů, které umožňují spouštět nejmenší systémy sap a krajiny až po velké systémy SAP a krajiny s tisíci uživateli. SAP dimenzování SAPS čísla různých SAP certifikovaných SKU virtuálních zařízení je k dispozici v [poznámce podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Dokumentaci k instalaci služby SAP ASE do systému Windows naleznete v [příručce sap ase installation pro Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Zamknout stránky v paměti je nastavení, které zabrání vyrovnávací paměti databáze sap ase z stránkování ven.  Toto nastavení je užitečné pro velké zaneprázdněné systémy s velkým množstvím paměti. Pro více informací kontaktujte BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>Nastavení specifická pro operační systém Linux
Na virtuálních počítačích `saptune` SIO, běh s profilem SAP-ASE Linux Obrovské stránky by měly být povoleny ve výchozím nastavení a lze ověřit pomocí příkazu  

`cat /proc/meminfo` 

Velikost stránky je obvykle 2048 KB. Podrobnosti naleznete v článku [Obrovské stránky o Linuxu](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Doporučení pro virtuální počítač a strukturu disku pro nasazení SAP ASE

SAP ASE pro sap netweaver aplikace je podporována na všech typech virtuálních počítačů uvedených v [poznámce podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) typické typy virtuálních počítačů používané pro středně velké sap ase databázové servery patří Esv3.  Velké víceterabajtové databáze můžou využívat typy virtuálních počítačů řady M. Výkon zápisu disku protokolu transakcí služby SAP ASE může být vylepšen povolením akcelerátoru zápisu řady M. Akcelerátor zápisu by měl být pečlivě testován pomocí sap ase vzhledem ke způsobu, jakým SAP ASE provádí zápisprotokolu.  Zkontrolujte [poznámku podpory SAP #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) a zvažte spuštění testu výkonu.  
Akcelerátor zápisu je určen pouze pro disk protokolu transakcí. Mezipaměť na úrovni disku by měla být nastavena na hodnotu NONE. Nebuďte překvapeni, pokud Azure Write Accelerator nezobrazuje podobná vylepšení jako u jiných DBMS. Na základě způsobu SAP ASE zapisuje do transakční protokol, může být, že je málo nebo žádné zrychlení pomocí Akcelerátor zápisu Azure.
Pro datová zařízení a protokolovaná zařízení se doporučují samostatné disky.  Systém databáze sybsecurity `saptools` a nevyžadují vyhrazené disky a mohou být umístěny na disky obsahující data databáze SAP a log zařízení 

![Konfigurace úložiště pro SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Systémy souborů, velikost proklápěcí & vyvažování vi 
SAP ASE zapisuje data postupně do diskových úložných zařízení, pokud není nakonfigurováno jinak. To znamená, že prázdná databáze SAP ASE se čtyřmi zařízeními bude zapisovat data pouze do prvního zařízení.  Ostatní disková zařízení budou zapsána pouze v případě, že je první zařízení plné.  Množství čtení a zápis u Každého zařízení SAP ASE se pravděpodobně bude lišit. Chcete-li vyvážit vi disk mezi všechny dostupné disky Azure, je potřeba použít prostory úložiště Windows nebo Linux LVM2. Na Linuxu se doporučuje používat systém souborů XFS pro formátování disků. Velikost lvm proužku by měla být testována s výkonnostním testem. Velikost proužek 128 KB je dobrým výchozím bodem. V systému Windows by měla být testována velikost alokační jednotky NTFS (AUS). 64 KB lze použít jako výchozí hodnotu. 

Doporučujeme nakonfigurovat automatické rozšíření databáze, jak je popsáno v článku [Konfigurace automatického rozšíření prostoru databáze v sap adaptive server enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) a SAP poznámka podpory [#1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Ukázka sap ase na azure virtuální počítač, disk a souborový systém konfigurace 
Níže uvedené šablony zobrazují ukázkové konfigurace pro Linux i Windows. Před potvrzením konfigurace virtuálního počítače a disku se ujistěte, že kvóty šířky pásma sítě a úložiště jednotlivých virtuálních počítačů jsou dostatečné ke splnění obchodních požadavků. Mějte také na paměti, že různé typy virtuálních počítačů Azure mají různé maximální počet disků, které lze připojit k virtuálnímu počítači. Například E4s_v3 virtuální ms má omezenou propustnost úložiště 48 MB/s. Pokud propustnost úložiště vyžadovaná aktivitou zálohování databáze vyžaduje více než 48 MB/s, je nevyhnutelné, že je nevyhnutelný větší typ virtuálního počítače s větší propustností šířky pásma úložiště. Při konfiguraci úložiště Azure je také potřeba mít na paměti, že zejména s [úložištěm Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) se mění propustnost a vimp na GB kapacity. Další informace o tomto tématu najdete v článku [Jaké typy disků jsou dostupné v Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). Kvóty pro konkrétní typy virtuálních počítačů Azure jsou zdokumentovány v článku [Velikosti virtuálních počítačů optimalizované pro paměť](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) a články s ním propojené. 

> [!NOTE]
>  Pokud se systém DBMS přesouvá z místního do Azure, doporučujeme provádět monitorování na virtuálním počítači a vyhodnocovat procesor, paměť, viopy a propustnost úložiště. Porovnejte maximální hodnoty pozorované s limity kvót virtuálních měn, které jsou popsány ve výše uvedených článcích

Níže uvedené příklady jsou pro ilustrativní účely a mohou být upraveny na základě individuálních potřeb. Vzhledem k návrhu SAP ASE, počet datových zařízení není tak důležité jako u jiných databází. Počet datových zařízení uvedených v tomto dokumentu je pouze vodítkem. 

Příklad konfigurace pro malý server SAP ASE DB S velikostí databáze mezi 50 GB – 250 GB, jako je například Správce řešení SAP, může vypadat

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního virtuálního mísy | E4s_v3 (4 virtuální procesory/32 GB PAMĚTI RAM) | E4s_v3 (4 virtuální procesory/32 GB PAMĚTI RAM) | --- |
| Akcelerované síťové služby | Povolení | Povolení | ---|
| Verze sap ase | 16.3 PL 7 nebo vyšší | 16.3 PL 7 nebo vyšší | --- |
| Počet datových zařízení | 4 | 4 | ---|
| Počet log zařízení | 1 | 1 | --- |
| Počet dočasných zařízení | 1 | 1 | více pro pracovní zátěž SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 nebo RHEL 7.6 | --- |
| Agregace disku | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | Xfs |
| Formát velikosti bloku | potřebuje testování pracovního vytížení | potřebuje testování pracovního vytížení | --- |
| # a typ datových disků | Prémiové úložiště: 2 x P10 (RAID0) | Prémiové úložiště: 2 x P10 (RAID0)| Cache = Jen pro čtení |
| # a typ disků protokolu | Prémiové úložiště: 1 x P20  | Prémiové úložiště: 1 x P20 | Mezipaměť = ŽÁDNÁ |
| Parametr ASE MaxMemory | 90% fyzické paměti RAM | 90% fyzické paměti RAM | za předpokladu, že jedna instance |
| Počet zálohovacích zařízení | 4 | 4| --- |
| # a typ záložních disků | 1 | 1 | --- |


Příklad konfigurace pro střední SAP ASE DB Server s velikostí databáze mezi 250 GB – 750 GB, jako je například menší systém SAP Business Suite, může vypadat

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního virtuálního mísy | E16s_v3 (16 virtuálních procesorů/128 GB paměti RAM) | E16s_v3 (16 virtuálních procesorů/128 GB paměti RAM) | --- |
| Akcelerované síťové služby | Povolení | Povolení | ---|
| Verze sap ase | 16.3 PL 7 nebo vyšší | 16.3 PL 7 nebo vyšší | --- |
| Počet datových zařízení | 8 | 8 | ---|
| Počet log zařízení | 1 | 1 | --- |
| Počet dočasných zařízení | 1 | 1 | více pro pracovní zátěž SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 nebo RHEL 7.6 | --- |
| Agregace disku | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | Xfs |
| Formát velikosti bloku | potřebuje testování pracovního vytížení | potřebuje testování pracovního vytížení | --- |
| # a typ datových disků | Prémiové úložiště: 4 x P20 (RAID0) | Prémiové úložiště: 4 x P20 (RAID0)| Cache = Jen pro čtení |
| # a typ disků protokolu | Prémiové úložiště: 1 x P20  | Prémiové úložiště: 1 x P20 | Mezipaměť = ŽÁDNÁ |
| Parametr ASE MaxMemory | 90% fyzické paměti RAM | 90% fyzické paměti RAM | za předpokladu, že jedna instance |
| Počet zálohovacích zařízení | 4 | 4| --- |
| # a typ záložních disků | 1 | 1 | --- |

Příklad konfigurace pro malý sap ASE DB Server s velikostí databáze mezi 750 GB – 2000 GB, jako je například větší systém SAP Business Suite, může vypadat

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního virtuálního mísy | E64s_v3 (64 virtuálních procesorů/432 GB paměti RAM) | E64s_v3 (64 virtuálních procesorů/432 GB paměti RAM) | --- |
| Akcelerované síťové služby | Povolení | Povolení | ---|
| Verze sap ase | 16.3 PL 7 nebo vyšší | 16.3 PL 7 nebo vyšší | --- |
| Počet datových zařízení | 16 | 16 | ---|
| Počet log zařízení | 1 | 1 | --- |
| Počet dočasných zařízení | 1 | 1 | více pro pracovní zátěž SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 nebo RHEL 7.6 | --- |
| Agregace disku | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | Xfs |
| Formát velikosti bloku | potřebuje testování pracovního vytížení | potřebuje testování pracovního vytížení | --- |
| # a typ datových disků | Prémiové úložiště: 4 x P30 (RAID0) | Prémiové úložiště: 4 x P30 (RAID0)| Cache = Jen pro čtení |
| # a typ disků protokolu | Prémiové úložiště: 1 x P20  | Prémiové úložiště: 1 x P20 | Mezipaměť = ŽÁDNÁ |
| Parametr ASE MaxMemory | 90% fyzické paměti RAM | 90% fyzické paměti RAM | za předpokladu, že jedna instance |
| Počet zálohovacích zařízení | 4 | 4| --- |
| # a typ záložních disků | 1 | 1 | --- |


Příklad konfigurace pro malý server SAP ASE DB S velikostí databáze 2 TB+, například větší globálně používaný systém SAP Business Suite, může vypadat

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního virtuálního mísy | Řada M (1,0 až 4,0 TB RAM)  | Řada M (1,0 až 4,0 TB RAM) | --- |
| Akcelerované síťové služby | Povolení | Povolení | ---|
| Verze sap ase | 16.3 PL 7 nebo vyšší | 16.3 PL 7 nebo vyšší | --- |
| Počet datových zařízení | 32 | 32 | ---|
| Počet log zařízení | 1 | 1 | --- |
| Počet dočasných zařízení | 1 | 1 | více pro pracovní zátěž SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 nebo RHEL 7.6 | --- |
| Agregace disku | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | Xfs |
| Formát velikosti bloku | potřebuje testování pracovního vytížení | potřebuje testování pracovního vytížení | --- |
| # a typ datových disků | Prémiové úložiště: 4+ x P30 (RAID0) | Prémiové úložiště: 4+ x P30 (RAID0)| Cache = jen pro čtení, zvažte disk Azure Ultra |
| # a typ disků protokolu | Prémiové úložiště: 1 x P20  | Prémiové úložiště: 1 x P20 | Cache = NONE, zvažte disk Azure Ultra |
| Parametr ASE MaxMemory | 90% fyzické paměti RAM | 90% fyzické paměti RAM | za předpokladu, že jedna instance |
| Počet zálohovacích zařízení | 16 | 16 | --- |
| # a typ záložních disků | 4 | 4 | Použití lvm2/úložných prostorů |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Aspekty obnovení & zálohování pro sap ase v Azure
Zvýšení počtu dat a zálohovacích zařízení zvyšuje výkon zálohování a obnovení. Doporučujeme proužkovat disky Azure, které jsou hostitelem zálohovacízařízení SAP ASE, jak je znázorněno v tabulkách uvedených dříve. Je třeba dbát na vyvážení počtu zálohovacích zařízení a disků a zajistit, aby propustnost zálohování nepřekročila 40%-50 % celkové propustné kvóty virtuálních počítačů. Jako výchozí se doporučuje použít kompresi zálohování SAP. Více informací naleznete v článcích:

- [Poznámka podpory SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Poznámka podpory SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Poznámka podpory SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Nepoužívejte jednotku D:\ nebo /temp mezery jako cíl databáze nebo výpisu protokolu.

### <a name="impact-of-database-compression"></a>Dopad komprese databáze
V konfiguracích, kde vstupně-v., šířka pásma se může stát omezujícím faktorem, mohou opatření, která snižují vstupně-maily, pomoci roztáhnout úlohu, kterou lze spustit ve scénáři IaaS, jako je Azure. Proto se doporučuje ujistěte se, že sap ase komprese se používá před odesláním existující databáze SAP do Azure.

Doporučení použít kompresi před nahráním do Azure je dáno z několika důvodů:

* Množství dat, která se mají nahrát do Azure, je nižší
* Doba trvání spuštění komprese je kratší za předpokladu, že je možné použít silnější hardware s více procesory nebo vyšší vstupně-výstupní šířku pásma nebo menší latence vstupně-výstupních prostor v místním prostředí
* Menší velikosti databáze mohou vést k nižším nákladům na přidělení disku.

Komprese dat a LOB funguje ve virtuálním počítači hostovaném ve virtuálních počítačích Azure stejně jako v místním prostředí. Další podrobnosti o tom, jak zkontrolovat, zda je komprese již používána v existující databázi sap ase, naleznete na [poznámce podpory SAP 1750510](https://launchpad.support.sap.com/#/notes/1750510). Další podrobnosti o kompresi databáze SAP ASE najdete v [poznámce podpory SAP #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Vysoká dostupnost sap ase v Azure 
Hadr Users Guide podrobně popisuje nastavení a konfiguraci řešení 2 uzlu SAP ASE "Always-on".  Kromě toho je podporován třetí uzel zotavení po havárii. Sap ASE podporuje mnoho vysoce dostupných konfigurací, včetně sdíleného disku a nativního clusteringu operačního systému (plovoucí IP). Jedinou podporovanou konfigurací v Azure je použití Správce chyb bez plovoucí IP adresy.  Metoda plovoucí IP adresa nebude v Azure fungovat.  Jádro SAP je aplikace "HA Aware" a ví o primárních a sekundárních serverech SAP ASE. Neexistují žádné úzké integrace mezi SAP ASE a Azure, Azure Internal balancer se nepoužívá. Proto by měla být dodržována standardní dokumentace SAP ASE počínaje [průvodcem uživateli SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Jedinou podporovanou konfigurací v Azure je použití Správce chyb bez plovoucí IP adresy.  Metoda plovoucí IP adresa nebude v Azure fungovat. 

### <a name="third-node-for-disaster-recovery"></a>Třetí uzel pro zotavení po havárii
Kromě použití SAP ASE Always-On pro místní vysokou dostupnost, můžete chtít rozšířit konfiguraci asynchronně replikovaný uzel v jiné oblasti Azure. Dokumentaci k takovému scénáři naleznete [zde](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Šifrování databáze SAP ASE & SSL 
SAP Software provisioning Manager (SWPM) dává možnost šifrovat databázi během instalace.  Pokud chcete použít šifrování, doporučujeme použít sap full database encryption.  Podrobnosti jsou popsány v:

- [Poznámka podpory SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Poznámka podpory SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Poznámka podpory SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Poznámka podpory SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Pokud je databáze služby SAP ASE zašifrována, nebude komprese záložního výpisu fungovat. Viz také [poznámka podpory SAP #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Sap ASE na kontrolním seznamu nasazení Azure
 
- Nasazení sap ase 16,3 PL7 nebo vyšší
- Aktualizace na nejnovější verzi a opravy FaultManager a SAPHostAgent
- Nasazení na nejnovější certifikovaný operační systém k dispozici, jako je Windows 2019, Suse 15.1 nebo Redhat 7.6 nebo vyšší
- Použití virtuálních počítačů s certifikací SAP – doporučujeme su s vus virtuálních zařízení Azure s vysokou pamětí, jako jsou Es_v3 nebo pro x-velké systémy Virtuální počítače řady M
- Porovná vaviopy disku a celkové kvóty agregační propustnost virtuálního počítače virtuálního počítače s návrhem disku.  Nasazení dostatečného počtu disků
- Agregace disků pomocí prostorů úložiště Windows nebo Linuxu LVM2 se správnou velikostí proklápěcí velikosti a systémem souborů
- Vytvoření dostatečného počtu zařízení pro účely dat, protokolů, teploty a zálohování
- Zvažte použití aplikace UltraDisk pro systémy x-large 
- Spuštění `saptune` SAP-ASE v operačním systému Linux 
- Zabezpečení databáze pomocí db šifrování – ruční ukládání klíčů v azure key vaultu 
- Dokončení [sap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) na Azure Kontrolní seznam 
- Konfigurace zálohování protokolu a úplného zálohování 
- Test HA/DR, zálohování a obnovení a provedení zátěžového & multitestu 
- Potvrdit, že automatické rozšíření databáze funguje 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Použití dbacockpitu ke sledování instancí databáze
Pro systémy SAP, které používají SAP ASE jako databázovou platformu, je DBACockpit přístupný jako vestavěná okna prohlížeče v transakčním DBACockpit nebo jako Webdynpro. Plná funkčnost pro sledování a správu databáze je však k dispozici pouze v implementaci Webdynpro DBACockpit.

Stejně jako u místních systémů je zapotřebí několik kroků, které umožní všechny funkce SAP NetWeaver používané implementací DBACockpit Webdynpro. Postupujte podle [poznámky podpory SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) povolit použití webdynpros a generovat ty požadované. Při dodržování pokynů ve výše uvedených poznámkách také`ICM`nakonfigurujete Správce internetové komunikace ( ) spolu s porty, které mají být použity pro připojení http a https. Výchozí nastavení pro http vypadá takto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a odkazy generované v transakci DBACockpit vypadá podobně jako:

> https:\//\<plně kvalifikovanýnázev host>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<plně kvalifikovanýhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

V závislosti na tom, jak je virtuální počítač Azure hostující systém SAP připojen k vašemu službě AD a DNS, musíte se ujistit, že ICM používá plně kvalifikovaný název hostitele, který lze vyřešit v počítači, ze kterého otevíráte DBACockpit. Viz [poznámka podpory SAP #773830](https://launchpad.support.sap.com/#/notes/773830) pochopit, jak ICM určuje plně kvalifikovaný název hostitele na základě parametrů profilu a nastavit parametr icm/host_name_full explicitně v případě potřeby.

Pokud jste nasadili virtuální počítač ve scénáři pouze pro cloud bez připojení mezi mezi místními a `domainlabel`Azure, musíte definovat veřejnou IP adresu a . Formát veřejného názvu DNS virtuálního_ virtuálního serveru vypadá takto:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

Další podrobnosti týkající se názvu DNS naleznete [zde][virtual-machines-azurerm-versus-azuresm].

Nastavení parametru profilu SAP icm/host_name_full na název DNS virtuálního počítače Azure, odkaz může vypadat podobně jako:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

V takovém případě se musíte ujistit, že:

* Přidání příchozích pravidel do skupiny zabezpečení sítě na portálu Azure pro porty TCP/IP používané ke komunikaci s ICM
* Přidání příchozích pravidel do konfigurace brány Windows Firewall pro porty TCP/IP používané ke komunikaci s icm

Pro automatické importované všechny dostupné opravy se doporučuje pravidelně používat sbírku oprav SAP Note platnou pro vaši verzi SAP:

* [Poznámka podpory SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Poznámka podpory SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Poznámka podpory SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Další informace o DBA Cockpit pro SAP ASE naleznete v následujících poznámkách SAP:

* [Poznámka podpory SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Poznámka podpory SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Poznámka podpory SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Poznámka podpory SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Poznámka podpory SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Poznámka podpory SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Poznámka podpory SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Poznámka podpory SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Užitečné odkazy, poznámky & dokumenty white paper pro SAP ASE
Úvodní stránka dokumentace [Sybase ASE 16.3 PL7](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) obsahuje odkazy na různé dokumenty, z nichž dokumenty:

- SAP ASE Learning Journey - Správa & monitorování
- Sap ASE Learning Journey – upgrade & instalace

jsou užitečné. Dalším užitečným dokumentem jsou [sap aplikace na SAP Adaptive Server Enterprise Best Practices pro migraci a runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Další užitečné poznámky podpory SAP jsou:

- [Poznámka podpory SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Poznámka podpory SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Poznámka podpory SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Poznámka podpory SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Poznámka podpory SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Poznámka podpory SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Poznámka podpory SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Poznámka podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Poznámka podpory SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Poznámka podpory SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Poznámka podpory SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Poznámka podpory SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Poznámka podpory SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Další informace jsou zveřejňovány dne 

- [Aplikace SAP na sap adaptivní server enterprise](https://community.sap.com/topics/applications-on-ase)
- [Informační centrum Sybase](http://infocenter.sybase.com/help/index.jsp) 

Měsíční bulletin je publikován prostřednictvím [poznámky podpory SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 

[Vždy zapnutá ase služby Sybase a s nastavením třetího uzlu zotavení po havárii](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199) 

## <a name="next-steps"></a>Další kroky
Podívejte se na článek [úlohSAP v Azure: kontrolní seznam plánování a nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

