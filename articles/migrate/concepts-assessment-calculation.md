---
title: Vyhodnocení výpočtů v Azure migrovat | Microsoft Docs
description: Poskytuje přehled o vyhodnocení výpočtů ve službě Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6fd0af65e63e9fc1c09232cd1e002da105a9d086
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287884"
---
# <a name="assessment-calculations"></a>Výpočty hodnocení

[Azure migrací](migrate-overview.md) vyhodnocuje místní úlohy pro migraci do Azure. Tento článek obsahuje informace o tom, jak jsou vypočítávány vyhodnocování.


## <a name="overview"></a>Přehled

Posouzení migrace Azure má tři fáze. Hodnocení začíná vhodnosti analýzy, za nímž následuje velikosti, a nakonec měsíční náklady odhad. Počítače s pouze přesune na pozdější fázi pokud předává předchozí. Například pokud je počítač selže kontrola Azure vhodnosti, je označen jako není vhodný pro Azure a velikost a nákladů nebude provedeno.


## <a name="azure-suitability-analysis"></a>Analýza Azure vhodnosti

Ne všechny počítače jsou vhodné pro spuštění v cloudu jako cloud má svou vlastní omezení a požadavky. Azure migrací vyhodnocuje každý místního počítače pro migraci vhodnosti do Azure a rozděluje se počítače do jedné z následujících kategorií:
- **Připraveno k Azure** -na počítači mohou být migrovány jako-je do Azure bez uložení změn. Se spustí v Azure s podporu úplné Azure.
- **Podmíněná připraveni k Azure** -tento počítač může spustit v Azure, ale nemusí mít podporu úplné Azure. Například počítač se starší verzí operačního systému Windows Server nepodporuje v Azure. Budete muset pečlivě před migrací těchto počítačů do Azure a postupujte podle pokynů nápravy navržený v assessment opravit problémy připravenosti, před migrací.
- **Není připraven pro Azure** – počítač se nespustí v Azure. Například pokud místní počítač disk o velikosti více než 4 TB připojený, ho nemůže být hostovaná v Azure. Budete muset podle pokynů uvedených nápravy navržený v assessment vyřešit problém, připravenosti před migrací do Azure. Pro počítače, které jsou označeny jako není připraven pro Azure neprovádí odhadu optimalizaci velikosti a nákladů.
- **Neznámá připravenost** -Azure migrovat nelze nalézt připravenost počítače z důvodu nedostatečných data dostupná v systému vCenter Server.

Azure migrací zkontroluje počítač vlastnosti a hostovaného operačního systému k identifikaci Azure připravenosti na místním počítači.

### <a name="machine-properties"></a>Vlastnosti počítače.
Azure migrací zkontroluje následující vlastnosti virtuálního počítače na místě a určete, jestli virtuální počítač můžete spustit v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti Azure**
--- | --- | ---
**Typ spouštění** | Azure podporuje virtuální počítače s typem spuštění jako systém BIOS a ne rozhraní UEFI. | Podmíněná připravené pro Azure, pokud se spouštěcí typ rozhraní UEFI.
**Počet jader** | Počet jader na počítačích musí být rovna nebo menší než maximální počet jader (32) podporované pro virtuální počítač Azure.<br/><br/> Pokud není k dispozici historie výkonu, migrovat Azure zvažuje využívané jader pro porovnání. Pokud v nastavení hodnocení je zadán koeficient pohodlí, počet jader využívané vynásobí faktorem pohodlí.<br/><br/> Pokud není žádná historie výkonu, migrovat Azure používá přidělených jader bez použití Multi-Factor pohodlí. | Není připraven Pokud počet jader je větší než 32.
**Paměť** | Velikost paměti počítače musí být rovna nebo menší než maximální povolenou pro virtuální počítač Azure paměť (448 GB). <br/><br/> Pokud není k dispozici historie výkonu, migrovat Azure zvažuje využívané paměti pro porovnání. Pokud je zadán koeficient pohodlí, využívané paměti vynásobí faktorem pohodlí.<br/><br/> Pokud není žádná historie je použita přidělenou paměť, bez použití Multi-Factor pohodlí.<br/><br/> | Není připraven Pokud velikost paměti je větší než 448 GB.
**Disk úložiště** | Přidělená velikost disku musí být 4 TB (4096 GB) nebo méně.<br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disku operačního systému. | Není připraven, pokud existuje disk má velikost větší než 4 TB, nebo pokud existuje víc než 65 disky připojené k počítači.
**Sítě** | Na počítači musíte mít 32 nebo méně síťové adaptéry připojené k němu. | Není připraven, pokud má počítač více než 32 síťových adaptérů

