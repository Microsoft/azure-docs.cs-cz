---
title: Výpočty hodnocení ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje přehled o vyhodnocení výpočtů ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 62683aaf7dda048b5828e9494ba8cafe6c8b8f9f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417935"
---
# <a name="assessment-calculations"></a>Výpočty hodnocení

[Azure Migrate](migrate-overview.md) posuzuje místní úlohy pro migraci do Azure. Tento článek obsahuje informace o tom, jak se počítají posouzení.


## <a name="overview"></a>Přehled

Posouzení služby Azure Migrate má tři fáze. Posouzení začíná vhodnost analýzy, za nímž následuje velikosti, a nakonec měsíční odhadu nákladů. Počítače s pouze přesune později v případě úspěšného předchozí. Například pokud na počítači selže kontrola vhodnost pro Azure, je označen jako nevhodný pro Azure a změny velikosti a ocenění nebude provedeno.

## <a name="azure-suitability-analysis"></a>Vhodnost pro Azure analysis

Ne všechny počítače jsou vhodné pro cloud má svou vlastní omezení a požadavky na spouštění v cloudu. Azure Migrate posuzuje vhodnost k migraci do Azure každý místní počítač a slouží ke kategorizaci počítače do jedné z následujících kategorií:
- **Připraveno pro Azure** – počítače můžete migrovat, protože – je do Azure bez nutnosti jakkoli měnit. Se spustí v Azure s podporou úplné Azure.
- **Připraveno pro Azure s podmíněně** -počítač může spustit v Azure, ale nemusí mít podporu úplné Azure. Například počítač se starší verzí operačního systému Windows Server se nepodporuje v Azure. Budete muset pečlivě před migrací těchto počítačů do Azure a postupujte podle pokynů nápravy navržený v posouzení, chcete-li vyřešit problémy s připraveností před migrací.
- **Nepřipraveno pro Azure** -počítače v Azure se nespustí. Například pokud místní počítač má disk o velikosti více než 4 TB k němu připojená, nelze jej hostovat v Azure. Je nutné podle pokynů uvedených nápravy navržený v hodnocení připravenosti tento problém vyřešit, před migrací do Azure. U počítačů, které jsou označeny jako není připraveno pro Azure není Hotovo odhadu určení správné velikosti a nákladů.
- **Připravenost Neznámá** – Azure Migrate nelze najít připravenosti počítačů kvůli nedostatku dat k dispozici v systému vCenter Server.

Azure Migrate posuzuje vlastnosti počítače a operačního systému hosta Chcete-li určit připravenost pro Azure z místního počítače.

### <a name="machine-properties"></a>Vlastnosti počítače
Azure Migrate posuzuje následující vlastnosti místní virtuální počítač a zjistěte, jestli virtuální počítač můžete spustit v Azure.

