---
title: Pokyny pro návrh tabulky Azure Storage | Microsoft Docs
description: Seznamte se s pokyny pro návrh služby Table Storage Azure, aby bylo možné efektivně podporovat operace čtení a zápisu.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236314"
---
# <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek

Navrhování tabulek pro použití s tabulkovou službou Azure Storage se velmi liší od hledisek návrhu relační databáze. Tento článek popisuje pokyny pro návrh řešení Table service, aby bylo možné je efektivně číst a efektivně zapisovat.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Navrhněte své řešení Table service, aby bylo možné je efektivně číst.

* ***Návrh pro dotazování v aplikacích s vysokým oprávněním*** Když navrhujete tabulky, zamyslete se nad dotazy (zejména s citlivostí na latenci), kterou budete spouštět před tím, než si myslíte, jak budete entity aktualizovat. To obvykle vede k efektivnímu a výkonnému řešení.  
* ***V dotazech zadejte jak PartitionKey, tak RowKey.** _ _Point dotazů * jako jsou to nejúčinnější dotazy služby Table Service.  
* ***Zvažte uložení duplicitních kopií entit.*** Úložiště tabulek je levné, proto zvažte uložení stejné entity vícekrát (s různými klíči), čímž umožníte efektivnější dotazy.  
* ***Zvažte denormalizaci vašich dat.*** Úložiště tabulek je levné, takže zvažte denormalizaci vašich dat. Můžete například uložit souhrnné entity, aby dotazy na agregovaná data měly jenom přístup k jedné entitě.  
* ***Použijte hodnoty složených klíčů.** _ Jsou to jediné klíče _ *PartitionKey** a **RowKey**. Použijte například hodnoty složeného klíče k povolení alternativních přístupových cest k entitám.  
* ***Použití projekce dotazu.*** Množství dat přenášených přes síť můžete snížit pomocí dotazů, které vyberou pouze pole, která potřebujete.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Návrh Table service řešení pro zápis  

* ***Nevytvářejte aktivní oddíly.*** Vyberte klíče, které vám umožní rozdělit požadavky do více oddílů kdykoli.  
* ***Nepoužívejte špičky v provozu.*** Vyhladení provozu v přiměřené době a zabraňte špičkám v provozu.
* ***Nevytvářejte nutně samostatnou tabulku pro každý typ entity.*** Pokud požadujete atomické transakce napříč typy entit, můžete tyto typy entit Uložit do stejného oddílu ve stejné tabulce.
* ***Vezměte v úvahu maximální propustnost, kterou je třeba dosáhnout.*** Musíte znát cíle škálovatelnosti pro Table service a zajistit, aby vám návrh nezpůsobí, že byste je překročili.  

Při čtení tohoto průvodce uvidíte příklady, které všechny tyto zásady zařadí do praxe. 

## <a name="next-steps"></a>Další kroky

- [Způsoby návrhu tabulek](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrovat data tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