### <a name="guest-operating-system"></a>Hostovaný operační systém
Společně s vlastností virtuálního počítače Azure migrovat také vypadá na hostovaný operační systém virtuálního počítače na místě a zjistit, zda virtuální počítač můžete spustit v Azure.

> [!NOTE]
> Azure migrací zvažuje operačního systému zadané v systému vCenter Server udělat následující analýzy. Vzhledem k tomu, že zjišťování provádí migraci Azure je založen na zařízení, nemá způsob, jak ověřit, zda operačního systému běžících v rámci virtuálního počítače je stejný jako jeden zadaný v systému vCenter Server.

Následující logice podle migrovat Azure slouží k identifikaci Azure připravenosti na virtuální počítač založený na operačním systému.

**Operační systém** | **Podrobnosti** | **Stav připravenosti Azure**
--- | --- | ---
Windows Server 2016 & všechny aktualizace Service Pack | Azure poskytuje úplnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 a všechny aktualizace Service Pack | Azure poskytuje úplnou podporu. | Připraveno pro Azure
Windows Server 2012 & všechny aktualizace Service Pack | Azure poskytuje úplnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 s všechny aktualizace Service Pack | Azure poskytuje úplnou podporu.| Připraveno pro Azure
Windows Server 2003-2008 R2 | Tyto operační systémy uplynulo jejich koncové datum podpory a nutnost [vlastní podporu smlouvy (CSA)](https://aka.ms/WSosstatement) pro podporu v Azure. | Podmíněná připraveni k Azure, zvažte upgradování operačního systému před migrací do Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Předané jejich koncové datum podpory těchto operačních systémů, bude počítač může spustit v Azure, ale žádná podpora operačního systému je k dispozici v Azure. | Podmíněná připravený pro Azure, se doporučuje pro upgrade operačního systému před migrací do Azure.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu pro předplatné sady Visual Studio. | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Předané jejich koncové datum podpory těchto operačních systémů, bude počítač může spustit v Azure, ale žádná podpora operačního systému je k dispozici v Azure. | Podmíněná připravený pro Azure, se doporučuje pro upgrade operačního systému před migrací do Azure.
Linux | Azure nezodpovídá tyto [operačních systémů Linux](../virtual-machines/linux/endorsed-distros.md). Jiných operačních systémů Linux může spustit v Azure, ale doporučuje se upgrade operačního systému na potvrzená verzi před migrací do Azure. | Připraveno pro Azure. Pokud je verze schválené.<br/><br/>Podmíněná připravené, pokud není schválené verzi.
Jiné operační systémy<br/><br/> například Oracle Solaris, atd. Apple Mac OS, FreeBSD atd. | Azure neschvaluje těchto operačních systémů. Tento počítač může spustit v Azure, ale žádná podpora operačního systému je k dispozici v Azure. | Podmíněná připravený pro Azure, se doporučuje nainstalovat podporovaný operační systém před migrací do Azure.  
Zadaný operační systém jako *jiných* v systému vCenter Server | Azure migrací nemůže v tomto případě identifikovat operačního systému. | Neznámý připravenosti. Ujistěte se, že operačního systému spuštěné ve virtuálním počítači podporovaná v Azure.
32bitové operační systémy | Tento počítač může spustit v Azure, ale Azure nemusí poskytnout plnou podporu. | Podmíněná připravené pro Azure, zvažte upgrade operačního systému na počítač z 32bitového operačního systému na 64bitový operační systém, před migrací do Azure.

## <a name="sizing"></a>Velikost

Po počítač je označena jako připravena pro Azure, Azure migrovat velikostí virtuálního počítače a jeho disky pro Azure. Je-li toto nastavení velikosti kritérium zadána ve vlastnostech assessment uděláte na základě výkonu nastavení velikosti, migrovat Azure za historie výkonu počítače k identifikaci typ velikosti a disku virtuálního počítače v Azure. Tato metoda je užitečná ve scénářích, kde jste přidělili přepsání místní virtuální počítač, ale je nízkou využití a byste chtěli optimální velikost virtuálních počítačů v Azure se uložit náklady.

> [!NOTE]
> Azure migrací shromažďuje historie výkonu místní virtuálních počítačů ze serveru vCenter Server. Aby přesné optimalizaci velikosti, zkontrolujte, že nastavení statistiky v systému vCenter Server je nastavená na úroveň 3 a počkejte alespoň jeden den před si to chtěl / vypnutí zjišťování místní virtuální počítače. Pokud nastavení statistiky v systému vCenter Server je nižší než úroveň 3, není shromažďovat údaje o výkonu pro disk a síť.

Pokud nechcete, zvažte historie výkonu pro virtuální počítač sizing a chcete převzít virtuální počítač jako-je do Azure, můžete zadat kritéria pro změnu velikosti jako *jako místní* a migrovat Azure bude potom velikost založené na místní virtuální počítače konfigurace bez ohledu data o využití. Změna velikosti disku, v takovém případě bude provedeno na základě typu úložiště, které určíte ve vlastnostech assessment (Standard disk nebo Premium)

### <a name="performance-based-sizing"></a>Nastavení velikosti na základě výkonu

Pro nastavení velikosti na základě výkonu, síťové adaptéry a potom migrovat Azure začíná disky připojené k virtuálnímu počítači, a pak mapy virtuální počítač Azure na základě požadavky na výpočetní místní virtuálního počítače.

- **Úložiště**: migrace Azure pokusí namapovat všechny disky připojené k počítači na disk v Azure.

    > [!NOTE]
    > Azure migrací podporuje pouze spravované disky pro vyhodnocení.

    - Pokud chcete získat efektivní diskové vstupně-výstupních operací za sekundu (IOPS) a propustnost (MB/s), Azure migrovat vynásobí disku IOPS a propustnost s Multi-Factor pohodlí. Na základě efektivní IOPS a hodnoty propustnosti, migrovat Azure označuje, jestli disk by měly být namapované na standard nebo premium disk v Azure.
    - Pokud migrace Azure nemůže najít disk s požadované IOPS & propustnost, označí počítač jako není vhodný pro Azure. [Další informace](../azure-subscription-service-limits.md#storage-limits) o Azure omezuje na disk a virtuální počítač.
    - Pokud najde sadu vhodné disky, Azure migrace vybere ty, které podporují metodu redundance úložiště a k umístění zadanému v nastavení hodnocení.
    - Pokud existují oprávněné víc disků, vybere jeden s nejnižší náklady.
    - Pokud se údaje o výkonu pro disky v není k dispozici, všechny disky jsou namapované na standardní disky v Azure.

- **Sítě**: migrace Azure se pokusí najít virtuálního počítače Azure, který může podporovat počet síťových adaptérech připojených do místního počítače a výkonu vyžadují tyto síťové adaptéry.
    - Získat efektivní síťový výkon místní virtuální počítač, migrujte Azure agreguje data přenášená za sekundu (MBps) z počítače (síť out), ve všech síťových adaptérech a vztahuje na pohodlí faktor. Toto číslo se používá k vyhledání podporující požadované síťový výkon virtuálním počítači Azure.
    - Společně s výkon sítě, také považuje pokud virtuální počítač Azure může podporovat požadované počet síťových adaptérů.
    - Pokud je k dispozici žádná data o výkonu sítě, považuje za pouze počet síťových adaptérů pro nastavení velikosti virtuálních počítačů.

- **Výpočetní**: po úložiště a síťové požadavky jsou vypočítávány, migrovat Azure zvažuje procesoru a paměti požadavky najít vhodný velikost virtuálního počítače v Azure.
    - Azure migrací vyhledá využívané jader a paměti a platí pohodlí faktor, který chcete získat efektivní jader a paměti. Podle toho, že číslo, pokusí se najít vhodnou velikost virtuálního počítače v Azure.
    - Pokud je nalezen žádný vhodný velikost, tento počítač je označena jako není vhodný pro Azure.
    - Pokud se nenajde vhodný velikost, migrovat Azure platí výpočty úložiště a sítě. Pak provede umístění a cenovou úroveň nastavení pro konečné doporučení velikost virtuálního počítače.
    - Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.

### <a name="as-on-premises-sizing"></a>Jako místní Změna velikosti
Pokud je toto nastavení velikosti kritérium *jako místní nastavení velikosti*, migrovat Azure nebere v úvahu historie výkonu virtuálních počítačů a disky a přiděluje SKU virtuálních počítačů v Azure, v závislosti na velikosti přidělené místně. Podobně platí pro nastavení velikosti disku, zjistí zadaný ve vlastnostech assessment (Standard nebo Premium) typ úložiště a odpovídajícím způsobem doporučuje typ disku. Výchozí typ úložiště je prémiové disky.

### <a name="confidence-rating"></a>Hodnocení spolehlivosti

Ke každému posouzení ve službě Azure Migrate se přidruží hodnocení spolehlivosti v rozsahu od 1 do 5 hvězdiček (1 hvězdička znamená nejnižší a 5 hvězdiček nejvyšší spolehlivost). Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení. Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.

Hodnocení spolehlivosti posouzení je užitečnější u posouzení s kritériem velikosti založeným na výkonu. K určení velikosti na základě výkonu potřebuje Azure Migrate data o využití procesoru a paměti virtuálního počítače. Pro každý disk připojený k virtuálnímu počítači navíc potřebuje IOPS a propustnost disku. Podobně u každého síťového adaptéru připojeného k virtuálnímu počítači potřebuje Azure Migrate k určení velikosti na základě výkonu informace o síťových vstupech a výstupech. Pokud některá z výše uvedených čísel o využití nejsou v systému vCenter Server k dispozici, doporučení velikosti provedené službou Azure Migrate nemusí být spolehlivé. Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

Posouzení nemusí mít k dispozici všechny datové body z některého z následujících důvodů:
- Statistika v systému vCenter Server není nastavena na úroveň 3. Pokud je nastavení statistiky v systému vCenter Server nižší než úroveň 3, data o výkonu disku a sítě se ze systému vCenter Server neshromažďují. V takovém případě není doporučení služby Azure Migrate týkající se disku a sítě založené na využití. Bez zohlednění vstupně-výstupních operacích za sekundu a propustnosti disku nemůže Azure Migrate určit, jestli disk bude v Azure potřebovat disk Premium, a proto v tomto případě Azure Migrate doporučí pro všechny disky použít disky Standard.
- Nastavení statistiky v systému vCenter Server bylo před zahájením zjišťování nastavené na úroveň 3 po kratší dobu. Zvažme například scénář, kdy dnes změníte nastavení statistiky na úroveň 3 a zítra (za 24 hodin) zahájíte zjišťování pomocí zařízení kolektoru. Pokud vytváříte posouzení za jeden den, máte všechny datové body a hodnocení spolehlivosti posouzení bude 5 hvězdiček. Pokud však změníte dobu trvání výkonu ve vlastnostech posouzení na jeden měsíc, hodnocení spolehlivosti se sníží, protože data o výkonu disku a sítě za poslední měsíc nebudou k dispozici. Pokud chcete zohlednit data o výkonu za poslední měsíc, doporučujeme ponechat nastavení statistiky systému vCenter Server na úrovni 3 po dobu jednoho měsíce před zahájením zjišťování.
- Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, vCenter Server nebude mít za toto období data o výkonu.
- Během období, pro které se posouzení počítá, se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takových případech nebude k dispozici historie výkonu nových virtuálních počítačů za celé období.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení nižší než 4 hvězdičky, doporučujeme změnit nastavení statistiky systému vCenter Server na úroveň 3, počkat po dobu, kterou chcete v posouzení zohlednit (1 den, 1 týden, 1 měsíc), a pak provést zjišťování a posouzení. Pokud to není možné, určení velikosti na základě výkonu nemusí být spolehlivé a doporučuje se změnou vlastností posouzení přepnout na *určování stejné velikosti jako v místním prostředí*.

## <a name="monthly-cost-estimation"></a>Měsíční náklady na odhad

Po dokončení nastavení velikosti doporučení se migrovat Azure vypočítá náklady na výpočetní a úložnou po migraci.

- **Výpočetní náklady**: pomocí doporučená velikost virtuálního počítače Azure, Azure migrovat používá rozhraní API fakturace můžete vypočítat náklady na měsíční pro virtuální počítač. Výpočet trvá operačního systému, programu software assurance, vyhrazenou instancí, virtuálních počítačů provozu, umístění a nastavení měny v úvahu. Ji ve všech počítačích, chcete-li vypočítat celkové měsíční náklady na výpočetní agreguje náklady.
- **Náklady na úložiště**: měsíční úložiště, náklady pro počítače s je vypočítána agregování měsíční náklady na všechny disky připojené k počítači. Azure migrací se vypočítává agregování náklady na úložiště všech počítačů celkové měsíční náklady na úložiště. V současné době výpočtu neberou nabízí uvedenou v nastavení assessment v úvahu.

Náklady jsou zobrazeny v měny v nastavení hodnocení.


## <a name="next-steps"></a>Další postup

[Vytvoření posouzení pro virtuální počítače VMware na místě](tutorial-assessment-vmware.md)
