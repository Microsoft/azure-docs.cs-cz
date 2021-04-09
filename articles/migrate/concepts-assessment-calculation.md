---
title: Posouzení virtuálních počítačů Azure v Azure Migrate
description: Další informace o posouzení v Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 16c3b59bcfa14cc02f13dadd726e0380d934598b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023805"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Přehled posouzení (migrace na virtuální počítače Azure)

Tento článek poskytuje přehled posouzení v nástroji [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Tool. Nástroj může posoudit místní servery v prostředí VMware Virtual a Hyper-V a fyzické servery pro migraci do Azure.

## <a name="whats-an-assessment"></a>Co je posouzení?

Posouzení pomocí nástroje pro zjišťování a hodnocení měří připravenost a odhadne dopad migrace místních serverů do Azure.

> [!NOTE]
> V Azure Government zkontrolujte [podporovaná cílová](migrate-support-matrix.md#supported-geographies-azure-government) umístění pro posouzení. Poznámka: doporučení velikosti virtuálních počítačů v hodnoceních budou používat řadu virtuálních počítačů specificky pro oblasti cloudu státní správy. [Přečtěte si další informace](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typech virtuálních počítačů.

## <a name="types-of-assessments"></a>Typy posouzení

Existují tři typy hodnocení, které můžete vytvořit pomocí Azure Migrate: zjišťování a posouzení.

***Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. Místní servery můžete vyhodnotit v prostředí [VMware](how-to-set-up-appliance-vmware.md) a [Hyper-V](how-to-set-up-appliance-hyper-v.md) a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci na virtuální počítače Azure pomocí tohoto typu posouzení.
**Azure SQL** | Vyhodnotí migraci místních SQL serverů z prostředí VMware do Azure SQL Database nebo spravované instance Azure SQL.
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md) pro migraci do řešení Azure VMware (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

Hodnocení, která vytvoříte pomocí Azure Migrate, jsou snímkem dat v určitém časovém okamžiku. Posouzení virtuálních počítačů Azure poskytuje dvě možnosti pro kritéria změny velikosti:

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která poskytují doporučení na základě shromážděných údajů o výkonu | Doporučení na velikost virtuálního počítače vychází z dat využití procesoru a paměti RAM.<br/><br/> Doporučení pro typ disku vychází z počtu vstupně-výstupních operací za sekundu (IOPS) a propustnosti místních disků. Typy disků jsou Azure HDD úrovně Standard, Azure SSD úrovně Standard a Azure Premium disks.
**Jako v místním prostředí** | Posouzení, které nepoužívají údaje o výkonu k vytváření doporučení | Doporučení na velikost virtuálního počítače je založené na velikosti místního serveru.<br/><br> Doporučený typ disku je založený na vybraném typu úložiště pro posouzení.

## <a name="how-do-i-run-an-assessment"></a>Návody spustit posouzení?

Existuje několik způsobů, jak spustit posouzení.

- Posuzuje servery pomocí metadat serveru shromažďovaných odlehčeným Azure Migrate zařízením. Zařízení zjišťuje místní servery. Pak odešle metadata serveru a data o výkonu do Azure Migrate.
- Vyhodnoťte servery pomocí metadat serveru, která jsou importovaná ve formátu hodnot oddělených čárkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Návody posoudit pomocí zařízení?

Pokud nasazujete zařízení Azure Migrate pro zjišťování místních serverů, proveďte následující kroky:

1. Nastavte Azure a vaše místní prostředí pro práci s Azure Migrate.
1. Pro vaše první posouzení vytvořte projekt Azure a přidejte do něj Nástroj pro zjišťování a vyhodnocení.
1. Nasaďte odlehčené zařízení Azure Migrate. Zařízení nepřetržitě zjišťuje místní servery a odesílá údaje o metadatech a výkonu serveru do Azure Migrate. Nasaďte zařízení jako virtuální počítač nebo fyzický server. Nemusíte nic instalovat na servery, které chcete vyhodnotit.

Jakmile zařízení zahájí zjišťování serveru, můžete shromáždit servery, které chcete vyhodnotit do skupiny, a spustit posouzení pro skupinu s typem posouzení **virtuální počítač Azure**.

Pokud si chcete vyzkoušet tento postup, postupujte podle našich kurzů pro [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)nebo [fyzických serverů](./tutorial-discover-physical.md) .

## <a name="how-do-i-assess-with-imported-data"></a>Návody vyhodnotit s importovanými daty?

Pokud vydáváte servery pomocí souboru CSV, nepotřebujete zařízení. Místo toho proveďte následující kroky:

1. Nastavte Azure pro práci s Azure Migrate
1. Pro vaše první posouzení vytvořte projekt Azure a přidejte do něj Nástroj pro zjišťování a vyhodnocení.
1. Stáhněte si šablonu sdíleného svazku clusteru a přidejte do ní data serveru.
1. Importovat šablonu do Azure Migrate
1. Vyhledejte servery přidané pomocí importu, shromážděte je do skupiny a spusťte posouzení pro skupinu s typem posouzení **virtuální počítač Azure**.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data shromažďuje zařízení?

Pokud pro posouzení používáte zařízení Azure Migrate, přečtěte si informace o metadatech a datech o výkonu, které jsou shromažďovány pro [VMware](migrate-appliance.md#collected-data---vmware) a [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak zařízení počítá data o výkonu?

Pokud zařízení používáte ke zjišťování, shromažďuje údaje o výkonu pro nastavení výpočtů pomocí těchto kroků:

1. Zařízení shromažďuje ukázkový bod v reálném čase.

    - **Virtuální počítače VMware**: ukázkový bod se shromáždí každých 20 sekund.
    - **Virtuální počítače Hyper-V**: vzorový bod se shromáždí každých 30 sekund.
    - **Fyzické servery**: ukázkový bod se shromáždí každých pět minut.

1. Zařízení kombinuje ukázkové body, které každých 10 minut vytvoří jeden datový bod pro servery VMware a Hyper-V a každých 5 minut pro fyzické servery. Pokud chcete vytvořit datový bod, zařízení vybere vrcholové hodnoty ze všech ukázek. Pak odešle datový bod do Azure.
1. Posouzení ukládá všechny datové body 10 minut za poslední měsíc.
1. Při vytváření posouzení identifikuje posouzení vhodný datový bod, který se má použít pro snižování. Identifikace je založena na hodnotách percentilu pro *historii výkonu* a procento *využití*.

    - Například pokud je historie výkonu jeden týden a procento využití je 95. percentil, vyřadí hodnocení 10 minut vzorků za poslední týden. Seřadí je ve vzestupném pořadí a vybere hodnotu 95. percentilu pro snižování.
    - Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud jste si vybrali 99 percentil.
    - Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné odlehlé hodnoty, vyberte 99 percentil pro využití percentilu.

1. Tato hodnota se vynásobí faktorem komfortu, aby získala efektivní data o využití výkonu pro tyto metriky, které zařízení shromažďuje:

    - Využití procesoru
    - Využití paměti RAM
    - Disk IOPS (čtení a zápis)
    - Propustnost disku (čtení a zápis)
    - Propustnost sítě (v/v)

## <a name="how-are-azure-vm-assessments-calculated"></a>Jak se počítají vyhodnocení virtuálních počítačů Azure?

Při výpočtu hodnocení používá posouzení metadata a data o výkonu místních serverů. Pokud nasadíte zařízení Azure Migrate, posouzení použije data, která zařízení shromáždí. Pokud ale spustíte posouzení importované pomocí souboru CSV, poskytnete metadata pro tento výpočet.

K výpočtům dochází v těchto třech fázích:

1. **Výpočet připravenosti na Azure**: vyhodnotí, jestli jsou servery vhodné pro migraci do Azure.
1. **Výpočet doporučení pro velikost**: odhad výpočetních prostředků, úložiště a velikosti sítě.
1. **Výpočet měsíčních nákladů**: Vypočítejte Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz serverů v Azure po migraci.

Výpočty jsou v předchozím pořadí. Server serveru se přesune do pozdější fáze pouze v případě, že předá předchozí. Pokud třeba server neprojde fází připravenosti na Azure, je pro Azure označený jako nevhodný. Pro tento server se neprovádí výpočty velikosti a nákladů.

## <a name="whats-in-an-azure-vm-assessment"></a>Co je ve službě Azure VM Assessment?

Co je je součástí posouzení virtuálního počítače Azure:

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění, do kterého chcete migrovat. Posouzení aktuálně podporuje tyto cílové oblasti Azure:<br/><br/> Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed Indie, Střed USA, Čína – východ, Čína – sever, Východní Asie, východní USA, Východní USA 2, Německo – střed, Německo – severovýchod, Japonsko – východ, Japonsko – západ, Korea – jih, střed USA – sever, Severní Evropa, střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US gov – Texas, US gov – Virginie , Středozápadní USA, Západní Evropa, Západní Indie, Západní USA a Západní USA 2.
**Cílový disk úložiště (stejně jako velikost)** | Typ disku, který se má použít pro úložiště v Azure. <br/><br/> Zadejte cílový disk úložiště jako spravovaný SSD úrovně Standard spravovaný na úrovni Premium nebo HDD úrovně Standard.
**Cílový disk úložiště (Změna velikosti na základě výkonu)** | Určuje typ cílového úložného disku jako automatické, spravované na úrovni Premium, HDD úrovně Standard spravovaný nebo SSD úrovně Standard spravovaný.<br/><br/> **Automaticky**: doporučení na disku vychází z údajů o výkonu disků, což znamená vstupně-výstupní operace a propustnost.<br/><br/>**Premium nebo Standard**: posouzení doporučuje SKU disku v rámci vybraného typu úložiště.<br/><br/> Pokud požadujete smlouvu SLA s jednou instancí služby (SLA) 99,9%, zvažte použití disků spravovaných na úrovni Premium. Tím se zajistí, že se všechny disky v posouzení doporučují jako disky spravované na úrovni Premium.<br/><br/> Azure Migrate podporuje pro vyhodnocení migrace pouze spravované disky.
**Azure Reserved VM Instances** | Určuje [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) , aby se odhady nákladů v rámci posouzení zohlednily v úvahu.<br/><br/> Když vyberete možnost rezervované instance, sleva (%) a vlastnosti pro dobu provozu virtuálního počítače nelze použít.<br/><br/> Azure Migrate aktuálně podporuje Azure Reserved VM Instances jenom pro nabídky s průběžnými platbami.
**Kritéria stanovení velikosti** | Používá se k nastavte správnou velikost virtuálního počítače Azure.<br/><br/> Použijte změnu velikosti nebo určení velikosti na základě výkonu.
**Historie výkonu** | Používá se při změně velikosti na základě výkonu. Historie výkonu určuje dobu použitou při vyhodnocování údajů o výkonu.
**Percentilové využití** | Používá se při změně velikosti na základě výkonu. Procento využití Určuje hodnotu percentilu pro vzorek výkonu, který se používá pro snižování.
**Řada virtuálních počítačů** | Řady virtuálních počítačů Azure, které chcete zvážit pro snižování. Pokud například nemáte produkční prostředí, které potřebuje pro virtuální počítače řady A-Series v Azure, můžete ze seznamu řad vyloučit řady.
**Faktor komfortu** | Vyrovnávací paměť použitá během posouzení. Aplikuje se na procesor, paměť RAM, disk a síťová data pro virtuální počítače. IT účty pro problémy, jako je sezónní využití, historie krátkého výkonu a pravděpodobná zvýšení využití v budoucnu.<br/><br/> Například virtuální počítač s 10 jádry s 20% využitím obvykle vede k virtuálnímu počítači se dvěma jádry. Výsledkem je faktor komfortu 2,0, ale výsledkem je virtuální počítač se čtyřmi jádry.
**Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/) , do které jste zaregistrovaní. Posouzení odhaduje náklady na tuto nabídku.
**Měna** | Fakturační měna vašeho účtu.
**Sleva (%)** | Všechny slevy specifické pro předplatné, které obdržíte nad nabídkou Azure. Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Doba ve dnech za měsíc a hodiny za den pro virtuální počítače Azure, které nebudou běžet nepřetržitě. Odhad nákladů vychází z této doby trvání.<br/><br/> Výchozí hodnoty jsou 31 dní za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud má nastavení výchozí hodnotu "Ano", ceny za Azure pro jiné operační systémy než Windows se považují za virtuální počítače s Windows.
**Předplatné EA** | Určuje, že se pro odhad nákladů používá předplatné smlouva Enterprise (EA). Bere v úvahu slevu, která se vztahuje k předplatnému. <br/><br/> Ponechat nastavení pro rezervované instance, sleva (%) a vlastnosti pro dobu provozu virtuálních počítačů s jejich výchozími nastaveními.


[Projděte si osvědčené postupy](best-practices-assessment.md) pro vytvoření posouzení pomocí Azure Migrate.

## <a name="calculate-readiness"></a>Vypočítat připravenost

Ne všechny servery jsou vhodné ke spuštění v Azure. Posouzení virtuálního počítače Azure vyhodnocuje všechny místní servery a přiřadí jim kategorii připravenosti.

- **Připraveno pro Azure**: Server se dá migrovat tak, jak je, do Azure bez jakýchkoli změn. Spustí se v Azure s plnou podporou Azure.
- **Podmíněně připravené pro Azure**: Server může začínat v Azure, ale nemusí mít plnou podporu Azure. Azure například nepodporuje server, na kterém je spuštěná stará verze Windows serveru. Než tyto servery migrujete do Azure, musíte být opatrní. Pokud chcete opravit případné problémy s připraveností, postupujte podle pokynů k nápravě, které posouzení navrhuje.
- **Nepřipraveno pro Azure**: Server se v Azure nespustí. Pokud například disk na místním serveru uchovává více než 64 TB, Azure nemůže hostovat server. Pokud chcete problém vyřešit před migrací, postupujte podle pokynů k nápravě.
- **Připravenost neznámá**: Azure Migrate nemůže určit připravenost serveru z důvodu nedostatečných metadat.

Pro výpočet připravenosti zkontroluje posouzení vlastnosti serveru a nastavení operačního systému shrnuté v následujících tabulkách.

### <a name="server-properties"></a>Vlastnosti serveru

V případě posouzení virtuálních počítačů Azure vyhodnotí posouzení následující vlastnosti místního virtuálního počítače, aby zjistila, jestli se dá spustit na virtuálních počítačích Azure.

Vlastnost | Podrobnosti | Stav připravenosti na Azure
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spouštění systému BIOS, nikoli UEFI. | Podmíněně připravený, pokud je typ spouštění UEFI
**Cores** | Každý server nesmí mít více než 128 jader, což je maximální počet podporovaných virtuálních počítačů Azure.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využité jádra k porovnání. Pokud nastavení posouzení určí faktor komfortu, vynásobí se počet využitých jader faktorem komfortu.<br/><br/> Pokud není k dispozici žádná historie výkonu, Azure Migrate používá přidělená jádra k použití faktoru pohodlí. | Připraveno, pokud je počet jader v rámci limitu
**SRAM** | Každý server musí mít maximálně 3 892 GB paměti RAM, což je maximální velikost, kterou virtuální počítač Azure M-Series Standard_M128m &nbsp; <sup>2</sup> podporuje. [Další informace](../virtual-machines/sizes.md).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využitou paměť RAM za účelem porovnání. Pokud je určen faktor komfortu, vynásobit se využití paměti RAM faktorem komfortu.<br/><br/> Pokud není k dispozici žádná historie, použije se přidělená paměť RAM k použití faktoru pohodlí.<br/><br/> | Připraveno, pokud je velikost paměti RAM v rámci limitu
**Disk úložiště** | Přidělená velikost disku nesmí být větší než 32 TB. I když Azure podporuje disky 64 – TB s SSD úrovně Ultra disky Azure, posouzení aktuálně kontroluje 32 TB jako omezení velikosti disku, protože to ještě nepodporuje SSD úrovně Ultra. <br/><br/> Počet disků připojených k serveru, včetně disku s operačním systémem, musí být 65 nebo menší. | Připraveno, pokud je velikost disku a číslo v mezích
**Sítě** | Server musí mít k tomuto serveru k dispozici maximálně 32 síťových rozhraní (nic). | Připraveno, pokud je počet síťových adaptérů v limitu

### <a name="guest-operating-system"></a>Hostovaný operační systém

U posouzení virtuálních počítačů Azure spolu s kontrolou vlastností virtuálního počítače vyhledá posouzení hostovaný operační systém serveru a určí, jestli se dá spustit na Azure.

> [!NOTE]
> Pro zpracování analýzy hostů pro virtuální počítače VMware používá posouzení operační systém určený pro virtuální počítač v vCenter Server. VCenter Server ale neposkytuje verzi jádra pro operační systémy Linux VM. Chcete-li zjistit verzi, je třeba nastavit [zjišťování aplikací](./how-to-discover-applications.md). Zařízení pak zjistí informace o verzi pomocí přihlašovacích údajů hosta, které zadáte při nastavení zjišťování aplikací.


Posouzení používá následující logiku k identifikaci připravenosti na Azure na základě operačního systému:

**Operační systém** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
Windows Server 2016 a všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2012 R2 a všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2012 a všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2008 R2 se všemi službami SPs | Azure poskytuje plnou podporu.| Připraveno pro Azure.
Windows Server 2008 (32 a 64-bit) | Azure poskytuje plnou podporu. | Připraveno pro Azure.
Windows Server 2003 a Windows Server 2003 R2 | Tyto operační systémy prošly datem ukončení podpory a potřebují pro podporu v Azure [vlastní smlouvu o podpoře (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) . | Podmíněně připravené pro Azure. Před migrací do Azure zvažte možnost upgradovat operační systém.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 a MS-DOS | Tyto operační systémy prošly datem ukončení podpory. Server se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme upgradovat operační systém.
Windows 7, Windows 8 a Windows 10 | Azure poskytuje podporu jenom s [předplatným sady Visual Studio.](../virtual-machines/windows/client-images.md) | Podmíněně připravené pro Azure.
Windows 10 Pro | Azure podporuje [práva hostování s více klienty.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Podmíněně připravené pro Azure.
Windows Vista a Windows XP Professional | Tyto operační systémy prošly datem ukončení podpory. Server se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme upgradovat operační systém.
Linux | Podívejte se na [operační systémy Linux](../virtual-machines/linux/endorsed-distros.md) , které Azure schválí. Ostatní operační systémy Linux se můžou v Azure spustit. Doporučujeme ale před migrací do Azure upgradovat operační systém na schválenou verzi. | Je připravený pro Azure, pokud je verze schválená.<br/><br/>Podmíněně připravený, pokud verze není schválená.
Jiné operační systémy jako Oracle Solaris, Apple macOS a FreeBSD | Azure tyto operační systémy neschvaluje. Server se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme nainstalovat podporovaný operační systém.  
Operační systém určený jako **jiný** v vCenter Server | Azure Migrate v tomto případě nedokáže identifikovat operační systém. | Neznámá připravenost. Ujistěte se, že Azure podporuje operační systém běžící v rámci virtuálního počítače.
32 – bitové operační systémy | Server se může v Azure spustit, ale Azure možná neposkytne plnou podporu. | Podmíněně připravené pro Azure. Před migrací do Azure zvažte možnost upgradovat 64 na 64bitový operační systém.

## <a name="calculating-sizing"></a>Výpočet velikosti

Až bude server označený jako připravený pro Azure, vyhodnotí se doporučení na změnu velikosti ve službě Azure VM Assessment. Tato doporučení identifikují virtuální počítač Azure a SKU disku. Výpočty velikosti závisí na tom, jestli používáte místní velikost nebo změnu velikosti na základě výkonu.

### <a name="calculate-sizing-as-is-on-premises"></a>Vypočítat velikost (jako místní)

 Pokud použijete místní velikost, hodnocení nebere v úvahu historii výkonu virtuálních počítačů a disků ve službě Azure VM Assessment.

- **Výpočet velikosti**: posouzení PŘIDĚLUJE SKU virtuálního počítače Azure na základě velikosti přidělené místně.
- **Velikost úložiště a disku**: posouzení vyhledává typ úložiště určený ve vlastnostech posouzení a doporučuje vhodný typ disku. Možné typy úložišť jsou HDD úrovně Standard, SSD úrovně Standard a Premium. Výchozí typ úložiště je Premium.
- Nastavení **velikosti sítě**: posouzení zohledňuje síťový adaptér na místním serveru.

### <a name="calculate-sizing-performance-based"></a>Vypočítat velikost (na základě výkonu)

Pokud v posouzení virtuálního počítače Azure používáte změnu velikosti na základě výkonu, vyhodnocování vydává doporučení pro změnu velikosti takto:

- Posouzení považuje historii výkonu serveru za účelem identifikace velikosti virtuálního počítače a typu disku v Azure.
- Pokud importujete servery pomocí souboru CSV, použijí se hodnoty, které zadáte. Tato metoda je užitečná hlavně v případě, že jste předělili místní server, využití je nízké a chcete nastavte správnou velikost virtuální počítač Azure za účelem úspory nákladů.
- Pokud nechcete data o výkonu používat, obnovte kritéria změny velikosti na místní, jak je popsáno v předchozí části.

#### <a name="calculate-storage-sizing"></a>Výpočet velikosti úložiště

Pro určení velikosti úložiště v rámci posouzení virtuálních počítačů Azure se Azure Migrate pokusí mapovat každý disk, který je připojený k serveru, na disk Azure. Velikost funguje takto:

1. Vyhodnocení přidá IOPS čtení a zápisu disku, aby získal celkový počet požadovaných IOPS. Podobně přidá hodnoty propustnosti čtení a zápisu k získání celkové propustnosti každého disku. V případě posouzení na základě importu máte možnost poskytnout celkový počet vstupně-výstupních operací za sekundu, celkovou propustnost a celkový počet. disků v importovaném souboru bez zadání nastavení jednotlivých disků. Pokud to uděláte, přeskočí se velikost jednotlivých disků a dodaná data se použijí přímo k výpočtu velikosti a vyberte příslušnou SKU virtuálního počítače.

1. Pokud jste typ úložiště určili jako automatický, je vybraný typ založený na platných hodnotách IOPS a propustnosti. Posouzení určuje, jestli se má namapovat disk na disk s HDD úrovně Standard, SSD úrovně Standard nebo Premium v Azure. Pokud je typ úložiště nastavený na jeden z těchto typů disků, vyhodnocování se pokusí najít SKU disku v rámci vybraného typu úložiště.
1. Disky jsou vybrané takto:
    - Pokud posouzení nemůže najít disk s požadovanými vstupně-výstupními operacemi a propustností, označí Server jako nevhodný pro Azure.
    - Pokud posouzení najde sadu vhodných disků, vybere disky, které podporují umístění zadané v nastavení posouzení.
    - Pokud existuje více způsobilých disků, vyhodnocování vybere disk s nejnižšími náklady.
    - Pokud nejsou k dispozici údaje o výkonu pro libovolný disk, použije se k nalezení SSD úrovně Standard disku v Azure velikost konfiguračního disku.

#### <a name="calculate-network-sizing"></a>Výpočet velikosti sítě

V případě posouzení virtuálních počítačů Azure se vyhodnocování pokusí najít virtuální počítač Azure, který podporuje počet a požadovaný výkon síťových adaptérů připojených k místnímu serveru.

- Aby se dosáhlo efektivního výkonu sítě na místním serveru, vyhodnocování agreguje přenosovou rychlost přenosu dat ze serveru (sítě) ve všech síťových adaptérech. Pak aplikuje faktor pohodlí. Pomocí výsledné hodnoty vyhledá virtuální počítač Azure, který může podporovat požadovaný výkon sítě.
- Společně s výkonem sítě vyhodnocování taky vybere, jestli virtuální počítač Azure podporuje požadovaný počet síťových adaptérů.
- Pokud nejsou k dispozici data o výkonu sítě, posouzení považuje pouze počet síťových adaptérů pro velikost virtuálního počítače.

#### <a name="calculate-compute-sizing"></a>Vypočítat výpočetní velikost

Po výpočtu požadavků na úložiště a síť vyhodnocování vyhodnotí požadavky na procesor a paměť RAM, aby se v Azure vyhledala vhodná velikost virtuálního počítače.

- Azure Migrate podíváme se na efektivní využitá jádra a paměť RAM a najděte vhodnou velikost virtuálního počítače Azure.
- Pokud se nenajde žádná vhodná velikost, server je pro Azure označený jako nevhodný.
- Pokud se najde vhodná velikost, Azure Migrate použije výpočty úložiště a sítě. Pak použije nastavení umístění a cenové úrovně pro konečné doporučení velikosti virtuálního počítače.
- Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.

## <a name="confidence-ratings-performance-based"></a>Hodnocení spolehlivosti (na základě výkonu)

Každé posouzení výkonu virtuálního počítače Azure založené na výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti. Hodnocení se pohybuje od jedné (nejnižší) po pět (nejvyšší) hvězdiček. Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti Azure Migrate poskytuje.

- Hodnocení spolehlivosti je přiřazeno k posouzení. Hodnocení je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení.
- Pro určení velikosti na základě výkonu je potřeba posouzení:
    - Data o využití procesoru a paměti RAM.
    - Vstupně-výstupní operace disku a data propustnosti pro každý disk připojený k serveru.
    - I/O sítě pro zpracování velikosti na základě výkonu každého síťového adaptéru připojeného k serveru.

Pokud některá z těchto čísel použití nejsou k dispozici, doporučení pro velikost můžou být nespolehlivá.

> [!NOTE]
> Hodnocení spolehlivosti není přiřazeno pro servery hodnocené pomocí importovaného souboru CSV. Hodnocení se také nevztahují k místnímu vyhodnocení.

### <a name="ratings"></a>Ratings

Tato tabulka ukazuje hodnocení spolehlivosti, které závisí na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0-20% | 1 hvězdička
   21-40% | 2 hvězdičky
   41-60% | 3 hvězdičky
   61-80% | 4 hvězdičky
   81-100% | 5 hvězdiček

### <a name="low-confidence-ratings"></a>Hodnocení nízké důvěry

Tady je několik důvodů, proč hodnocení může získat nízkou spolehlivost:

- Po dobu trvání, pro kterou vytváříte hodnocení, jste neprofilovali své prostředí. Pokud například vytvoříte hodnocení s trváním výkonu nastaveným na jeden den, musíte počkat alespoň den po zahájení zjišťování všech datových bodů, které se mají shromáždit.
- Hodnocení nemůže shromáždit údaje o výkonu některých nebo všech serverů v daném období hodnocení. Pro hodnocení s vysokou mírou jistoty Prosím zajistěte: 
    - Servery jsou napájené po dobu trvání posouzení.
    - Odchozí připojení na portech 443 jsou povolená.
    - Pro servery Hyper-V je dynamická paměť povolená. 
    
    Přepočítejte posouzení, aby se projevily poslední změny míry spolehlivosti.

- Některé servery byly vytvořeny během doby, kdy bylo hodnocení vypočítáno. Předpokládejme například, že jste vytvořili posouzení historie výkonu za poslední měsíc, ale některé servery byly vytvořeny pouze před týdnem. V takovém případě nebudou údaje o výkonu pro nové servery k dispozici po celou dobu trvání a hodnocení spolehlivosti bude nízké.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení menší než pět hvězdiček, doporučujeme, abyste počkali aspoň jeden den, než zařízení profiluje prostředí a pak přepočítá hodnocení. V opačném případě může být velikost na základě výkonu nespolehlivá. V takovém případě doporučujeme, abyste přepnuli posouzení na místní nastavení velikosti.

## <a name="calculate-monthly-costs"></a>Vypočítat měsíční náklady

Po dokončení doporučení pro vyhodnocení velikosti virtuálního počítače Azure v Azure Migrate vypočítá náklady na výpočetní prostředky a úložiště pro po migraci.

- **Náklady na výpočetní** výkon: Azure Migrate využívá doporučená velikost virtuálního počítače Azure a fakturační rozhraní API Azure k výpočtu měsíčních nákladů na server.

    Výpočet vezme v úvahu:
    - Operační systém
    - Software Assurance
    - Rezervované instance
    - Doba provozu virtuálního počítače
    - Umístění
    - Nastavení měny

    Posouzení agreguje náklady napříč všemi servery za účelem výpočtu celkových měsíčních výpočetních nákladů.

- **Náklady na úložiště**: měsíční náklady na úložiště pro server se počítají pomocí agregace měsíčních nákladů na všechny disky, které jsou připojené k serveru.

    Posouzení vypočítá celkové měsíční náklady na úložiště tím, že agreguje náklady na úložiště pro všechny servery. V současné době Výpočet nebere v úvahu nabídky uvedené v nastavení hodnocení.

Náklady se zobrazují v měně určené v nastavení hodnocení.

## <a name="next-steps"></a>Další kroky

[Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení. 

- Seznamte se se spouštěním posouzení pro servery, které jsou spuštěné v prostředí [VMware](./tutorial-discover-vmware.md) a [Hyper-V ](./tutorial-discover-hyper-v.md) a [fyzické servery](./tutorial-discover-physical.md).
- Seznamte se s vyhodnocováním serverů [importovaných pomocí souboru CSV](./tutorial-discover-import.md).
- Přečtěte si o nastavení [Vizualizace závislostí](concepts-dependency-visualization.md).