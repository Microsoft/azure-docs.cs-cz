---
title: Nasazení SAP pomocného mechanismu pro Azure Virtual Machines DBMS pro úlohy SAP | Microsoft Docs
description: Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 430af1dfcbd9c7faabfca2f3f5b80b3b3106a889
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675792"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP

V tomto dokumentu popisuje několik různých oblastí, které je potřeba vzít v úvahu při nasazování pomocného mechanismu SAP v Azure IaaS. Jako předběžnou podmínkou tohoto dokumentu byste měli mít přehled o dokumentech [pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_general.md) a další příručky v [dokumentaci ke službě SAP v dokumentaci k Azure](./get-started.md). Tento dokument popisuje službu SAP pomocného programu v operačních systémech Linux a Windows. Minimální podporovaná verze v Azure je SAP pomocného mechanismu 16.0.02 (verze 16 support Pack 2). Doporučuje se nasadit nejnovější verzi SAP a nejnovější úroveň oprav.  Pro SAP je doporučena minimální verze SAP pomocného 16.0.03.07 (verze 16 support Pack 3, úroveň opravy 7).  Nejnovější verzi SAP najdete v části cílový Průvodce příznakem pro [vydání 16,0 a informace o seznamu CR](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

K dispozici jsou další informace o podpoře vydaných verzí s aplikacemi SAP nebo umístěním instalačních médií, kromě v matici dostupnosti produktu SAP v těchto umístěních:

- [Poznámka k podpoře SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Poznámka k podpoře SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Poznámka k podpoře SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Poznámka k podpoře SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Remark: v celé dokumentaci v rámci produktu SAP World i mimo něj je název produktu odkazován jako Sybase pomocného programu nebo SAP pomocného programu nebo v některých případech. Aby bylo možné zachovat konzistenci, použijeme v této dokumentaci název **SAP POmocného mechanismu** .

## <a name="operating-system-support"></a>Podpora operačního systému
Matice dostupnosti produktu SAP obsahuje podporované kombinace operačních systémů a jádra SAP pro jednotlivé aplikace SAP.  Distribuce systému Linux SUSE 12. x, SUSE 15. x, Red Hat 7. x jsou plně podporovány.  Oracle Linux jako operační systém pro SAP pomocného mechanismu řízení se nepodporuje.  Doporučuje se používat nejnovější dostupné verze systému Linux. Zákazníci se systémem Windows by měli používat verze Windows Server 2016 nebo Windows Server 2019.  Starší verze Windows, jako je Windows 2012, jsou podporované technicky, ale doporučuje se vždycky použít nejnovější verzi Windows.


## <a name="specifics-to-sap-ase-on-windows"></a>Specifická pro SAP pomocného programu SAP ve Windows
Počínaje Microsoft Azure můžete stávající aplikace SAP pomocného mechanismu migrovat do Azure Virtual Machines. Protokol SAP pomocného programu na virtuálním počítači Azure umožňuje snížit celkové náklady na vlastnictví nasazení, správy a údržby aplikací v rozlehlých sítích, a to tak, že tyto aplikace jednoduše migrujete do Microsoft Azure. S pomocným mechanismem SAP na virtuálním počítači Azure můžou správci a vývojáři dál používat stejné nástroje pro vývoj a správu, které jsou k dispozici místně.

Microsoft Azure nabízí spoustu různých typů virtuálních počítačů, které vám umožní spustit nejmenší systémy SAP a využít až velké systémy SAP a možnost krajiny s tisíci uživateli. Poznámky SAP o velikosti SAP různých SKU pro virtuální počítače SAP jsou k dispozici v [poznámkách k podpoře sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Dokumentaci k instalaci SAP pomocného programu SAP ve Windows najdete v [instalační příručce SAP POmocného mechanismu pro Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html) .

Uzamknout stránky v paměti je nastavení, které zabrání stránkovanému vystavení vyrovnávací paměti databáze SAP pomocného mechanismu.  Toto nastavení je užitečné pro velké zaneprázdněné systémy s velkým množstvím paměti. Další informace získáte v kontaktu BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>Nastavení specifické pro operační systém Linux
V případě virtuálních počítačů se systémem Linux `saptune` by měly být ve výchozím nastavení povoleny s profilem SAP-POmocného programu Linux a lze ho ověřit pomocí příkazu.  

`cat /proc/meminfo` 

Velikost stránky je obvykle 2048 KB. Podrobnosti najdete v článku [velké stránky v systému Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) . 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Doporučení k VIRTUÁLNÍm počítačům a diskovým strukturám pro nasazení SAP pomocného mechanismu

Pro všechny typy virtuálních počítačů uvedené v [poznámce SAP Support](https://launchpad.support.sap.com/#/notes/1928533) se podporuje SAP Poznámkový pro aplikace SAP #1928533 typické typy virtuálních počítačů používané pro databázové servery SAP pomocného mechanismu podpory střední velikosti zahrnují Esv3.  Velké databáze s více terabajty můžou využívat typy virtuálních počítačů řady M-Series. Zvýšení výkonu zápisu na disk protokolu transakcí SAP pomocného protokolu se může zlepšit povolením Akcelerátor zápisu řady M-Series. Akcelerátor zápisu by se měla pečlivě testovat se SAP pomocným mechanismem SAP v důsledku způsobu, jakým protokol SAP pomocného programu provádí zápis  Přečtěte [si poznámku k podpoře SAP #2816580](../../how-to-enable-write-accelerator.md) a zvažte spuštění testu výkonnosti.  
Akcelerátor zápisu je navržena pouze pro disk protokolu transakcí. Mezipaměť na úrovni disku by měla být nastavená na NONE. Nepoužívejte překvapen, pokud Azure Akcelerátor zápisu nezobrazuje podobná vylepšení jako s jinými systémy DBMS. Na základě způsobu, jakým protokol SAP pomocného mechanismu zápisu do transakčního protokolu, může to být tím, že Azure Akcelerátor zápisu nezrychluje žádnou akceleraci.
Pro datová zařízení a protokolová zařízení se doporučují samostatné disky.  Systémové databáze sybsecurity a `saptools` nevyžadují vyhrazené disky a je možné je umístit na disky obsahující data databáze SAP a protokolová zařízení. 

![Konfigurace úložiště pro SAP pomocného mechanismu](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Systémy souborů, velikost Stripe & vyrovnávání v/v 
SAP pomocného mechanismu zapisuje data postupně do zařízení diskového úložiště, pokud není nakonfigurováno jinak. To znamená, že prázdná databáze SAP pomocného programu se čtyřmi zařízeními zapisuje data pouze do prvního zařízení.  Ostatní disková zařízení budou zapsána pouze v případě, že první zařízení je zaplněno.  Množství vstupně-výstupních operací čtení a zápisu pro každé zařízení SAP pomocného programu se pravděpodobně liší. K vyrovnávání vstupně-výstupních operací disku na všech dostupných discích Azure musí být použit buď prostory úložiště Windows, nebo Linux LVM2. V systému Linux se k formátování disků doporučuje použít systém souborů XFS. Velikost proložení LVM by měla být testována pomocí testu výkonnosti. velikost proložení 128 KB je dobrým výchozím bodem. Ve Windows by se měla testovat velikost alokační jednotky NTFS (Austrálie). 64 KB lze použít jako počáteční hodnotu. 

Doporučuje se nakonfigurovat automatické rozšíření databáze, jak je popsáno v článku [Konfigurace automatického rozšíření prostoru v databázi v SAP adaptivního serveru Enterprise a v](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) [poznámkách k podpoře SAP #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Ukázkový pomocného mechanismu pro SAP na virtuálním počítači Azure, konfiguraci disků a systémů souborů 
Níže uvedené šablony znázorňují ukázkové konfigurace pro Linux i Windows. Před potvrzením konfigurace virtuálního počítače a disku zajistěte, aby byly kvóty šířky pásma sítě a úložiště pro jednotlivé virtuální počítače dostačující pro splnění podnikového požadavku. Pamatujte také, že různé typy virtuálních počítačů Azure mají různý maximální počet disků, které se dají připojit k virtuálnímu počítači. Například virtuální počítač E4s_v3 má omezení propustnosti vstupně-výstupních operací úložiště 48 MB/s. Pokud propustnost úložiště vyžadovaná aktivitou zálohování databáze vyžaduje více než 48 MB/s, větší typ virtuálního počítače s větší propustností šířky pásma úložiště je nenevyhnutelný. Při konfiguraci služby Azure Storage je také nutné mít na paměti, že se v [Azure Premium Storage](../../premium-storage-performance.md) mění propustnost a IOPS za GB kapacity. Další informace najdete v tomto tématu v článku [Jaké typy disků jsou k dispozici v Azure?](../../disks-types.md). Kvóty pro konkrétní typy virtuálních počítačů Azure jsou popsány v článku [paměťově optimalizované velikosti virtuálních počítačů](../../sizes-memory.md) a články s nimi propojené. 

> [!NOTE]
>  Pokud se systém DBMS přesouvá z místního prostředí do Azure, doporučuje se provést monitorování virtuálního počítače a vyhodnotit procesor, paměť, IOPS a propustnost úložiště. Porovnejte hodnoty ve špičce zjištěné s omezeními kvóty virtuálních počítačů popsanými v článcích uvedených výše.

Níže uvedené příklady jsou pro ilustrativní účely a dají se upravit na základě individuálních potřeb. Vzhledem k návrhu SAP pomocného programu SAP není počet datových zařízení tak důležitý jako u jiných databází. Počet datových zařízení, která jsou popsána v tomto dokumentu, je pouze průvodce. 

Příklad konfigurace malého serveru databáze s pomocným mechanismem SAP s velikostí databáze mezi 50 GB – 250 GB, jako je třeba SAP Solution Manager, může vypadat takto:

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního počítače | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Akcelerované síťové služby | Povolit | Povolit | ---|
| Verze protokolu SAP pomocného mechanismu | 16.0.03.07 nebo vyšší | 16.0.03.07 nebo vyšší | --- |
| počet datových zařízení | 4 | 4 | ---|
| počet zařízení protokolu | 1 | 1 | --- |
| počet dočasných zařízení | 1 | 1 | Další pro úlohu SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/15 SP1 nebo RHEL 7,6 | --- |
| Agregace disků | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | XFS |
| Velikost bloku formátu | vyžaduje testování úloh | vyžaduje testování úloh | --- |
| počet a typ datových disků | Premium Storage: 2 x P10 (RAID0) | Premium Storage: 2 x P10 (RAID0)| Cache = jen pro čtení |
| počet a typ disků protokolu | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = NONE |
| MaxMemory parametr pomocného mechanismu | 90% fyzické paměti RAM | 90% fyzické paměti RAM | Předpokládá se jediná instance. |
| počet zálohovacích zařízení | 4 | 4| --- |
| počet a typ záložních disků | 1 | 1 | --- |


Příkladem konfigurace pro střední Server databáze pomocného programu SAP s velikostí databáze mezi 250 GB – 750 GB, například menším systémem SAP Business Suite, může vypadat takto:

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního počítače | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Akcelerované síťové služby | Povolit | Povolit | ---|
| Verze protokolu SAP pomocného mechanismu | 16.0.03.07 nebo vyšší | 16.0.03.07 nebo vyšší | --- |
| počet datových zařízení | 8 | 8 | ---|
| počet zařízení protokolu | 1 | 1 | --- |
| počet dočasných zařízení | 1 | 1 | Další pro úlohu SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/15 SP1 nebo RHEL 7,6 | --- |
| Agregace disků | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | XFS |
| Velikost bloku formátu | vyžaduje testování úloh | vyžaduje testování úloh | --- |
| počet a typ datových disků | Premium Storage: 4 x P20 (RAID0) | Premium Storage: 4 x P20 (RAID0)| Cache = jen pro čtení |
| počet a typ disků protokolu | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = NONE |
| MaxMemory parametr pomocného mechanismu | 90% fyzické paměti RAM | 90% fyzické paměti RAM | Předpokládá se jediná instance. |
| počet zálohovacích zařízení | 4 | 4| --- |
| počet a typ záložních disků | 1 | 1 | --- |

Příkladem konfigurace malého serveru databáze pomocného mechanismu pro SAP a velikosti databáze mezi 750 GB – 2000 GB, například větším systémem SAP Business Suite, může vypadat takto:

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního počítače | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Akcelerované síťové služby | Povolit | Povolit | ---|
| Verze protokolu SAP pomocného mechanismu | 16.0.03.07 nebo vyšší | 16.0.03.07 nebo vyšší | --- |
| počet datových zařízení | 16 | 16 | ---|
| počet zařízení protokolu | 1 | 1 | --- |
| počet dočasných zařízení | 1 | 1 | Další pro úlohu SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/15 SP1 nebo RHEL 7,6 | --- |
| Agregace disků | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | XFS |
| Velikost bloku formátu | vyžaduje testování úloh | vyžaduje testování úloh | --- |
| počet a typ datových disků | Premium Storage: 4 x P30 (RAID0) | Premium Storage: 4 x P30 (RAID0)| Cache = jen pro čtení |
| počet a typ disků protokolu | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = NONE |
| MaxMemory parametr pomocného mechanismu | 90% fyzické paměti RAM | 90% fyzické paměti RAM | Předpokládá se jediná instance. |
| počet zálohovacích zařízení | 4 | 4| --- |
| počet a typ záložních disků | 1 | 1 | --- |


Příkladem konfigurace malého serveru databáze s pomocným mechanismem SAP s velikostí databáze 2 TB +, jako je větší globálně používaný systém SAP Business Suite, může vypadat takto.

| Konfigurace | Windows | Linux | Komentáře |
| --- | --- | --- | --- |
| Typ virtuálního počítače | Řada M-Series (1,0 až 4,0 TB RAM)  | Řada M-Series (1,0 až 4,0 TB RAM) | --- |
| Akcelerované síťové služby | Povolit | Povolit | ---|
| Verze protokolu SAP pomocného mechanismu | 16.0.03.07 nebo vyšší | 16.0.03.07 nebo vyšší | --- |
| počet datových zařízení | 32 | 32 | ---|
| počet zařízení protokolu | 1 | 1 | --- |
| počet dočasných zařízení | 1 | 1 | Další pro úlohu SAP BW |
| Operační systém | Windows Server 2019 | SUSE 12 SP4/15 SP1 nebo RHEL 7,6 | --- |
| Agregace disků | Prostory úložiště | LVM2 | --- |
| Systém souborů | NTFS | XFS |
| Velikost bloku formátu | vyžaduje testování úloh | vyžaduje testování úloh | --- |
| počet a typ datových disků | Premium Storage: 4 + x P30 (RAID0) | Premium Storage: 4 + x P30 (RAID0)| Cache = jen pro čtení, zvážení Azure Ultra disk |
| počet a typ disků protokolu | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = NONE, zvážení Azure Ultra disk |
| MaxMemory parametr pomocného mechanismu | 90% fyzické paměti RAM | 90% fyzické paměti RAM | Předpokládá se jediná instance. |
| počet zálohovacích zařízení | 16 | 16 | --- |
| počet a typ záložních disků | 4 | 4 | Použití LVM2/prostorů úložiště |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Předpoklady pro zálohování & obnovení pro SAP pomocného programu v Azure
Zvýšením počtu dat a zálohovacích zařízení se zvýší výkon zálohování a obnovení. Doporučuje se prokládat disky Azure, které hostují zálohovací zařízení SAP pomocného programu, jako v tabulkách uvedených výše. Měli byste dbát na vyrovnávání počtu zálohovacích zařízení a disků a zajistěte, aby propustnost zálohování neměla překročit 40% až 50% z celkové kvóty propustnosti virtuálního počítače. Jako výchozí se doporučuje použít kompresi zálohování SAP. Další podrobnosti najdete v článcích:

- [Poznámka k podpoře SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Poznámka k podpoře SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Poznámka k podpoře SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Nepoužívat jednotku D:\ nebo/Temp místo jako cíl výpisu databáze nebo protokolu.

### <a name="impact-of-database-compression"></a>Dopad komprimace databáze
V konfiguracích, kde se může v/v šířka pásma stát omezením, můžou míry, které omezují IOPS, způsobit roztažení zatížení, které je možné využít ve scénáři IaaS, jako je Azure. Proto se před nahráním existující databáze SAP do Azure doporučuje použít kompresi SAP pomocného mechanismu.

Doporučení použít kompresi před nahráním do Azure je z několika důvodů:

* Množství dat, která se mají nahrát do Azure, je nižší.
* Doba trvání spuštění komprese je kratší, předpokládá se, že jedna může používat silnější hardware s více procesory nebo vyšší propustnost vstupně-výstupních operací nebo méně v/v místní latenci.
* Menší velikosti databází můžou vést k menšímu množství nákladů na přidělení disku.

Data a LOB-Compression fungují na virtuálním počítači hostovaném v Azure Virtual Machines jako v místním prostředí. Další podrobnosti o tom, jak zjistit, jestli se komprese už používá v existující databázi pomocného mechanismu služby SAP, najdete v [poznámce SAP Support note 1750510](https://launchpad.support.sap.com/#/notes/1750510). Další podrobnosti o kontrole komprese databáze služby SAP pomocného bodu [SAP – poznámka #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Vysoká dostupnost služby SAP pomocného mechanismu pro Azure 
Průvodce uživateli HADR podrobně popisuje nastavení a konfiguraci řešení "Always-On" pro SAP, které má 2 uzel.  Kromě toho se podporuje i třetí uzel pro zotavení po havárii. SAP pomocného mechanismu podporuje mnoho konfigurací s vysokou dostupností, včetně sdílených disků a nativního clusteringu operačního systému (plovoucí IP adresy). Jediná podporovaná konfigurace v Azure používá správce selhání bez plovoucí IP adresy.  Metoda s plovoucí IP adresou nebude v Azure fungovat.  Jádro SAP je aplikace s podporou HA a ví o primárních a sekundárních serverech SAP pomocného programu. Mezi službou SAP pomocného mechanismu a Azure neexistují žádná uzavřená integrace, ale interní nástroj pro vyrovnávání zatížení Azure se nepoužívá. Proto by měla být standardní dokumentace k SAP pomocnému programu uvedena od začátku pomocí [Průvodce SAP POmocného uživatele hadr](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) . 

> [!NOTE]
> Jediná podporovaná konfigurace v Azure používá správce selhání bez plovoucí IP adresy.  Metoda s plovoucí IP adresou nebude v Azure fungovat. 

### <a name="third-node-for-disaster-recovery"></a>Třetí uzel pro zotavení po havárii
Kromě použití Always-On SAP pomocného programu pro místní vysokou dostupnost můžete chtít konfiguraci nakonfigurovat na asynchronně replikovaný uzel v jiné oblasti Azure. Dokumentaci k tomuto scénáři najdete [tady](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Šifrování databáze protokolu SAP pomocného mechanismu & SSL 
Správce SAP software Provisioning (SWPM) poskytuje možnost šifrovat databázi během instalace.  Pokud chcete použít šifrování, doporučuje se použít úplné šifrování databáze SAP.  Podívejte se na podrobné informace v tématu:

- [Poznámka k podpoře SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Poznámka k podpoře SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Poznámka k podpoře SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Poznámka k podpoře SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Pokud je databáze pomocného programu SAP zašifrovaná, komprese výpisu zálohy nebude fungovat. Viz také [SAP Support – poznámka #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Kontrolní seznam pro SAP pomocného bodu pro nasazení Azure
 
- Nasazení SAP pomocného 16.0.03.07 nebo vyšší úrovně
- Aktualizace na nejnovější verzi a opravy pro FaultManager a SAPHostAgent
- Nasadit na nejnovější dostupný operační systém, jako je Windows 2019, SUSE 15,1 nebo RedHat 7,6 nebo novější
- Použití certifikovaných virtuálních počítačů SAP – doporučujeme použít skladové jednotky virtuálních počítačů Azure s vysokou pamětí, jako je například Es_v3 nebo pro x-large SKU virtuálních počítačů M-Series.
- Porovnává diskové IOPS a celkovou kvótu agregované propustnosti virtuálního počítače s návrhem disku.  Nasazení dostatečného počtu disků
- Agregované disky pomocí prostorů úložiště Windows nebo Linux LVM2 se správnou velikostí a systémem souborů
- Vytvoření dostatečného počtu zařízení pro účely dat, protokolů, dočasného a zálohování
- Zvažte použití UltraDisk pro x-large systémy 
- Spuštění `saptune` SAP-POmocného mechanismu v operačním systému Linux 
- Zabezpečte databázi pomocí šifrování DB – ruční ukládání klíčů v Azure Key Vault 
- Dokončete [Kontrolní seznam SAP v Azure](./sap-deployment-checklist.md) . 
- Konfigurace zálohování protokolu a úplného zálohování 
- Test HA/DR, zálohování a obnovení a provádění zátěžového testu & 
- Potvrďte, že automatické rozšíření databáze funguje 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Monitorování instancí databáze pomocí DBACockpit
Pro systémy SAP, které používají program SAP pomocného mechanismu přístupu jako databázová platforma, je DBACockpit přístupný jako okna integrovaných prohlížečů v transakčních DBACockpit nebo jako WebDynpro. Kompletní funkce pro monitorování a správu databáze jsou však k dispozici pouze v WebDynpro implementaci DBACockpit.

Stejně jako u místních systémů je potřeba pro povolení všech funkcí SAP NetWeaver používaných WebDynpro implementací DBACockpit použít několik kroků. Pokud chcete povolit použití webdynpros a generovat požadované hodnoty, postupujte podle [poznámky k podpoře SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) . Pokud budete postupovat podle pokynů uvedených v předchozích poznámkách, můžete také nakonfigurovat nástroj Internet Communications Manager ( `ICM` ) spolu s porty, které se mají použít pro připojení HTTP a HTTPS. Výchozí nastavení pro protokol HTTP vypadá takto:

> ICM/server_port_0 = PROT = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = PROT = HTTPS, PORT = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

a odkazy vygenerované v transakční DBACockpit vypadají podobně jako:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> 

V závislosti na tom, jak je virtuální počítač Azure hostující systém SAP připojený k vaší službě AD a DNS, je potřeba zajistit, aby ICM používala plně kvalifikovaný název hostitele, který se dá vyřešit v počítači, ze kterého se DBACockpit otevírá. Informace o tom, jak ICM Určuje plně kvalifikovaný název hostitele v závislosti na parametrech profilu a v případě potřeby explicitně nastavuje parametr ICM/host_name_full, najdete v článku o [podpoře SAP – poznámka #773830](https://launchpad.support.sap.com/#/notes/773830) .

Pokud jste virtuální počítač nasadili ve scénáři Cloud-Only bez propojení mezi místními sítěmi a Azure, musíte definovat veřejnou IP adresu a `domainlabel` . Formát veřejného názvu DNS virtuálního počítače vypadá takto:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

Další podrobnosti týkající se názvu DNS najdete [tady] [Virtual-Machines-azurerm-vs-azuresm].

Nastavení parametru profilu SAP ICM/host_name_full na název DNS virtuálního počítače Azure odkaz může vypadat podobně jako:

> https: \/ /mydomainlabel.westeurope.cloudapp.NET:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.NET:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

V takovém případě je třeba zajistit následující:

* Přidat příchozí pravidla do skupiny zabezpečení sítě v Azure Portal pro porty TCP/IP používané ke komunikaci s nástrojem ICM
* Přidání příchozích pravidel do konfigurace brány Windows Firewall pro porty TCP/IP používané ke komunikaci s nástrojem ICM

Pro automatizované importy dostupných oprav se doporučuje pravidelně uplatňovat poznámku SAP ke shromažďování oprav, které platí pro vaši verzi SAP:

* [Poznámka k podpoře SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Poznámka k podpoře SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Poznámka k podpoře SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Další informace o řídicím panelu DBA pro SAP pomocného mechanismu služby najdete v následujících komentářích ke službě SAP:

* [Poznámka k podpoře SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Poznámka k podpoře SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Poznámka k podpoře SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Poznámka k podpoře SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Poznámka k podpoře SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Poznámka k podpoře SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Poznámka k podpoře SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Poznámka k podpoře SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Užitečné odkazy, poznámky & dokument white paper pro SAP pomocného mechanismu
Úvodní stránka [dokumentace SAP POmocného programu 16.0.03.07](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) obsahuje odkazy na různé dokumenty, na kterých dokumenty:

- Výuková cesta k SAP pomocnému mechanismu řízení & monitorování
- Výuková cesta k SAP pomocnému procesu – instalace & upgrade

jsou užitečné. Dalším užitečným dokumentem jsou [aplikace SAP na základě osvědčených postupů pro migraci a modul runtime SAP adaptivního serveru Enterprise](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Další užitečné poznámky k podpoře SAP:

- [Poznámka k podpoře SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Poznámka k podpoře SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Poznámka k podpoře SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Poznámka k podpoře SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Poznámka k podpoře SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Poznámka k podpoře SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Poznámka k podpoře SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Poznámka k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Poznámka k podpoře SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Poznámka k podpoře SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Poznámka k podpoře SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Poznámka k podpoře SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Poznámka k podpoře SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Další informace jsou publikovány na 

- [Aplikace SAP v systému SAP – adaptivní Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [SAP pomocného InfoCenter](http://infocenter.sybase.com/help/index.jsp) 
- [Protokol SAP pomocného programu SAP je vždy zapnutý s nastavením uzlu DR.](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Měsíční bulletin se zveřejňuje prostřednictvím [poznámky o podpoře SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Další kroky
Podívejte se na článek [o úlohách SAP v Azure: kontrolní seznam pro plánování a nasazení](./sap-deployment-checklist.md)