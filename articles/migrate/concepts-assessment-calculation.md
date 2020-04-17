---
title: Hodnocení v azure migrate server hodnocení
description: Informace o hodnoceních v Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d1f32eea0ec6a8a4877fd1dc134344cfe68dcaba
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537759"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Hodnocení v Azure Migrate:Vyhodnocení serveru

Tento článek obsahuje přehled hodnocení v nástroji [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Nástroj Vyhodnocení serveru může vyhodnotit místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure.

## <a name="whats-an-assessment"></a>Co je to hodnocení?

Posouzení pomocí nástroje Hodnocení serveru měří připravenost a odhaduje dopad migrace místních serverů do Azure.

> [!NOTE]
> Ve službě Azure Government zkontrolujte podporovaná umístění [cílového](migrate-support-matrix.md#supported-geographies-azure-government) hodnocení. Všimněte si, že doporučení velikosti virtuálních počítače v hodnocení bude používat řady virtuálních počítače speciálně pro oblasti Government Cloud. [Další informace](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) o typech virtuálních počítačů.

## <a name="types-of-assessments"></a>Druhy hodnocení

Hodnocení, která vytvoříte pomocí vyhodnocení serveru, jsou snímek dat v čase. Vyhodnocení serveru poskytuje dva typy hodnocení.

**Typ posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení, která doporučení vyhovují na základě shromážděných údajů o výkonnosti | Doporučení velikosti virtuálního počítače je založené na datech využití procesoru a paměti.<br/><br/> Doporučení typu disku (standardní disky HDD/SSD nebo disky spravované s prémií) je založeno na iOPS a propustnosti místních disků.
**As-je v místním prostředí** | Hodnocení, která nepoužívají údaje o výkonu k doporučení. | Doporučení velikosti virtuálního počítače je založené na velikosti místního virtuálního počítače<br/><br> Doporučený typ disku je založen na vybraném typu úložiště pro posouzení.

## <a name="how-do-i-run-an-assessment"></a>Jak spustím hodnocení?

Existuje několik způsobů, jak spustit hodnocení:

- Vyhodnoťte počítače pomocí metadat serveru shromážděných zjednodušenou službou Azure Migrate. Zařízení zjišťuje místní počítače a odesílá data metadat a výkonu počítače do Migrace Azure.
- Vyhodnoťte počítače pomocí metadat serveru, která jsou importována ve formátu CSV (csv) oddělených čárkami.

## <a name="how-do-i-assess-with-the-appliance"></a>Jak hodnotím s přístrojem?

Pokud nasazujete zařízení Azure Migrate ke zjišťování místních serverů, postupujte takto:

1. Azure a místní prostředí nastavíte tak, aby fungovalo s vyhodnocením serveru.
2. Pro první hodnocení vytvoříte projekt Azure a přidáte do něj nástroj Hodnocení serveru.
3. Nasadíte zjednodušené zařízení Azure Migrate. Zařízení průběžně zjišťuje místní počítače a odesílá metadata a údaje o výkonu počítače do Migrace Azure. Zařízení se nasazuje jako virtuální počítač nebo fyzický počítač. Není třeba instalovat nic na počítačích, které chcete posoudit.
4. Po spuštění zjišťování stroje můžete shromáždit stroje, které chcete posoudit do skupiny, a spustit hodnocení pro skupinu.

Můžete sledovat naše výukové programy pro [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md)nebo [fyzické servery](tutorial-prepare-physical.md) vyzkoušet tyto kroky.

## <a name="how-do-i-assess-with-imported-data"></a>Jak hodnotím s importovanými daty?

Pokud vyhodnocujete servery pomocí souboru CSV, nepotřebujete zařízení. Místo toho provést následující kroky:

1. Azure nastavíte tak, aby fungoval s vyhodnocením serveru.
2. Pro první hodnocení vytvoříte projekt Azure a přidáte do něj nástroj Hodnocení serveru.
3. Stáhnete šablonu CSV a přidáte do ní data serveru.
4. Šablonu importujete do vyhodnocení serveru.
5. Zjistíte servery přidané s importem, shromáždíte se do skupiny a spustíte hodnocení pro skupinu.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data přístroj shromažďuje?

Pokud používáte zařízení Azure Migrate pro posouzení, přečtěte si informace o metadatech a údajích o výkonu shromážděných pro [společnosti VMware](migrate-appliance.md#collected-data---vmware) a [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak zařízení vypočítává údaje o výkonu?

Pokud zařízení používáte k zjišťování, data o výkonu pro nastavení výpočetních prostředků se shromažďují následujícím způsobem:

1. Přístroj shromažďuje bod vzorku v reálném čase:

    - **Virtuální zařízení VMware**: Zařízení shromažďuje bod vzorkování v reálném čase v intervalu 20 sekund.
    - **Virtuální aplikace Hyper-V**: Bod vzorkování v reálném čase se shromažďuje v intervalu 30 sekund.
    - **Fyzické servery**: Bod ukázky v reálném čase se shromažďuje v intervalu pěti minut. 
    
2. Zařízení shrnuje vzorkovací body (20 sekund, 30 sekund, pět minut) a každých 10 minut vytvoří jeden datový bod. Chcete-li vytvořit jeden bod, zařízení vybere hodnotu špičky ze všech vzorků a odešle ji do Azure.
3. Vyhodnocení serveru ukládá všechny ukázkové body za poslední měsíc za 10 minut.
4. Při vytváření hodnocení určuje vyhodnocení serveru příslušný datový bod, který se má použít pro správnou velikost, na základě hodnot percentilu pro *historii výkonu* a *využití percentilu*.

    - Pokud je například historie výkonu nastavena na jeden týden a využití percentilu je 95 percentil, vyhodnocení serveru seřadí 10minutové ukázkové body za poslední týden ve vzestupném pořadí a vybere hodnotu 95 percentilu pro správné nastavení velikosti. 
    - Hodnota 95. percentilu zajišťuje, že ignorujete všechny odlehlé hodnoty, které mohou být zahrnuty, pokud vyberete 99.
    - Pokud chcete vybrat využití ve špičce pro období a nechcete zmeškat žádné odlehlé hodnoty, měli byste vybrat 99 percentil pro využití percentilu.

5. Tato hodnota je vynásobena faktorem pohodlí získat efektivní využití výkonu data pro každou metriku (využití procesoru, využití paměti, videa videa disků (čtení a zápis), propustnost disku (čtení a zápis) a propustnost sítě (dovnitř a ven), které zařízení shromažďuje.



## <a name="how-are-assessments-calculated"></a>Jak se hodnocení počítají? 

Hodnocení v vyhodnocení serveru se vypočítají pomocí metadat/údajů o výkonu pro místní počítače. Pokud nasadíte zařízení Azure Migrate, pak posouzení pomocí dat shromážděných zařízení. Pokud spustíte hodnocení pro počítače importované pomocí . CSV, zadáte metadata pro výpočet. Výpočty probíhají ve třech fázích:

1. **Výpočet připravenosti Azure**: Vyhodnoťte, jestli jsou počítače vhodné pro migraci do Azure.
2. **Výpočet doporučení pro velikost**: Odhadujte výpočetní, úložnou a síťovou velikost. 
2. **Výpočet měsíčních nákladů**: Vypočítejte odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění počítačů v Azure po migraci.

Výpočty jsou v pořádku a strojový server se přesune do pozdější fáze pouze v případě, že projde předchozí fází. Například pokud server selže připravenost Azure, je označen jako nevhodné pro Azure a velikosti a náklady se neprovádí pro tento server.


## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

Zde je to, co zahrnuto v hodnocení v hodnocení serveru.

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění, do kterého chcete migrovat. Server Assessment aktuálně podporuje tyto cílové oblasti Azure:<br/><br/> Austrálie – východ, Austrálie – jihovýchod, Brazílie jih, Kanada – střed, Kanada – východ, střední Indie, střední USA, Čína – východ, Čína – sever, východní Asie, východní USA, východní USA2, Německo – střed, Německo – severovýchod, Japonsko – východ, Japonsko – západ, Korea – jih, severní střed USA, severní Evropa, jižní střed USA, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, americká vláda Arizona, USA– Usa– Usa– Usa– Usa– Usa– Usa – Guvernér Usa– Usa– Usa – Guvernér , Západní střed USA, Západní Evropa, Západní Indie, Západní USA a Západní US2.
*Cílový disk úložiště (velikost stejně jako velikost)** | Typ disků, které se mají používat pro úložiště v Azure. <br/><br/> Zadejte cílový disk úložiště jako spravovaný prémiový, standardní spravovaný disk SSD nebo standardní pevný disk.
**Cílový disk úložiště (velikost založená na výkonu)** | Zadejte typ cílového úložného disku jako automatického, prémiově spravovaného, standardního spravovaného pevného disku nebo standardního spravovaného ssd disku.<br/><br/> **Automatické**: Doporučení disku je založeno na údajích o výkonu disků (vstupní ch a výstupní operace za sekundu (IOPS) a propustnost).<br/><br/>**Premium/standard**: Hodnocení doporučuje disk ovou položku v rámci vybraného typu úložiště.<br/><br/> Pokud chcete dosáhnout jedné instance SLA virtuálního počítače 99,9 %, zvažte použití prémiových spravovaných disků. Tím je zajištěno, že všechny disky v hodnocení jsou doporučeny jako disky spravované prémií.<br/><br/> Azure Migrate podporuje pro posouzení migrace jenom spravované disky.
**Rezervované instance (RIs)** | Zadejte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure, aby odhady nákladů v posouzení braly v úvahu slevy RI.<br/><br/> ReIs jsou v současné době podporované jenom pro nabídky s průběžným platbami v Azure Migrate.
**Kritéria pro velikost** | Používá se k správné velikosti virtuálního počítače v Azure.<br/><br/> Použití velikosti podle velikosti nebo velikosti založené na výkonu.
**Historie výkonu** | Používá se s velikosti založené na výkonu. Určete dobu trvání použitou při vyhodnocování dat o výkonu.
**Percentilové využití** | Používá se s velikosti založené na výkonu. Určuje hodnotu percentilu vzorku výkonu, který má být použit pro správné velikosti. 
**Řada virtuálních počítačů** | Zadejte řadu virtuálních počítačových virtuálních počítače Azure, které chcete zvážit pro správné velikosti. Například pokud nemáte produkční prostředí, které potřebuje virtuální počítače řady A v Azure, můžete vyloučit řady A ze seznamu nebo řady.
**Faktor komfortu** | Vyrovnávací paměť použitá při hodnocení. Použito nad daty využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Zodpovídá problémy, jako je sezónní využití, krátká historie výkonu a pravděpodobné zvýšení budoucího využití.<br/><br/> Například 10jádrový virtuální virtuální ms s 20 % využití obvykle vede k virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu provozu se dvěma jádry. S faktorem pohodlí 2,0x je výsledkem čtyřjádrový virtuální virtuální mon....
**Nabídka** | Zobrazí [nabídku Azure,](https://azure.microsoft.com/support/legal/offer-details/) ve které jste zaregistrovaní. Vyhodnocení serveru odpovídajícím způsobem odhadne náklady.
**Měna** | Fakturace měny pro váš účet.
**Sleva (%)** | Zobrazí seznam všech slev specifických pro předplatné, které obdržíte nad nabídku Azure. Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud virtuální počítače Azure neběží 24 hodin denně, 7 dní v týdnu, můžete určit dobu trvání (dny za měsíc a hodiny za den), které se spustí. Odhady nákladů jsou zpracovány odpovídajícím způsobem.<br/><br/> Výchozí hodnota je 31 dní v měsíci a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, zda máte jistotu softwaru a máte nárok na [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je nastavena na Ano (výchozí nastavení), ceny než Windows Azure jsou považovány za pro virtuální počítače s Windows.

[Projděte si osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení pomocí vyhodnocení serveru.


## <a name="calculate-readiness"></a>Vypočítat připravenost

Ne všechny počítače jsou vhodné pro spuštění v Azure. Vyhodnocení serveru vyhodnotí každý místní počítač a přiřadí mu kategorii připravenosti. 
- **Připraveno pro Azure**: Počítač lze migrovat jako je do Azure bez jakýchkoli změn. Spustí se v Azure s plnou podporou Azure.
- **Podmíněně připravený pro Azure**: Počítač se může spustit v Azure, ale nemusí mít plnou podporu Azure. Například počítač, který používá starší verzi Windows Serveru, není v Azure podporovaný. Před migrací těchto počítačů do Azure musíte být opatrní. Postupujte podle pokynů pro nápravu navržených v posouzení, abyste opravili problémy s připraveností.
- **Není připraven pro Azure**: počítač se nespustí v Azure. Například pokud je místní počítačový disk více než 64-TBs, nemůže být hostovaný v Azure. Chcete-li problém vyřešit před migrací, postupujte podle pokynů pro nápravu. 
- **Připravenost neznámý**: Azure Migrate nelze určit připravenost počítače, z důvodu nedostatečné metadata.

Chcete-li vypočítat připravenost, vyhodnocení serveru zkontroluje vlastnosti počítače a nastavení operačního systému shrnuté v následujících tabulkách. 

### <a name="machine-properties"></a>Vlastnosti stroje

Vyhodnocení serveru zkontroluje následující vlastnosti místního virtuálního počítače k určení, zda může běžet v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti Azure**
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spouštění systému BIOS, nikoli uefi. | Podmíněně připraven, pokud je typ spouštění UEFI.
**Cores** | Počet jader v počítačích musí být rovna nebo menší než maximální počet jader (128) podporovaných pro virtuální počítač Azure.<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využitá jádra pro porovnání. Pokud je v nastavení hodnocení specifikován faktor komfortu, počet využitých jader se vynásobí faktorem komfortu.<br/><br/> Pokud neexistuje žádná historie výkonu, Azure Migrate používá přidělená jádra bez použití faktoru pohodlí. | Připraven, pokud je menší nebo rovno limitům.
**Paměti** | Velikost paměti počítače musí být rovna nebo menší než maximální paměti (3892 gigabajtů [GB] v řadě Azure M Standard_M128m&nbsp;<sup>2</sup>) povolené pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud je k dispozici historie výkonu, Azure Migrate považuje využité paměti pro porovnání. Je-li zadán komfortní faktor, využitá paměť se vynásobí faktorem komfortu.<br/><br/> Pokud neexistuje žádná historie, přidělená paměť se používá bez použití faktoru pohodlí.<br/><br/> | Připraven, pokud v mezích.
**Disk úložiště** | Přidělená velikost disku musí být 32 TB nebo méně. Přestože Azure podporuje disky o velikosti 64 TB s disky Ultra SSD, Azure Migrate: Server Assessment aktuálně kontroluje 32 TB jako omezení velikosti disku, protože ještě nepodporuje Ultra SSD. <br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku operačního systému. | Připraven, pokud v mezích.
**Sítě** | K počítači musí být připojeno 32 nebo méně síťových rozhraní. | Připraven, pokud v mezích.

### <a name="guest-operating-system"></a>Hostovaný operační systém
Spolu s vlastnostmi virtuálního počítače, vyhodnocení serveru se dívá na hostovanéoperační systém počítačů k určení, zda lze spustit v Azure.

> [!NOTE]
> Pro virtuální servery VMware používá vyhodnocení serveru operační systém určený pro virtuální ho virtuální ho schod v serveru vCenter ke zpracování analýzy hostovaného operačního systému. U virtuálních počítačů s Linuxem spuštěných na vmware aktuálně neidentifikuje přesnou verzi jádra hostovaného operačního systému.

Následující logika se používá server assessment k identifikaci připravenosti Azure na základě operačního systému.

**Operační systém** | **Podrobnosti** | **Stav připravenosti Azure**
--- | --- | ---
Windows Server 2016 & všechny sp | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 & všechny sp | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 & všechny sp | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 se všemi sp | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32bitový a 64bitový) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Tyto operační systémy prošly datem ukončení podpory a potřebují [vlastní smlouvu o podpoře (CSA)](https://aka.ms/WSosstatement) pro podporu v Azure. | Podmíněně připraven pro Azure. Před migrací do Azure zvažte upgrade operačního systému.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Tyto operační systémy prošly datem ukončení podpory. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního serveru. | Podmíněně připraven pro Azure. Doporučujeme upgradovat operační systém před migrací do Azure.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu [jenom s předplatným Visual Studia.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Připraveno pro Azure s podmínkou
Plocha Windows 10 Pro | Azure poskytuje podporu s [víceklientských práv hostingu.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Tyto operační systémy prošly datem ukončení podpory. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního serveru. | Podmíněně připraven pro Azure. Doporučujeme upgradovat operační systém před migrací do Azure.
Linux | Azure podporuje tyto [operační systémy Linuxu](../virtual-machines/linux/endorsed-distros.md). Jiné operační systémy Linuxu se mohou spustit v Azure, ale doporučujeme upgradovat operační systém na schválenou verzi před migrací do Azure. | Připraveno pro Azure, pokud je verze schválena.<br/><br/>Podmíněně připraven, pokud verze není schválena.
Ostatní operační systémy<br/><br/> Například Oracle Solaris, Apple macOS atd., FreeBSD atd. | Azure tyto operační systémy neschvaluje. Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního serveru. | Podmíněně připraven pro Azure. Doporučujeme nainstalovat podporovaný operační systém před migrací do Azure.  
Operační systém byl zadán jako **Jiný** na serveru vCenter | Azure Migrate nemůže identifikovat operační systém v tomto případě. | Neznámá připravenost. Ujistěte se, že operační systém spuštěný uvnitř virtuálního počítače je podporovaný v Azure.
32bitové operační systémy | Počítač se může spustit v Azure, ale Azure nemusí poskytovat plnou podporu. | Podmíněně připraven pro Azure. Před migrací do Azure zvažte upgrade operačního systému počítače z 32bitového operačního systému na 64bitový operační systém.

## <a name="calculating-sizing"></a>Výpočet velikosti


Po zařízení je označenjako připravený pro Azure, vyhodnocení serveru umožňuje doporučení pro měření velikosti k identifikaci virtuálního počítače Azure a diskové skladové položky. Výpočty velikosti závisí na tom, zda používáte místní velikosti jako místní, nebo na velikosti založené na výkonu.

### <a name="calculate-sizing-as-is-on-premises"></a>Výpočet velikosti (místní)

 Pokud používáte jako místní velikosti, hodnocení serveru nebere v úvahu historii výkonu virtuálních počítačů a disků.

- **Velikost výpočetních prostředků**: Přiděluje skladovou jednotku virtuálního počítače Azure na základě velikosti přidělené místně.
- **Velikost úložiště/disku**: Vyhodnocení serveru se zabývá typem úložiště zadaným ve vlastnostech hodnocení (standardní HDD/SSD/premium) a odpovídajícím způsobem doporučuje typ disku. Výchozí typ úložiště jsou disky premium.
- **Velikost sítě**: Vyhodnocení serveru bere v úvahu síťový adaptér v místním počítači.


### <a name="calculate-sizing-performance-based"></a>Vypočítat velikost (na základě výkonu)

Pokud používáte velikost i na základě výkonu, vyhodnocení serveru, které doporučení pro stanovení velikosti provádí takto:

- Vyhodnocení serveru bere v úvahu historii výkonu počítače k identifikaci velikosti virtuálního počítače a typu disku v Azure.
- Pokud byly servery importovány pomocí souboru CSV, použijí se zadané hodnoty. Tato metoda je užitečná zejména v případě, že jste přerozděleny místní počítač, využití je nízká a chcete správně velikost virtuálního počítače v Azure ušetřit náklady. 
- Pokud nechcete používat data o výkonu, obnovte kritéria velikosti tak, aby byla místní, jak je popsáno v předchozí části.

#### <a name="calculate-storage-sizing"></a>Výpočet velikosti úložiště

Pro velikost úložiště Azure Migrate se pokusí mapovat každý disk připojený k počítači na disk v Azure a funguje takto:

1. Vyhodnocení serveru přidá čtení a zápis viop disku získat celkový požadovaný vipos. Podobně přidá hodnoty propustnosti pro čtení a zápisu, aby získal celkovou propustnost každého disku.
2. Pokud jste zadali typ úložiště jako automatické, na základě efektivní viops a propustnost hodnoty, vyhodnocení serveru určuje, zda disk by měl být mapován na standardní HDD, standardní SSD nebo premium disk v Azure. Pokud je typ úložiště nastaven na standardní pevný disk/SSD/Premium, vyhodnocení serveru se pokusí najít diskovou skladovou položku v rámci vybraného typu úložiště (disky Standard HDD/SSD/Premium).
3. Disky jsou vybrány takto:
    - Pokud server Assessment nemůže najít disk s požadovanými viopy a propustnost, označí počítač jako nevhodný pro Azure.
    - Pokud vyhodnocení serveru najde sadu vhodných disků, vybere disky, které podporují umístění zadané v nastavení hodnocení.
    - Pokud existuje více způsobilých disků, vyhodnocení serveru vybere disk s nejnižšími náklady.
    - Pokud nejsou k dispozici data o výkonu pro libovolný disk, konfigurační data disku (velikost disku) se používají k vyhledání standardního disku SSD v Azure.

#### <a name="calculate-network-sizing"></a>Vypočítat velikost sítě

Vyhodnocení serveru se pokusí najít virtuální počítač Azure, který podporuje počet síťových adaptérů připojených k místnímu počítači a výkon vyžadovaný těmito síťovými adaptéry.
- Chcete-li získat efektivní výkon sítě místního virtuálního počítače, vyhodnocení serveru agreguje data přenášená za sekundu (Mb/s) z počítače (síť out), přes všechny síťové adaptéry a použije faktor pohodlí. Toto číslo používá k vyhledání virtuálního počítače Azure, který podporuje požadovaný výkon sítě.
- Spolu s výkonem sítě, vyhodnocení serveru také zvažuje, zda virtuální počítač Azure podporuje požadovaný počet síťových adaptérů.
- Pokud nejsou k dispozici žádná data o výkonu sítě, vyhodnocení serveru bere v úvahu pouze počet síťových adaptérů pro velikost virtuálního adaptéru.


#### <a name="calculate-compute-sizing"></a>Vypočítat velikost výpočetní hodnoty

Po výpočtu požadavků na úložiště a síť, vyhodnocení serveru zvažuje požadavky na procesor a paměť najít vhodnou velikost virtuálního počítače v Azure.
- Azure Migrate vyhledá efektivní využívaná jádra a paměť a najde v Azure vhodnou velikost virtuálního počítače.
- Pokud není nalezena žádná vhodná velikost, je počítač označen jako nevhodný pro Azure.
- Pokud je nalezena vhodná velikost, Azure Migrate použije výpočty úložiště a sítě. Potom použije nastavení umístění a cenové úrovně pro konečné doporučení velikosti virtuálního počítače.
- Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.


## <a name="confidence-ratings-performance-based"></a>Hodnocení spolehlivosti (založené na výkonu)

Každé hodnocení založené na výkonu v Azure Migrate je spojeno s hodnocením spolehlivosti, které se pohybuje od jedné (nejnižší) do pěti hvězdiček (nejvyšší). Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti poskytovaných Azure Migrate.

- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Pro velikost na základě výkonu, server hodnocení potřebuje:
    - Data využití paměti procesoru a virtuálního počítače.
    - Data viopů disku a propustnost pro každý disk připojený k virtuálnímu počítače.
    - Vstupně-založené na síťovém vstupně-založené pro zpracování velikosti založené na výkonu pro každý síťový adaptér připojený k virtuálnímu virtuálnímu připojení.
    - Pokud některý z těchto čísel využití nejsou k dispozici, velikost doporučení nemusí být spolehlivé.

> [!NOTE]
> Hodnocení spolehlivosti se nepřiřazuje pro servery hodnocené pomocí importovaného . CSV. Hodnocení se také nevztahují na místní hodnocení podle pohlaví.
   
### <a name="ratings"></a>Ratings

V závislosti na procentu dostupných datových bodů je hodnocení spolehlivosti pro hodnocení následující.

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0-20% | 1 hvězdička
   21-40% | 2 hvězdičky
   41-60% | 3 hvězdičky
   61-80% | 4 hvězdičky
   81-100% | 5 hvězdiček

### <a name="low-confidence-ratings"></a>Nízké hodnocení spolehlivosti

Zde je několik důvodů, proč by hodnocení mohlo získat nízké hodnocení spolehlivosti:

- Neprofilovat prostředí po dobu, po kterou vytváříte hodnocení. Pokud například vytvoříte hodnocení s dobou trvání výkonu nastavenou na jeden den, musíte počkat alespoň jeden den po spuštění zjišťování pro všechny datové body, které mají být shromážděny.
- Některé virtuální společnosti byly vypnuty během období, pro které bylo vypočteno posouzení. Pokud jsou některé virtuální servery po určitou dobu vypnuté, hodnocení serveru nemůže shromažďovat údaje o výkonu za toto období.
- Některé virtuální společnosti byly vytvořeny během období, pro které bylo vypočteno posouzení. Například pokud jste vytvořili hodnocení pro historii výkonu za poslední měsíc, ale některé virtuální chody byly vytvořeny v prostředí pouze před týdnem, historie výkonu nových virtuálních hrach nebude existovat po celou dobu trvání.

> [!NOTE]
> Pokud je hodnocení spolehlivosti jakéhokoli hodnocení menší než pět hvězdiček, doporučujeme počkat alespoň jeden den, než zařízení profiluje prostředí, a poté přepočítat hodnocení. Pokud tak nechcete, velikost na základě výkonu nemusí být spolehlivé. V takovém případě doporučujeme přepnout hodnocení na místní velikosti.

## <a name="calculate-monthly-costs"></a>Výpočet měsíčních nákladů

Po dokončení doporučení pro nastavení velikosti Azure Migrate vypočítá náklady na výpočetní prostředky a úložiště po migraci.

- **Náklady na výpočetní výkon**: Pomocí doporučené velikosti virtuálního počítače Azure migrace používá rozhraní API pro fakturaci k výpočtu měsíčních nákladů na virtuální počítač.
    - Výpočet bere v úvahu operační systém, zabezpečení softwaru, rezervované instance, dobu provozu virtuálního počítače, umístění a nastavení měny.
    - Agreguje náklady napříč všemi počítači a vypočítá celkové měsíční výpočetní náklady.
- **Náklady na úložiště**: Měsíční náklady na úložiště pro stroj se vypočítátak, že se agregují měsíční náklady na všechny disky připojené k počítači, a to následovně:
    - Vyhodnocení serveru vypočítá celkové měsíční náklady na úložiště agregací nákladů na úložiště všech počítačů.
    - V současné době výpočet nebere v úvahu nabídky zadané v nastavení hodnocení.

Náklady jsou zobrazeny v měně zadané v nastavení hodnocení.


## <a name="next-steps"></a>Další kroky

[Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení. 


- Další informace o spuštění hodnocení pro [virtuální zařízení VMware](tutorial-prepare-vmware.md), [virtuální chod y Hyper-V](tutorial-prepare-hyper-v.md)a [fyzické servery](tutorial-prepare-physical.md).
- Informace o hodnocení serverů [importovaných pomocí souboru CSV](tutorial-assess-import.md).
- Informace o nastavení [vizualizace závislostí](concepts-dependency-visualization.md).