**Vlastnost** | **Podrobnosti** | **Stav připravenosti pro Azure**
--- | --- | ---
**Typ spuštění** | Azure podporuje virtuální počítače s typem spuštění BIOS a není rozhraní UEFI. | Podmíněně připraveno, pokud typ spouštění je UEFI.
**Počet jader** | Počet jader na počítačích musí být větší nebo menší než maximální počet jader (128 jader) pro virtuální počítač Azure nepodporuje.<br/><br/> Pokud není k dispozici historie výkonu, Azure Migrate bere v úvahu jader využívaných pro porovnání. Pokud je v nastavení posouzení Zadaný faktor komfortu, počet jader využívaných se vynásobí faktor komfortu.<br/><br/> Pokud neexistuje žádná historie výkonu, Azure Migrate použije přidělená jádra, bez použití faktor komfortu. | Připraveno, pokud je menší než nebo rovna omezení.
**Paměť** | Velikost paměti počítače musí být větší nebo menší než maximální velikost paměti (3892 GB na Azure M series Standard_M128m&nbsp;<sup>2</sup>) povolený pro virtuální počítač Azure. [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Pokud není k dispozici historie výkonu, Azure Migrate bere v úvahu využívaných paměť pro porovnání. Pokud je zadaný faktor komfortu, využívaných paměti se násobí hodnotou faktor komfortu.<br/><br/> Pokud neexistuje žádná historie přidělená paměť použijete, bez použití faktor komfortu.<br/><br/> | Pokud v mezích limitů připraven.
**Disk úložiště** | Přidělená velikost disku musí mít 4 TB (4096 GB) nebo méně.<br/><br/> Počet disků připojených k počítači musí být 65 nebo méně, včetně disk s operačním systémem. | Pokud v mezích limitů připraven.
**Sítě** | Počítač musí mít 32 nebo méně síťových adaptérů připojený k němu. | Pokud v mezích limitů připraven.

### <a name="guest-operating-system"></a>Hostovaný operační systém
Spolu s vlastností virtuálního počítače Azure Migrate také vypadá v hostovaném operačním systému virtuálního počítače s místními a zjistěte, jestli virtuální počítač můžete spustit v Azure.

> [!NOTE]
> Azure Migrate bere v úvahu určený v systému vCenter Server, jak provádět analýzu následující operační systém. Protože zjišťování službou Azure Migrate je založená na zařízení, nemá způsob, jak ověřit, zda operační systém virtuálního počítače je stejný jako jeden zadaný v systému vCenter Server.

Podle následujícího postupu lze službou Azure Migrate určit připravenost pro Azure založený na operačním systému virtuálního počítače.

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

Po na počítači je označen jako připravený pro Azure, Azure Migrate velikost virtuálního počítače a jeho disků pro Azure. Pokud je kritérium určení velikosti zadaného ve vlastnostech posouzení provést určení velikosti na základě výkonu, Azure Migrate se zohlední historie výkonu počítače, abyste mohli identifikovat typ velikosti a disku virtuálního počítače v Azure. Tato metoda je užitečná v situacích, kdy máte nadměrně přidělených místní virtuální počítač, ale využití je nízké a chcete k nastavení správné velikosti virtuálních počítačů v Azure za účelem snížení nákladů.

Pokud nechcete, vezměte v úvahu historie výkonu pro určení velikosti virtuálního počítače a chcete převést virtuální počítač jako – je do Azure, můžete určit kritérium určení velikosti jako *jako místní* a Azure Migrate se změnit velikost virtuálních počítačů založených na místní konfigurace bez zohlednění data o využití. Změna velikosti disku, v takovém případě se provede na základě typu úložiště, kterou určíte ve vlastnostech posouzení (disk typu Standard nebo Premium disk)

### <a name="performance-based-sizing"></a>Určení velikosti na základě výkonu

Pro určení velikosti na základě výkonu začíná Azure Migrate disky připojené k virtuálnímu počítači, za nímž následuje síťových adaptérů a pak mapování virtuálního počítače Azure na základě požadavky na výpočetní prostředky z místní virtuální počítač.

- **Úložiště**: Azure Migrate se pokouší mapovat všechny disky připojené k počítači, na disk v Azure.

    > [!NOTE]
    > Azure Migrate podporuje pouze spravované disky pro posouzení.

    - Pokud chcete získat efektivní vstupně-výstupních operací za sekundu (IOPS) a propustnost (MB/s), Azure Migrate vynásobí disku IOPS a propustnost s faktorem komfortu. Na základě efektivní IOPS a propustnost hodnoty, Azure Migrate odhalí, pokud by měly být namapované na disk na disk úrovně standard nebo premium v Azure.
    - Pokud Azure Migrate nemůže najít disk s požadované IOPS a propustnost, označí počítače jako vhodný pro Azure. [Další informace](../azure-subscription-service-limits.md#storage-limits) týkající se Azure omezuje na disk a virtuální počítač.
    - Pokud najde sadu vhodné disky, Azure Migrate vybere ty, které podporují metodu redundance úložiště a umístění zadaného v nastavení posouzení.
    - Pokud existují oprávněné více disků, vybere tu s nejnižšími náklady.
    - Pokud data výkonu u disků do nedostupný, všechny disky se mapují na standardní disky v Azure.

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
Pokud je kritérium určení velikosti *jako v místním nastavení velikosti*, Azure Migrate nebere v úvahu historie výkonu virtuálních počítačů a disků a přiděluje skladovou Položku virtuálního počítače v Azure na základě velikosti přidělené místní. Podobně jako pro určení velikosti disku, vyhledá úložiště typ určený ve vlastnostech posouzení (Standard nebo Premium) a odpovídajícím způsobem doporučuje typ disku. Výchozí typ úložiště je prémiové disky.

### <a name="confidence-rating"></a>Hodnocení spolehlivosti
Ke každému posouzení na základě výkonu ve službě Azure Migrate se přidruží hodnocení spolehlivosti v rozsahu od jedné do pěti hvězdiček (jedna hvězdička znamená nejnižší a pět hvězdiček nejvyšší spolehlivost). Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení. Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate. Hodnocení spolehlivosti se netýká místních posouzení.

K určení velikosti na základě výkonu potřebuje Azure Migrate data o využití procesoru a paměti virtuálního počítače. Pro každý disk připojený k virtuálnímu počítači navíc potřebuje IOPS a propustnost disku. Podobně u každého síťového adaptéru připojeného k virtuálnímu počítači potřebuje Azure Migrate k určení velikosti na základě výkonu informace o síťových vstupech a výstupech. Pokud některá z výše uvedených čísel o využití nejsou v systému vCenter Server k dispozici, doporučení velikosti provedené službou Azure Migrate nemusí být spolehlivé. Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

   Níže jsou důvody týkající se důvod, proč posouzení můžete narazit nízkou spolehlivosti:

   - Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Například pokud vytváříte interní hodnocení s dobou výkonu nastavenou na 1 den, budete muset počkat aspoň jeden den po spuštění zjišťování, aby se shromáždily všechny datové body.

   - Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, nebudeme schopni za toto období shromáždit data o výkonu.

   - Během období, pro které se posouzení počítá, se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takových případech nebude k dispozici historie výkonu nových virtuálních počítačů za celé období.

   > [!NOTE]
   > Pokud je hodnocení spolehlivosti nějakého posouzení nižší než 5 hvězdiček, doporučujeme vám počkat aspoň jeden den pro zařízení, která má být profilována prostředí a potom *přepočítat* posouzení. Pokud to není možné, určení velikosti na základě výkonu nemusí být spolehlivé a doporučuje se změnou vlastností posouzení přepnout na *určování stejné velikosti jako v místním prostředí*.

## <a name="monthly-cost-estimation"></a>Odhad měsíčních nákladů

Po dokončení doporučení velikosti Azure Migrate vypočítá náklady na výpočetní výkon a úložiště po migraci.

- **Výpočetní náklady**: Pomocí doporučená velikost virtuálního počítače Azure, Azure Migrate k výpočtu měsíční náklady pro virtuální počítač používá rozhraní API pro fakturaci. Výpočet má operační systém, program software assurance, rezervované instance, virtuálního počítače doby provozu, umístění a nastavení měny v úvahu. Agreguje ve všech počítačích, k výpočtu celkové měsíční náklady na výpočetní náklady.
- **Náklady na úložiště**: Měsíční náklady na úložiště pro počítač se vypočítá agregováním měsíční náklady pro všechny disky připojené k počítači. Azure Migrate vypočítá celkové měsíční náklady na úložiště na základě agregace náklady na úložiště všech počítačů. V současné době nepřijímá výpočet nabídky zadaný v nastavení posouzení v úvahu.

Náklady jsou zobrazeny v místní měně, zadaný v nastavení posouzení.


## <a name="next-steps"></a>Další postup

[Vytvořte posouzení pro místní virtuální počítače VMware](tutorial-assessment-vmware.md)
