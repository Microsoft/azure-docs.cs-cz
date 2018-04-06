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
ms.openlocfilehash: f6d437e4ad0e05d55c408ad8f9defe5385b52050
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
Zóny redundantní úložiště (ZRS) synchronně replikuje data mezi tři clustery úložiště v jedné oblasti. Každý cluster úložiště je fyzicky oddělená od ostatních a se nachází ve vlastní zóně dostupnosti (AZ). Každou zónu dostupnosti a cluster ZRS v něm, je autonomní pomocí samostatných nástrojů a možností sítě.

Ukládání dat v účtu ZRS zajistí, že budete přistupovat a správě dat v případě, že nedostupný zónu. ZRS poskytuje vynikající výkon a s velmi nízkou latencí.

Další informace o dostupnosti zón najdete v tématu [dostupnost zóny přehled](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="zrs-for-high-availability"></a>ZRS pro zajištění vysoké dostupnosti 

Zvažte ZRS pro scénáře, které vyžadují silnou konzistenci, silné odolnost a vysokou dostupnost, i když k výpadku nebo přírodní katastrofě vykreslí datového centra není k dispozici. ZRS poskytuje odolnost pro úložiště objektů alespoň % 99.9999999999 (12 na 9) během daného roku.

ZRS aktuálně podporuje standardní, obecný v2 typy účtů (GPv2). ZRS je k dispozici pro objekty BLOB bloku, objekty BLOB stránky jiný disk, soubory, tabulky a fronty. 

ZRS je obecně k dispozici v následujících oblastech:

- USA – východ 2
- USA – střed
- Severní Evropa
- Západní Evropa
- Francie – střed
- Jihovýchodní Asie

Microsoft bude nadále povolit ZRS v jiných oblastech Azure.

### <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co se stane, když zónu přestane být dostupný?

Vaše data zůstanou odolné zónu přestane být dostupný. Společnost Microsoft doporučuje nadále osvědčených postupů pro přechodná chyba zpracování úloh, jako je například implementace zásady opakování s exponenciální back vypnout. Když zóna není k dispozici, se zavazuje Azure sítě aktualizace, jako je například DNS repointing. Tyto aktualizace může ovlivnit vaší aplikace, pokud se připojujete data předtím, než nebude dokončena.

ZRS nemusí chránit vaše data před regionální po havárii, kde jsou několika zón trvale vliv. Místo toho ZRS poskytuje odolnost proti chybám pro vaše data v případě dočasné nedostupnosti. Pro ochranu proti místní havárie, společnost Microsoft doporučuje používat [geograficky redundantní úložiště (GRS): mezi místní replikace pro Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Starší verze možnost pro blok objektů BLOB redundance
> [!NOTE]
> ZRS klasické účty jsou plánované pro vyřazení a požadované migrace na 31. března 2021. Společnost Microsoft bude posílat podrobnosti ZRS Classic zákazníkům před vyřazení. Společnost Microsoft se plánuje poskytovat automatické migraci z ZRS Classic ZRS v budoucnu.

ZRS Classic je dostupná jenom pro objekty BLOB bloku pro obecné účely V1 účty úložiště (GPv1). ZRS Classic asynchronně replikuje data napříč datovými centry v rámci jedné nebo dvou oblastech. Replika nemusí být k dispozici, pokud společnost Microsoft nezahájí převzetí služeb při selhání a přechod na sekundární data. Účet ZRS Classic nelze převést na LRS nebo GRS ani naopak a nepodporuje možnosti metrik ani protokolování.

ZRS klasické účty nelze převést na nebo z LRS, GRS nebo RA-GRS. ZRS klasické účty také nepodporují metriky nebo protokolování.

Jakmile ZRS je všeobecně dostupná v oblasti, už nebudete moct vytvořit účet ZRS Classic z portálu v daném regionu, ale můžete ji vytvořit jiným způsobem, jako jsou prostředí PowerShell, rozhraní příkazového řádku a tak dále.

Pokud chcete ručně migrovat data účtu ZRS do nebo z účtu LRS, ZRS Classic, GRS nebo RA-GRS, použijte AzCopy, Azure Storage Explorer, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. Můžete také vytvořit vlastní migrace řešení s jedním z knihovny klienta Azure Storage.