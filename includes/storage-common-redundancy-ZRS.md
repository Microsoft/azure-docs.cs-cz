---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 0347d6ca951b75c385b138487f58b85a809c6805
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
Zóny redundantní úložiště (ZRS) synchronně replikuje data mezi tří (3) clustery úložiště v jedné oblasti. Každý cluster úložiště je fyzicky oddělená od ostatních a se nachází ve vlastní zóně dostupnosti (AZ). Každou zónu dostupnosti a cluster ZRS v něm, je autonomní pomocí samostatných nástrojů a možností sítě.

Ukládání dat v účtu ZRS zajistí, že budete přistupovat a správě dat v případě, že nedostupný zónu. ZRS poskytuje vynikající výkon a s velmi nízkou latencí. Ve skutečnosti ZRS má stejné [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) jako LRS.

Zvažte ZRS pro scénáře, které vyžadují silnou konzistenci, silné odolnost a vysokou dostupnost, i když výpadku nebo přírodní katastrofě vykreslí oblastmi datového centra není k dispozici. ZRS poskytuje odolnost pro úložiště objektů alespoň % 99.9999999999 (12 na 9) během daného roku.

Další informace o dostupnosti zón najdete v tématu [dostupnost zóny přehled](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Podpora je poskytována a místní dostupnost
ZRS je aktuálně podporuje [ **standardní, obecný v2 (GPv2)** ](../articles/storage/common/storage-account-options.md#general-purpose-v2) typy účtů. ZRS je k dispozici pro objekty BLOB bloku, objekty BLOB stránky jiný disk, soubory, tabulky a fronty. Kromě toho všechny vaše [Storage Analytics](../articles/storage/common/storage-analytics.md) protokoly a [metriky úložiště](../articles/storage/common/storage-enable-and-view-metrics.md)

ZRS je **všeobecně dostupná** v následujících oblastech:

- USA – východ 2
- USA – střed
- Severní Evropa
- Západní Evropa
- Francie – střed
- Jihovýchodní Asie

Společnost Microsoft nadále povolit ZRS v jiných oblastech Azure a tento seznam bude aktualizovat, pokud k tomu dojde. Také budeme takové oznámení prostřednictvím standardních kanálů, jako [aktualizace služby Azure](https://azure.microsoft.com/updates/) stránku a e-mailové oznámení pro vlastníky předplatného Azure a správce.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co se stane, když zónu přestane být dostupný?

Vaše data zůstanou odolné zónu přestane být dostupný. Společnost Microsoft doporučuje nadále osvědčených postupů pro přechodná chyba zpracování úloh, jako je například implementace zásady opakování s exponenciální back vypnout. Když zóna není k dispozici, se zavazuje Azure sítě aktualizace, jako je například DNS repointing. Tyto aktualizace může ovlivnit vaší aplikace, pokud se připojujete data předtím, než nebude dokončena.

ZRS nemusí chránit vaše data před regionální po havárii, kde jsou několika zón trvale vliv. Místo toho ZRS poskytuje odolnost proti chybám pro vaše data v případě dočasné nedostupnosti. Pro ochranu proti místní havárie, společnost Microsoft doporučuje používat [geograficky redundantní úložiště (GRS): mezi místní replikace pro Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Převádění na replikaci ZRS
V současné době je poměrně jednoduché změnit mezi LRS, GRS a RA-GRS – využít portálu nebo rozhraní API. S ZRS ale není jako jednoznačné protože se týká přesunu fyzické dat z jednoho úložiště razítka do několika razítek v oblasti. Jako takový je k dispozici dvě možnosti primární – ručně zkopírovat nebo přesunout data na nový účet ZRS z vašeho stávajícího účtu nebo požádejte migrace za provozu. Důrazně doporučujeme provést ruční migraci, protože nejde zaručit při migraci za provozu dokončí; Existuje celá řada faktorů, které přímo a nepřímo vliv na dokončení úlohy migrace. 

Chcete-li provést ruční migraci, máte různé možnosti:
- Použijte existující nástrojů jako AzCopy, sada SDK, úložiště spolehlivé nástroje třetích stran, atd.
- Pokud jste obeznámeni s Hadoop nebo HDInsight, můžete připojit i zdrojové a cílové (ZRS) účet ke clusteru a použít něco jako DistCp učinit masivně paralelní proces kopírování dat
- Vytvoření vlastních nástrojů využití jeden příchuť sada SDK úložiště

Jak už bylo zmíněno dříve, DŮRAZNĚ doporučujeme přejít trasy ruční migraci, protože nabízí větší flexibilitu než migrace za provozu. Také jste ve plně pod kontrolou když dojde k migraci.

Pokud však ruční migraci bude mít za následek výpadky aplikací a nemůžete vyrovná se se zatížením, na vaše koncové, poskytujeme možnost migrace za provozu. Migrace za provozu je místní migrace, která umožňuje pokračovat v používání vaší stávající účet úložiště při migraci dat mezi zdrojovým a cílovým razítka úložiště. Během migrace budete mít stále stejnou úroveň odolnost a smlouva SLA o dostupnosti, obvyklým způsobem.

Migrace za provozu s určitými omezeními ale pocházet. Jsou uvedeny níže.

- Když jsme vyřeší vaši žádost o migraci za provozu okamžitě, ale nemůžeme zaručit při migraci se ve skutečnosti dokončit. Pokud potřebujete dat, aby se v ZRS do určité doby, byste měli provést ruční migraci. Obecně platí, více dat, které máte v účtu tím déle bude trvat migrovat data. 
- Pouze za provozu můžete migrovat z účtu s LRS a GRS replikace. Pokud máte RA-GRS pak bude muset nejdřív změnit na jednu z těchto typů replikace, než budete pokračovat. Tento zprostředkující krok zajistí, že sekundární endpoint jen pro čtení, který RA-GRS poskytuje, jsou odebrány při jsou připravené.
- Váš účet musí být neprázdný.
- Jsou podporovány pouze uvnitř oblasti migrace. Pokud chcete migrovat data do účtu ZRS umístěný v oblasti, která se liší od zdrojového účtu, je třeba provést ruční migraci.
- Standardní úložiště pouze typy účtů. Nemůžete migrovat z prémiový účet úložiště.

Požadavky přejděte prostřednictvím portálu Azure podporu migrace za provozu. Z portálu vyberte účet úložiště, který chcete převést na ZRS.
1. Klikněte na tlačítko **novou žádost o podporu**
2. Ověření základní informace. Klikněte na **Další**. 
3. Na **problém** části 
    - Nechte závažnost jako-je.
    - Typ problému = **migrace dat**
    - Kategorie = **migrace na ZRS v rámci oblasti**
    - Title = **ZRS účet migrace** (nebo něco popisný)
    - Podrobnosti = chci migrace z [LRS, GRS] na ZRS v oblasti ___. 
4. Klikněte na **Další**.
5. Ověřte správnost kontaktní informace v okně kontaktní informace.
6. Klikněte na tlačítko **odeslání**.

V kontaktu s vám pak bude pracovníci technické podpory. Tato osoba bude k dispozici pro všechny pomoc, které možná budete potřebovat. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Starší verze možnost pro blok objektů BLOB redundance
> [!NOTE]
> ZRS klasické účty jsou plánované pro vyřazení a požadované migrace na 31. března 2021. Společnost Microsoft bude posílat podrobnosti ZRS Classic zákazníkům před vyřazení. Společnost Microsoft se plánuje poskytovat automatické migraci z ZRS Classic ZRS v budoucnu.

>[!NOTE]
> Po ZRS [všeobecně dostupná](#support-coverage-and-regional-availability) v oblasti, už nebudete moct vytvořit účet ZRS Classic z portálu v daném regionu stejné. Však můžete stále vytvořit jednu jiným způsobem, jako je Microsoft PowerShell a rozhraní příkazového řádku Azure, to znamená, dokud ZRS Classic je zastaralý.

ZRS Classic asynchronně replikuje data napříč datovými centry v rámci jedné nebo dvou oblastech. Replika nemusí být k dispozici, pokud společnost Microsoft nezahájí převzetí služeb při selhání a přechod na sekundární data. ZRS Classic je dostupná jenom pro **objekty BLOB bloků** v [pro obecné účely V1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1) účty úložiště. Účet ZRS Classic nelze převést na LRS nebo GRS ani naopak a nepodporuje možnosti metrik ani protokolování.

ZRS klasické účty nelze převést na nebo z LRS, GRS nebo RA-GRS. ZRS klasické účty také nepodporují metriky nebo protokolování.

Pokud chcete ručně migrovat data účtu ZRS do nebo z účtu LRS, ZRS Classic, GRS nebo RA-GRS, použijte AzCopy, Azure Storage Explorer, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. Můžete také vytvořit vlastní migrace řešení s jedním z knihovny klienta Azure Storage.
