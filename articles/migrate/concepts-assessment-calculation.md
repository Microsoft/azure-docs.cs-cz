---
title: Výpočty hodnocení ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje přehled o vyhodnocení výpočtů ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805987"
---
# <a name="assessment-calculations"></a>Výpočty hodnocení

Posouzení Server Azure Migrate posuzuje místní úlohy pro migraci do Azure. Tento článek obsahuje informace o tom, jak se počítají posouzení.


[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migrace místních aplikací a úloh a instancí privátního nebo veřejného cloudu, do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).

## <a name="overview"></a>Přehled

Vyhodnocení Azure Migrate Server Assessment má tři fáze. Posouzení začíná vhodnost analýzy, za nímž následuje velikosti, a nakonec měsíční odhadu nákladů. Počítače s pouze přesune později v případě úspěšného předchozí. Například pokud na počítači selže kontrola vhodnost pro Azure, je označen jako nevhodný pro Azure a změny velikosti a ocenění nebude provedeno.


## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/><br/> Azure Migrate v současné době podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, Kanada – střed, Kanada – východ, střed Indie, střed USA, Čína – východ, Čína – sever, východní Asie, východní USA, východní USA 2, Německo – střed, Německo – severovýchod, Japonsko – východ, Japonsko – Západ, Korea – střed, Korea – Jih, severní USA (střed), Severní Evropa, střední část jihu USA, jihovýchodní Asie, Indie – Jih, Velká Británie – Jih, Velká Británie – Západ, USA Arizona, USA (gov) Gov Texas, USA (gov) Virginia, USA (střed) – Západ, západní Evropa, Indie – Západ, USA – západ a západní USA 2.<br/> Cílová oblast je standardně nastavená na Západní USA 2.
**Typ úložiště** | Disky nebo Standard standardní HHD disky SSD nebo Premium.<br/><br/> Když zadáte typ úložiště jako automatické v posouzení, disk doporučení je založeno na data o výkonu disků (IOPS a propustnost).<br/><br/> Pokud chcete zadat typ úložiště jako Premium nebo Standard, vybrat posouzení doporučuje disk SKU v rámci typu úložiště.<br/><br/> Pokud chcete dosáhnout jednu instanci virtuálního počítače SLA 99,9 % dostupnost, můžete nastavit typ úložiště jako Premium managed disks. Potom všechny disky v posouzení doporučí jako Premium managed disks. <br/> Azure Migrate podporuje pro posouzení migrace jenom spravované disky.<br/> 
**Rezervované instance (RI)** | Tuto vlastnost zadejte, pokud obsahují rezervované instance v Azure. Odhad nákladů v posouzení se rezervované instance slevy vezměte v úvahu. Rezervované instance jsou momentálně podporuje jenom pro průběžné platby ve službě Azure Migrate.
**Kritérium určení velikosti** | Používá k nastavení správné velikosti virtuálních počítačů. Nastavení velikosti může být založené na výkon, nebo jako – místní, bez zohlednění historie výkonu.
**Historie výkonu** | Doba trvání má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost je použitelná, pouze pokud je určení velikosti na základě výkonu.
**Percentilové využití** | Hodnota percentilu sady vzorků výkonu, který se používá pro určení správné velikosti virtuálních počítačů. Tato vlastnost je použitelná, pouze pokud je určení velikosti na základě výkonu.
**Řada virtuálních počítačů** | Řada virtuálních počítačů, která se použije k odhadu velikostí. Pokud máte například produkční prostředí, které se nechystáte migrovat na virtuální počítače řady A-Series v Azure, můžete vyloučit řadu A-Series ze seznamu nebo řad. Určení velikosti se provádí pouze na základě vybraných řad.
**Faktor komfortu** | Azure Migrate Server Assessment počítá s rezervou (faktor komfortu) během posouzení. Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Fakturační měna. 
**Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure.<br/> Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud se vaše virtuální počítače běžet 24 x 7 v Azure, můžete určit dobu trvání (počet dnů za měsíc) a počtu hodin za den pro kterou se bude spuštěna a odhady nákladů, které by se dělalo odpovídajícím způsobem.<br/> Výchozí hodnota je 31 dnů za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určuje, zda máte software assurance a nárok [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. 



## <a name="azure-suitability-analysis"></a>Vhodnost pro Azure analysis

Ne všechny počítače jsou vhodné pro spuštění v Azure. Azure Migrate Server Assessment vyhodnocuje každý počítač v místním pro migraci a rozděluje do jedné z následujících kategorií vhodnost počítačů:
- **Připraveno pro Azure** – počítače můžete migrovat, protože – je do Azure bez nutnosti jakkoli měnit. Se spustí v Azure s podporou úplné Azure.
- **Připraveno pro Azure s podmíněně** -počítač může spustit v Azure, ale nemusí mít podporu úplné Azure. Například počítač se starší verzí operačního systému Windows Server se nepodporuje v Azure. Budete muset pečlivě před migrací těchto počítačů do Azure a postupujte podle pokynů nápravy navržený v posouzení, chcete-li vyřešit problémy s připraveností před migrací.
- **Nepřipraveno pro Azure** -počítače v Azure se nespustí. Například pokud místní počítač má disk o velikosti více než 4 TB k němu připojená, nelze jej hostovat v Azure. Je nutné podle pokynů uvedených nápravy navržený v hodnocení připravenosti tento problém vyřešit, před migrací do Azure. U počítačů, které jsou označeny jako není připraveno pro Azure není Hotovo odhadu určení správné velikosti a nákladů.
- **Připravenost Neznámá** – Azure Migrate nelze najít připravenosti počítačů kvůli nedostatku dat k dispozici v systému vCenter Server.



### <a name="machine-properties"></a>Vlastnosti počítače

Azure Migrate posuzuje následující vlastnosti místní virtuální počítač k určení, zda lze spustit v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti pro Azure**
--- | --- | ---
**Typ spuštění** | Azure podporuje virtuální počítače s typem spuštění BIOS a není rozhraní UEFI. | Podmíněně připraveno, pokud typ spouštění je UEFI.
**Počet jader** | Počet jader na počítačích musí být větší nebo menší než maximální počet jader (128 jader) pro virtuální počítač Azure nepodporuje.<br/><br/> Pokud není k dispozici historie výkonu, Azure Migrate bere v úvahu jader využívaných pro porovnání. Pokud je v nastavení posouzení Zadaný faktor komfortu, počet jader využívaných se vynásobí faktor komfortu.<br/><br/> Pokud neexistuje žádná historie výkonu, Azure Migrate použije přidělená jádra, bez použití faktor komfortu. | Připraveno, pokud je menší než nebo rovna omezení.
**Paměť** | Velikost paměti počítače musí být větší nebo menší než maximální velikost paměti (3892 GB na Azure M series Standard_M128m&nbsp;<sup>2</sup>) povolený pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud není k dispozici historie výkonu, Azure Migrate bere v úvahu využívaných paměť pro porovnání. Pokud je zadaný faktor komfortu, využívaných paměti se násobí hodnotou faktor komfortu.<br/><br/> Pokud neexistuje žádná historie přidělená paměť použijete, bez použití faktor komfortu.<br/><br/> | Pokud v mezích limitů připraven.
**Disk úložiště** | Přidělená velikost disku musí mít 4 TB (4096 GB) nebo méně.<br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disk s operačním systémem. | Pokud v mezích limitů připraven.
**Sítě** | Počítač musí mít 32 nebo méně síťových adaptérů připojený k němu. | Pokud v mezích limitů připraven.

### <a name="guest-operating-system"></a>Hostovaný operační systém
Spolu s vlastností virtuálního počítače Azure Migrate Server Assessment vypadá v hostovaném operačním systému počítače, a zjistěte, jestli je možné spustit v Azure.

> [!NOTE]
> Azure Migrate Server Assessment používá operačního systému zadané pro virtuální počítač v systému vCenter Server pro účely analýzy. Azure Migrate Server Assessment je založen na zařízení pro zjišťování virtuálních počítačů a nelze ověřit, zda operační systém spuštěný na virtuálním počítači je stejná jako předpona zadaná v systému vCenter Server.

Podle následujícího postupu používá Azure Migrate Server Assessment určit připravenost pro Azure podle operačního systému.

**Operační systém** | **Podrobnosti** | **Stav připravenosti pro Azure**
--- | --- | ---
Windows Server 2016 a všechny aktualizace Service packu | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 R2 a všechny aktualizace Service packu | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2012 a všechny aktualizace Service packu | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2008 R2 s všechny aktualizace Service packu | Azure poskytuje plnou podporu.| Připraveno pro Azure
Windows Server 2008 (32bitová verze a 64bitová verze) | Azure poskytuje plnou podporu. | Připraveno pro Azure
Windows Server 2003, 2003 R2 | Tyto operační systémy prošly jejich koncové datum podpory a potřebu [smlouvy podporují vlastní (CSA)](https://aka.ms/WSosstatement) pro podporu v Azure. | Podmíněně připraveno pro Azure, zvažte možnost upgradovat operační systém před migrací do Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Tyto operační systémy prošly jejich podporu datum konce, tento počítač může spustit v Azure, ale Azure nenabízí žádnou podporu operačního systému. | Podmíněně připraveno pro Azure, se doporučuje upgradovat operační systém před migrací do Azure.
Klient Windows 7, 8 a 10 | Azure poskytuje podporu s [pouze předplatné sady Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Připraveno pro Azure s podmínkou
Windows 10 Pro plochu | Azure poskytuje podporu s [práv hostování více klientů.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Připraveno pro Azure s podmínkou
Windows Vista, XP Professional | Tyto operační systémy prošly jejich podporu datum konce, tento počítač může spustit v Azure, ale Azure nenabízí žádnou podporu operačního systému. | Podmíněně připraveno pro Azure, se doporučuje upgradovat operační systém před migrací do Azure.
Linux | Azure schvaluje tyto [operačních systémů Linux](../virtual-machines/linux/endorsed-distros.md). Jiných operačních systémech Linux mohou spustit v Azure, ale doporučuje se upgradovat operační systém na doporučené verze před migrací do Azure. | Připraveno pro Azure, pokud je verze se schválenou sadou.<br/><br/>Podmíněně připraveno, pokud není schválené verze pro.
Jiné operační systémy<br/><br/> například Oracle Solaris, atd. Apple Mac OS, FreeBSD atd. | Azure neschvaluje těchto operačních systémů. Tento počítač může spustit v Azure, ale Azure nenabízí žádnou podporu operačního systému Azure. | Podmíněně připraveno pro Azure, se doporučuje instalovat podporovaný operační systém před migrací do Azure.  
Zadaný operační systém jako **jiných** v systému vCenter Server | Azure Migrate nemůže určit operační systém v tomto případě. | Připravenost neznámá. Ujistěte se, že je podporován operační systém ve virtuálním počítači v Azure.
32bitové operační systémy | Tento počítač může spustit v Azure, ale Azure nemusí poskytnout plnou podporu. | Podmíněně připraveno pro Azure, zvažte možnost upgradovat operační systém počítače z 32BITOVÉHO do 64BITOVÉHO před migrací do Azure.

## <a name="sizing"></a>Velikost

Po na počítači je označen jako připravený pro Azure, Azure Migrate velikost virtuálního počítače a jeho disků pro Azure.

- Pokud posouzení používá určení velikosti na základě výkonu, Azure Migrate se zohlední historie výkonu počítače, abyste mohli identifikovat typ velikosti a disku virtuálního počítače v Azure. Tato metoda je užitečná, pokud jste nadměrně přidělených místní virtuální počítač, ale využití je nízké a chcete k nastavení správné velikosti virtuálních počítačů v Azure pro úsporu nákladů.
- Pokud jste pomocí jako místní posouzení, Azure Migrate Server Assessment bude velikost virtuálních počítačů založených na místní nastavení bez zohlednění data o využití. Změna velikosti disku, v takovém případě vychází typ úložiště, kterou určíte ve vlastnostech posouzení (disk typu Standard nebo Premium disku).

### <a name="performance-based-sizing"></a>Určení velikosti na základě výkonu

Pro určení velikosti na základě výkonu Azure Migrate začíná disky připojené k virtuálnímu počítači, za nímž následuje síťové adaptéry, a pak mapování virtuálního počítače Azure na základě požadavky na výpočetní prostředky z místní virtuální počítač.

- **Úložiště**: Azure Migrate se pokouší mapovat všechny disky připojené k počítači, na disk v Azure.
    - Pokud chcete získat efektivní vstupně-výstupních operací za sekundu (IOPS) a propustnost (MB/s), Azure Migrate vynásobí disku IOPS a propustnost s faktorem komfortu. Na základě efektivní IOPS a propustnost hodnoty, Azure Migrate odhalí, pokud by měly být namapované na disk na disk úrovně standard nebo premium v Azure.
    - Pokud Azure Migrate nemůže najít disk s požadované IOPS a propustnost, označí počítače jako vhodný pro Azure. [Další informace](../azure-subscription-service-limits.md#storage-limits) týkající se Azure omezuje na disk a virtuální počítač.
    - Pokud najde sadu vhodné disky, Azure Migrate vybere ty, které podporují metodu redundance úložiště a umístění zadaného v nastavení posouzení.
    - Pokud existují oprávněné více disků, vybere tu s nejnižšími náklady.
    - Pokud není k dispozici údaje o výkonu pro disky, všechny disky se mapují na standardní disky v Azure.

- **Síť**: Azure Migrate se pokusí najít virtuální počítač Azure, který podporuje počet síťových adaptérů připojený k místnímu počítači a výkonu, požadují tyto síťové adaptéry.
    - Chcete-li získat efektivní síťový výkon místního virtuálního počítače, Azure Migrate agreguje data odeslaných za sekundu (MB/s) z počítače (sítě navýšení kapacity), ve všech síťových adaptérech a faktor komfortu se vztahuje. Toto číslo slouží k vyhledání Virtuálním počítači Azure, který podporuje požadovaný výkon sítě.
    - Spolu s výkon sítě úvahu se berou také pokud virtuální počítač Azure může podporovat požadovaný počet síťových adaptérů.
    - Pokud je k dispozici žádná data o výkonu sítě, pouze počet síťových adaptérů se považuje za určení velikosti virtuálního počítače.

- **COMPUTE**: Poté, co se počítá požadavky na úložiště a síť, Azure Migrate bere v úvahu požadavky na procesor a paměť najít vhodnou velikost virtuálního počítače v Azure.
    - Azure Migrate zjistí využívaných jader a paměti a použije faktor komfortu, chcete-li získat efektivní jader a paměti. Na základě čísla, pokusí se najít vhodné velikosti virtuálního počítače v Azure.
    - Pokud se nenajde žádné vhodné velikosti, tento počítač je označen jako nevhodný pro Azure.
    - Pokud se nenajde vhodné velikosti, Azure Migrate použije výpočty úložiště a sítě. Poté použije umístění a nastavení cenové úrovně, pro poslední doporučená velikost virtuálního počítače.
    - Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.

### <a name="as-on-premises-sizing"></a>Jako místní změny velikosti

Pokud používáte jako místní nastavení velikosti, Server Assessment přiděluje skladovou Položku virtuálního počítače v Azure podle velikosti místní. Podobně jako pro určení velikosti disku, vyhledá úložiště typ určený ve vlastnostech posouzení (Standard nebo Premium) a odpovídajícím způsobem doporučuje typ disku. Výchozí typ úložiště je prémiové disky.

## <a name="confidence-ratings"></a>Hodnocení spolehlivosti
Každý posouzení na základě výkonu ve službě Azure Migrate se přidruží hodnocení spolehlivosti v rozsahu od jednoho (nejnižší) do pěti spustí (nejvyšší).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti se netýká jako místních posouzení.
- Pro určení velikosti na základě výkonu potřebuje Azure Migrate Server Assessment:
    - Data o využití procesoru a paměť virtuálního počítače.
    - Pro každý disk připojený k virtuálnímu počítači navíc potřebuje IOPS a propustnost disku.
    - Podobně pro každý síťový adaptér připojený k virtuálnímu počítači potřebuje hodnocení spolehlivosti v / v sítě provést určení velikosti na základě výkonu.
    - Pokud některý z výše uvedených čísel o využití nejsou k dispozici v systému vCenter Server, doporučená velikost nemusí být spolehlivé. 

Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 Star
   81 až 100 % | 5 hvězdiček

### <a name="low-confidence-ratings"></a>Hodnocení s nízkou spolehlivostí

Několik důvodů, proč posouzení mohl s nízkou spolehlivostí hodnocení:

- Vaše prostředí neměli Profilovat po dobu trvání, pro kterou vytváříte posouzení. Například pokud vytváříte posouzení s dobu trvání výkonu, nastavení 1 den, budete muset počkat aspoň jeden den po spuštění zjišťování pro všechny datové body k získání shromažďovat.
- Několik virtuálních počítačů vypnulo během období, pro které se posouzení počítá. Pokud nějakou dobu byly všechny virtuální počítače vypnuté, nemůže Azure Migrate Server Assessment shromažďování dat výkonu pro toto období.
- Období, pro které se posouzení počítá se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale některé virtuální počítače vytvořené v prostředí týdnem, historie výkonu nových virtuálních počítačů nebude existuje po celou dobu trvání.

  > [!NOTE]
  > Pokud je hodnocení spolehlivosti nějakého posouzení nižší než pět hvězdičky, doporučujeme počkat aspoň jeden den pro zařízení, která má být profilována prostředí a potom posouzení vypočítat znovu. Pokud to neuděláte, určení velikosti na základě výkonu nemusí být spolehlivá a My doporučujeme přepnutí posouzení používat jako místní nastavení velikosti.
  
## <a name="monthly-cost-estimation"></a>Odhad měsíčních nákladů

Po dokončení doporučení velikosti Azure Migrate vypočítá náklady na výpočetní výkon a úložiště pro po migraci.

- **Výpočetní náklady**: Pomocí doporučená velikost virtuálního počítače Azure, Azure Migrate k výpočtu měsíční náklady pro virtuální počítač používá rozhraní API pro fakturaci.
    - Výpočet má operační systém, program software assurance, rezervované instance, virtuálního počítače doby provozu, umístění a nastavení měny v úvahu.
    - Agreguje ve všech počítačích, k výpočtu celkové měsíční náklady na výpočetní náklady.
- **Náklady na úložiště**: Měsíční náklady na úložiště pro počítač se vypočítá agregováním měsíční náklady pro všechny disky připojené k počítači
    - Azure Migrate Server Assessment vypočítá celkové měsíční náklady na úložiště na základě agregace náklady na úložiště všech počítačů.
    - V současné době nepřijímá výpočet nabídky zadaný v nastavení posouzení v úvahu.

Náklady jsou zobrazeny v místní měně, zadaný v nastavení posouzení.


## <a name="next-steps"></a>Další postup

Vytvořit posouzení pro [virtuálních počítačů VMware](tutorial-assess-vmware.md) nebo [virtuálních počítačů Hyper-V](tutorial-assess-hyper-v.md).
