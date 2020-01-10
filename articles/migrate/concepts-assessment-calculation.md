---
title: Posouzení v Azure Migrate
description: Přečtěte si o hodnoceních v Azure Migrate.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 6950050be3c6fb812a6ade47e98f2d1ed479e61f
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720238"
---
# <a name="about-assessments-in-azure-migrate"></a>O hodnoceních v Azure Migrate

Tento článek popisuje, jak se vypočítávají vyhodnocení v [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool). Vyhledáte vyhodnocení u skupin místních počítačů, abyste zjistili, jestli jsou připravené na migraci na Azure Migrate.

## <a name="how-do-i-run-an-assessment"></a>Návody spustit posouzení?
Můžete spustit posouzení pomocí Azure Migrate: posouzení serveru nebo jiného nástroje Azure nebo nástroje třetí strany. Po vytvoření projektu Azure Migrate přidejte potřebný nástroj. [Další informace] (how-to-add-tool-first-time.md

### <a name="collect-compute-data"></a>Shromažďování výpočetních dat

Údaje o výkonu pro výpočetní nastavení jsou shromažďovány takto:

1. [Zařízení Azure Migrate](migrate-appliance.md) shromažďuje vzorový bod v reálném čase:

    - **virtuálních počítačů VMware*: u virtuálních počítačů vmware bude Azure Migrate zařízení shromažďovat ukázkový bod v reálném čase v intervalu 20 sekund.
    - **Virtuální počítače Hyper-v**: pro virtuální počítače Hyper-v se v každém intervalu 30 sekund shromáždí ukázkový bod v reálném čase.
    - **Fyzické servery**: u fyzických serverů se ukázkový bod v reálném čase shromažďuje každých pět minut. 
    
2. Zařízení zahrne ukázkové body (20 sekund, 30 sekund, pět minut) k vytvoření jednoho datového bodu každých 10 minut. Pokud chcete vytvořit jeden datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek a pak je pošle do Azure.
3. Vyhodnocování serveru ukládá všechny vzorkovací body 10 minut za poslední měsíc.
4. Když vytvoříte posouzení, vyhodnocování serveru identifikuje vhodný datový bod, který se má použít pro správnou velikost, a to na základě hodnot percentilu pro *historii výkonu* a *využití percentilu*.

    - Například pokud je historie výkonu nastavená na jeden týden a procento využití je 95. percentil, vyřadí vyhodnocení serveru ve vzestupném pořadí 10 minutové ukázkové body za poslední týden ve vzestupném pořadí a vybere hodnotu percentilu 95. pro správnou velikost. 
    - Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud vyberete 99 percentil.
    - Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné z nich, měli byste vybrat 99 percentil pro využití percentilu.

5. Tato hodnota je vynásobena faktorem komfortu, aby získala efektivní data o využití výkonu pro jednotlivé metriky (využití procesoru, využití paměti, IOPS disku (čtení a zápis), propustnost disku (čtení a zápis) a propustnost sítě (v/v). zařízení shromažďuje.

Pokud chcete spustit posouzení v rámci posouzení serveru, připravte se na posouzení v místním prostředí a v Azure a nastavte zařízení Azure Migrate pro nepřetržité zjišťování místních počítačů. Po zjištění počítačů je budete shromažďovat do skupin, abyste je vyhodnotili. Pro podrobnější a vysoce spolehlivé hodnocení můžete vizualizovat a mapovat závislosti mezi počítači a zjistit, jak je migrovat.

- Seznamte se se spouštěním posouzení pro [virtuální počítače VMware](tutorial-prepare-vmware.md), [virtuální počítače Hyper-V](tutorial-prepare-hyper-v.md)a [fyzické servery](tutorial-prepare-physical.md).
- Seznamte se s vyhodnocováním serverů [importovaných pomocí souboru CSV](tutorial-assess-import.md).
- Přečtěte si o nastavení [Vizualizace závislostí](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Posouzení v posouzení serveru 

Posouzení, které vytvoříte pomocí Azure Migrate posouzení serveru, jsou snímkem dat v určitém časovém okamžiku. Nástroj pro vyhodnocení serveru nabízí dva typy posouzení.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která vytvářejí doporučení na základě shromážděných údajů o výkonu | Doporučení na velikost virtuálního počítače vychází z dat využití procesoru a paměti.<br/><br/> Doporučení pro typ disku (standardní disková jednotka/SSD nebo Premium – spravované disky) vychází z IOPS a propustnosti místních disků.
**V místním prostředí** | Posouzení, které nepoužívají údaje o výkonu k vytváření doporučení. | Doporučení velikosti virtuálního počítače je založené na velikosti místního virtuálního počítače.<br/><br> Doporučený typ disku je založený na vybraném typu úložiště pro posouzení.

## <a name="collecting-performance-data"></a>Shromažďování údajů o výkonu

Údaje o výkonu jsou shromažďovány takto:

1. [Zařízení Azure Migrate](migrate-appliance.md) shromažďuje vzorový bod v reálném čase:

    - **virtuálních počítačů VMware*: u virtuálních počítačů vmware bude Azure Migrate zařízení shromažďovat ukázkový bod v reálném čase v intervalu 20 sekund.
    - **Virtuální počítače Hyper-v**: pro virtuální počítače Hyper-v se v každém intervalu 30 sekund shromáždí ukázkový bod v reálném čase.
    - **Fyzické servery**: u fyzických serverů se ukázkový bod v reálném čase shromažďuje každých pět minut. 
    
2. Zařízení zahrne ukázkové body (20 sekund, 30 sekund, pět minut) k vytvoření jednoho datového bodu každých 10 minut. Pokud chcete vytvořit jeden datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek a pak je pošle do Azure.
3. Vyhodnocování serveru ukládá všechny vzorkovací body 10 minut za poslední měsíc.
4. Když vytvoříte posouzení, vyhodnocování serveru identifikuje vhodný datový bod, který se má použít pro správnou velikost, a to na základě hodnot percentilu pro *historii výkonu* a *využití percentilu*.

    - Například pokud je historie výkonu nastavená na jeden týden a procento využití je 95. percentil, vyřadí vyhodnocení serveru ve vzestupném pořadí 10 minutové ukázkové body za poslední týden ve vzestupném pořadí a vybere hodnotu percentilu 95. pro správnou velikost. 
    - Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud vyberete 99 percentil.
    - Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné z nich, měli byste vybrat 99 percentil pro využití percentilu.

5. Tato hodnota je vynásobena faktorem komfortu, aby získala efektivní data o využití výkonu pro jednotlivé metriky (využití procesoru, využití paměti, IOPS disku (čtení a zápis), propustnost disku (čtení a zápis) a propustnost sítě (v/v). zařízení shromažďuje.
## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

Tady je postup, který je součástí posouzení v Azure Migrate: Server Assessment

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění, do kterého chcete migrovat.<br/><br/>Posouzení serveru aktuálně podporuje tyto cílové oblasti Azure: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed Indie, Střed USA, Čína – východ, Čína – sever, Východní Asie, Východní USA, východní USA 2, Německo – střed, Německo Severovýchod, Japonsko – východ, Japonsko – západ, Korea – jih, Jižní Korea, Střed USA – sever, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, středozápadní USA, západní Evropa, Západní Indie, Západní USA a západ USA 2.
**Cílový disk úložiště: Změna velikosti** | Typ disků, které se mají použít pro úložiště v Azure. <br/><br/> Zadejte cílový disk úložiště jako spravovaný na úrovni Premium, Standard SSD spravovaná nebo standardní pevný disk.<br/><br/> 
**Cílový disk úložiště: Změna velikosti na základě výkonu** | Zadejte typ cílového disku úložiště jako automatické, spravované na úrovni Premium, standardně spravované HDD nebo standardní SSD spravovaná.<br/><br/> **Automaticky**: doporučení na disku vychází z údajů o výkonu disků (vstupně-výstupní operace za sekundu (IOPS) a propustnost). <br/><br/>**Premium/Standard**: posouzení doporučuje SKU disku v rámci vybraného typu úložiště.<br/><br/> Pokud chcete dosáhnout smlouvy SLA pro virtuální počítače s jednou instancí 99,9%, zvažte použití služeb Premium Managed disks. Tím se zajistí, že se všechny disky v posouzení doporučují jako disky spravované na úrovni Premium.<br/><br/> Azure Migrate podporuje pro posouzení migrace jenom spravované disky.
**Rezervované instance (RIs)** | Zadejte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure, aby se odhady nákladů v rámci posouzení převzaly v úvahu slevy na vyhrazené platformě.<br/><br/> Služby RIs se momentálně podporují jenom pro nabídky s průběžnými platbami v Azure Migrate.
**Kritéria změny velikosti** | Slouží k napravení velikosti virtuálního počítače v Azure.<br/><br/> Použijte změnu velikosti nebo určení velikosti na základě výkonu.
**Historie výkonu** | Používá se při změně velikosti na základě výkonu. Zadejte dobu trvání použitou při vyhodnocení údajů o výkonu.
**Percentilové využití** | Používá se při změně velikosti na základě výkonu. Určuje hodnotu percentilu pro vzorek výkonu, který se má použít pro správnou velikost. 
**Řada virtuálních počítačů** | Zadejte řadu virtuálních počítačů Azure, které chcete zvážit pro správnou velikost. Pokud například nemáte produkční prostředí, které potřebuje pro virtuální počítače řady A-Series v Azure, můžete z tohoto seznamu nebo řady vyřadit řady.
**Faktor komfortu** | Vyrovnávací paměť použitá během posouzení Použito na data o využití počítače pro virtuální počítače (CPU, paměť, disk a síť). 
Účty IT mají problémy, jako je sezónní využití, krátká historie výkonu a pravděpodobná zvýšení využití v budoucnu.<br/><br/> Například virtuální počítač s 10 jádry s 20% využitím obvykle vede k virtuálnímu počítači se dvěma jádry. Výsledkem je faktor komfortu 2.0 x, ale výsledkem je virtuální počítač se čtyřmi jádry.
**Nabídka** | Zobrazí [nabídku Azure](https://azure.microsoft.com/support/legal/offer-details/) , do které jste zaregistrovaní. Vyhodnocení serveru odhadá náklady odpovídajícím způsobem.
**Měna** | Fakturační měna vašeho účtu.
**Sleva (%)** | Zobrazí seznam slev specifických pro předplatné, které obdržíte nad nabídkou Azure. Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud virtuální počítače Azure nebudou běžet 24 hodin denně, 7 dní v týdnu, můžete zadat dobu trvání (dny za měsíc a hodiny za den), které budou spuštěny. Odhad nákladů se zpracovává odpovídajícím způsobem.<br/><br/> Výchozí hodnota je 31 dní za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je nastavená hodnota Ano (výchozí nastavení), ceny jiné než Windows Azure se považují za virtuální počítače s Windows.

[Projděte si osvědčené postupy](best-practices-assessment.md) pro vytváření posouzení pomocí posouzení serveru.

## <a name="how-are-assessments-calculated"></a>Jak se počítají posouzení? 

Posouzení v Azure Migrate: posouzení serveru se počítá pomocí metadat shromážděných v místních počítačích. Pokud spustíte posouzení na počítačích importovaných pomocí. Soubor CSV vám poskytne metadata pro výpočet. K výpočtům dochází ve třech fázích:

1. **Výpočet připravenosti na Azure**: Vyhodnoťte, jestli jsou počítače vhodné pro migraci do Azure.
2. **Výpočet doporučení pro velikost**: odhad výpočetních prostředků, úložiště a velikosti sítě. 
2. **Výpočet měsíčních nákladů**: Vypočítejte Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz počítačů v Azure po migraci.

Výpočty jsou v pořadí a počítačový Server se přesune do pozdější fáze, jenom když projde předchozí. Pokud třeba server neprojde připravenost Azure, je označený jako nevhodný pro Azure a u tohoto serveru se neprovádí Změna velikosti a nákladů.



## <a name="calculate-readiness"></a>Vypočítat připravenost

Ne všechny počítače jsou vhodné ke spuštění v Azure. Posouzení serveru vyhodnocuje každý místní počítač a přiřadí mu kategorii připravenosti. 
- **Připraveno pro Azure**: počítač se dá migrovat tak, jak je, do Azure bez jakýchkoli změn. Spustí se v Azure s plnou podporou Azure.
- **Podmíněně připravené pro Azure**: počítač se může v Azure spustit, ale nemusí mít plnou podporu Azure. Například počítač, na kterém běží starší verze Windows serveru, se v Azure nepodporuje. Než tyto počítače migrujete do Azure, musíte být opatrní. Podle pokynů k nápravě navrhovaných v posouzení Opravte problémy s připraveností.
- **Nepřipraveno pro Azure**: počítač se v Azure nespustí. Pokud je například disk místního počítače větší než 64 – TBs, nejde ho hostovat v Azure. Pokud chcete problém vyřešit před migrací, postupujte podle pokynů k nápravě. 
- **Připravenost je neznámá**: Azure Migrate nedokázal určit připravenost počítače z důvodu nedostatečných metadat.

Pro výpočet připravenosti vyhodnocování serveru kontroluje vlastnosti počítače a nastavení operačního systému shrnuté v následujících tabulkách. 

### <a name="machine-properties"></a>Vlastnosti počítače

Posouzení serveru kontroluje následující vlastnosti místního virtuálního počítače, aby zjistil, jestli je možné ho spustit v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spouštění systému BIOS, nikoli UEFI. | Podmíněně připravený, pokud je typ spouštění UEFI.
**Jader** | Počet jader v počítačích musí být větší nebo roven maximálnímu počtu jader (128) podporovanému pro virtuální počítač Azure.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využité jádra k porovnání. Pokud je v nastavení hodnocení určen faktor komfortu, je počet využitých jader vynásoben faktorem pohodlí.<br/><br/> Pokud není k dispozici žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraveno, pokud je omezení menší nebo rovno.
**Paměť** | Velikost paměti počítače musí být menší nebo rovna maximální paměti (3892 GB [GB] v řadě Azure M Standard_M128m&nbsp;<sup>2</sup>), která je povolená pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využití paměti za využitou paměť k porovnání. Pokud je určen faktor komfortu, vyhodnotí se využitá paměť podle faktoru pohodlí.<br/><br/> Pokud není k dispozici žádná historie, přidělená paměť se použije bez použití faktoru pohodlí.<br/><br/> | V rámci omezení je připravený.
**Disk úložiště** | Přidělená velikost disku musí být 32 TB nebo méně. I když Azure podporuje disky 64 – TB s SSD úrovně Ultra disky, Azure Migrate: posouzení serveru aktuálně kontroluje 32 TB jako omezení velikosti disku, protože ještě nepodporuje SSD úrovně Ultra. <br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku s operačním systémem. | V rámci omezení je připravený.
**Sítě** | Počítač musí mít k tomuto počítači připojenou 32 nebo méně síťových rozhraní (nic). | V rámci omezení je připravený.

### <a name="guest-operating-system"></a>Hostovaný operační systém
Posouzení serveru spolu s vlastnostmi virtuálního počítače podívá na hostovaný operační systém počítačů a určí, jestli se dá spustit na Azure.

> [!NOTE]
> U virtuálních počítačů VMware používá posouzení serveru operační systém, který je zadaný pro virtuální počítač v vCenter Server pro zpracování analýzy hostovaného operačního systému. Pro virtuální počítače se systémem Linux běžící na VMware aktuálně neidentifikuje přesnou verzi jádra hostovaného operačního systému.

Následující logiku používá posouzení serveru k identifikaci připravenosti na Azure podle operačního systému.

**Operační systém** | **Podrobnosti** | **Stav připravenosti na Azure**
--- | --- | ---
Windows Server 2016 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 & všechny SPs | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 se všemi službami SPs | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32 a 64-bit) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Tyto operační systémy prošly datem ukončení podpory a potřebují pro podporu v Azure [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) . | Podmíněně připravené pro Azure. Před migrací do Azure zvažte možnost upgradovat operační systém.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Tyto operační systémy prošly datem ukončení podpory. Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme upgradovat operační systém.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu [jenom pro předplatné sady Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Připraveno pro Azure s podmínkou
Stolní počítač s Windows 10 pro | Azure podporuje [práva hostování s více klienty.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Tyto operační systémy prošly datem ukončení podpory. Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme upgradovat operační systém.
Linux | Azure tyto [operační systémy pro Linux](../virtual-machines/linux/endorsed-distros.md)schválí. Ostatní operační systémy Linux se můžou v Azure spustit, ale před migrací do Azure doporučujeme upgradovat operační systém na schválenou verzi. | Je připravený pro Azure, pokud je verze schválená.<br/><br/>Podmíněně připravený, pokud verze není schválená.
Jiné operační systémy<br/><br/> Například Oracle Solaris, Apple Mac OS atd., FreeBSD atd. | Azure tyto operační systémy neschvaluje. Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. | Podmíněně připravené pro Azure. Před migrací do Azure doporučujeme nainstalovat podporovaný operační systém.  
Operační systém určený jako **jiný** v vCenter Server | Azure Migrate nemůže v tomto případě identifikovat operační systém. | Neznámá připravenost. Ujistěte se, že operační systém běžící v rámci virtuálního počítače je podporovaný v Azure.
32 – bitové operační systémy | Počítač se může v Azure spustit, ale Azure možná neposkytne plnou podporu. | Podmíněně připravené pro Azure. Před migrací do Azure zvažte možnost upgradovat operační systém počítače z 32 operačního systému na 64.

## <a name="calculate-sizing-as-is-on-premises"></a>Vypočítat velikost (jako místní)

Jakmile je počítač označený jako připravený pro Azure, vyhodnocování serveru vytvoří doporučení pro změnu velikosti, která identifikují virtuální počítač Azure a diskovou jednotku disku. Pokud používáte místní velikost, vyhodnocování serveru nebere v úvahu historii výkonu virtuálních počítačů a disků.

**Výpočet velikosti**: PŘIDĚLUJE SKU virtuálního počítače Azure na základě velikosti přidělené místně.
**Velikost úložiště a disku**: posouzení serveru vypadá na typu úložiště, který je určený ve vlastnostech posouzení (standardní pevný disk/SSD/Premium), a podle toho doporučuje typ disku. Výchozí typ úložiště je prémiové disky.
**Změna velikosti sítě**: vyhodnocování serveru bere síťový adaptér na místním počítači.


## <a name="calculate-sizing-performance-based"></a>Vypočítat velikost (na základě výkonu)

Když je počítač označený jako připravený pro Azure, pokud použijete změnu velikosti na základě výkonu, vyhodnocování serveru provede doporučení pro změnu velikosti následujícím způsobem:

- Posouzení serveru považuje historii výkonu počítače za účelem identifikace velikosti virtuálního počítače a typu disku v Azure.
- Pokud byly servery importovány pomocí souboru CSV, budou použity zadané hodnoty. Tato metoda je užitečná hlavně v případě, že jste přestali mít přidělený místní počítač, protože využití je ve skutečnosti nízké a vy chcete ušetřit náklady v Azure na správnou velikost. 
- Pokud nechcete data o výkonu používat, obnovte kritéria změny velikosti na místní, jak je popsáno v předchozí části.

### <a name="calculate-storage-sizing"></a>Výpočet velikosti úložiště

V případě velikosti úložiště se Azure Migrate pokusí mapovat všechny disky připojené k počítači na disk v Azure a funguje následujícím způsobem:

1. Posouzení serveru přidá IOPS čtení a zápisu disku, aby bylo možné získat celkový počet požadovaných IOPS. Podobně přidá hodnoty propustnosti čtení a zápisu k získání celkové propustnosti každého disku.
2. Pokud jste zadali typ úložiště jako automatický, vychází na základě platných hodnot IOPS a propustnosti serveru k vyhodnocení, jestli by měl být disk v Azure namapovaný na standardní HDD, Standard SSD nebo disk Premium. Pokud je typ úložiště nastavený na HDD úrovně Standard/SSD/Premium, vyhodnocování serveru se pokusí najít SKU disku v rámci vybraného typu úložiště (disky HDD úrovně Standard/SSD/Premium).
3. Disky jsou vybrané takto:
    - Pokud server Assessment nenalezne disk s požadovanými vstupně-výstupními operacemi a propustností, označí počítač jako nevhodný pro Azure.
    - Pokud posouzení serveru najde sadu vhodných disků, vybere disky, které podporují umístění zadané v nastavení posouzení.
    - Pokud je k dispozici více opravňujících disků, vybírá Server Assessment disk s nejnižšími náklady.
    - Pokud nejsou k dispozici údaje o výkonu pro jakýkoli disk, k nalezení standardního disku SSD v Azure se použije konfigurační data disku (velikost disku).

### <a name="calculate-network-sizing"></a>Výpočet velikosti sítě

Vyhodnocování serveru se pokouší najít virtuální počítač Azure, který může podporovat počet síťových adaptérů připojených k místnímu počítači a výkon vyžadovaný těmito síťovými adaptéry.
- Aby se dosáhlo efektivního výkonu sítě v místním VIRTUÁLNÍm počítači, vyhodnocování serveru agreguje data přenášená za sekundu (MB/s) na všech síťových adaptérech a uplatní faktor komfortu. Pomocí tohoto čísla vyhledá virtuální počítač Azure, který může podporovat požadovaný výkon sítě.
- Společně s výkonem sítě taky posouzení serveru bere v úvahu, jestli virtuální počítač Azure může podporovat požadovaný počet síťových adaptérů.
- Pokud nejsou k dispozici žádná data o výkonu sítě, posouzení serveru považuje pouze počet síťových adaptérů pro velikost virtuálního počítače.


### <a name="calculate-compute-sizing"></a>Vypočítat výpočetní velikost

Jakmile vypočítá požadavky na úložiště a síť, vyhodnotí se požadavky na procesor a paměť, aby se v Azure vyhledala vhodná velikost virtuálního počítače.
- Azure Migrate podíváme se na efektivní využité jádra a paměť a najděte vhodnou velikost virtuálního počítače v Azure.
- Pokud se nenajde žádná vhodná velikost, je počítač označený jako nevhodný pro Azure.
- Pokud se najde vhodná velikost, Azure Migrate použije výpočty úložiště a sítě. Pak použije nastavení umístění a cenové úrovně pro konečné doporučení velikosti virtuálního počítače.
- Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.


### <a name="calculate-confidence-ratings"></a>Vypočítat hodnocení spolehlivosti

Každé posouzení na základě výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti, které je v rozsahu od jednoho (nejnižší) po pět hvězdiček (nejvyšší).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti není použitelné pro vyhodnocení *místních* hodnot.
- Pro určení velikosti na základě výkonu, požadavky na vyhodnocení serveru:
    - Data o využití procesoru a paměti virtuálního počítače.
    - IOPS disku a data propustnosti pro každý disk připojený k virtuálnímu počítači.
    - I/O sítě pro zpracování velikosti pro každý síťový adaptér připojený k virtuálnímu počítači na základě výkonu.

   Pokud některá z těchto čísel využití nejsou v vCenter Server k dispozici, doporučení velikosti nemusí být spolehlivé.

V závislosti na procentu dostupných datových bodů se hodnocení spolehlivosti pro posouzení provede následujícím způsobem.

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0-20% | 1 hvězdička
   21-40% | 2 hvězdičky
   41-60% | 3 hvězdičky
   61-80% | 4 hvězdičky
   81-100% | 5 hvězdiček

> [!NOTE]
> Hodnocení spolehlivosti nejsou přiřazena k posouzení serverů importovaných pomocí. Soubor CSV do Azure Migrate. 

#### <a name="low-confidence-ratings"></a>Hodnocení nízké důvěry

Tady je několik důvodů, proč hodnocení může získat nízkou spolehlivost:

- Nevytvořili jste profil svého prostředí po dobu, po kterou vytváříte posouzení. Pokud například vytvoříte hodnocení s trváním výkonu nastaveným na jeden den, musíte po spuštění zjišťování pro všechny datové body, které se mají shromáždit, počkat aspoň jeden den.
- Některé virtuální počítače se vypnuly během období, pro které se hodnocení vypočítalo. Pokud jsou některé virtuální počítače po určitou dobu vypnuté, vyhodnocování serveru nemůže shromažďovat data o výkonu pro tuto dobu.
- Některé virtuální počítače byly vytvořeny během období, pro které bylo hodnocení vypočítáno. Pokud jste například vytvořili vyhodnocení pro historii výkonu za poslední měsíc, ale některé virtuální počítače byly vytvořeny pouze před týdnem, historie výkonu nových virtuálních počítačů nebude po celou dobu trvání k dispozici.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení menší než pět hvězdiček, doporučujeme, abyste počkali aspoň jeden den, než zařízení profiluje prostředí, a pak posouzení přepočítá. Pokud to neuděláte, velikost na základě výkonu nemusí být spolehlivá. V takovém případě doporučujeme, abyste přepnuli posouzení na místní nastavení velikosti.

## <a name="calculate-monthly-costs"></a>Vypočítat měsíční náklady

Až se dokončí Změna velikosti doporučení, Azure Migrate vypočítá náklady na výpočetní prostředky a úložiště pro po migraci.

- **Náklady na výpočetní**výkon: používá se doporučená velikost virtuálního počítače Azure, Azure Migrate pro výpočet měsíčních nákladů na virtuální počítač používá rozhraní API pro fakturaci.
    - Výpočet vezme v úvahu operační systém, Software Assurance, rezervované instance, dobu provozu virtuálního počítače, umístění a nastavení měny.
    - Agreguje náklady napříč všemi počítači za účelem výpočtu celkových měsíčních výpočetních nákladů.
- **Náklady na úložiště**: měsíční náklady na úložiště pro počítač se počítají pomocí agregace měsíčních nákladů na všechny disky připojené k počítači, a to takto:
    - Posouzení serveru vypočítá celkové měsíční náklady na úložiště tím, že agreguje náklady na úložiště pro všechny počítače.
    - V současné době Výpočet nebere v úvahu nabídky uvedené v nastavení hodnocení.

Náklady se zobrazují v měně určené v nastavení hodnocení.


## <a name="next-steps"></a>Další kroky

[Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení. 
