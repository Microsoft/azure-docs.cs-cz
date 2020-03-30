---
title: Návrh úložiště tabulek Azure pro úpravy dat | Dokumenty společnosti Microsoft
description: Navrhněte tabulky pro úpravy dat v úložišti Azure Table.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771542"
---
# <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tento článek se zaměřuje na aspekty návrhu pro optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které optimalizují pro dotazování proti návrhům, které optimalizují pro úpravy dat stejně jako v návrzích pro relační databáze (ačkoli techniky pro správu kompromisů návrhu jsou v relační databázi). V části Návrhové vzory tabulky popisuje některé podrobné návrhové vzory pro službu Table service a upozorňuje na některé tyto kompromisy. V praxi zjistíte, že mnoho návrhů optimalizovaných pro dotazování entit také dobře funguje pro úpravy entit.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu operací vložení, aktualizace a odstranění
Chcete-li aktualizovat nebo odstranit entitu, musíte být schopni ji identifikovat pomocí hodnot **PartitionKey** a **RowKey.** V tomto ohledu vaše volba **PartitionKey** a **RowKey** pro úpravu entit by měla následovat podobná kritéria, která vaše volba pro podporu bodových dotazů, protože chcete identifikovat entity co nejefektivněji. Nechcete použít neefektivní oddíl nebo prohledávač tabulky k vyhledání entity za účelem zjištění hodnot **PartitionKey** a **RowKey,** které potřebujete k aktualizaci nebo odstranění.  

Následující vzory v části Návrhové vzory tabulky řeší optimalizaci výkonu nebo operací vložení, aktualizace a odstranění:  

* [Vzor odstranění velkého objemu](table-storage-design-patterns.md#high-volume-delete-pattern) – povolí odstranění velkého objemu entit uložením všech entit pro současné odstranění ve vlastní samostatné tabulce; odstraníte entity odstraněním tabulky.  
* [Vzor datových řad](table-storage-design-patterns.md#data-series-pattern) – uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  
* [Vzor širokých entit](table-storage-design-patterns.md#wide-entities-pattern) – k ukládání logických entit s více než 252 vlastnostmi použijte více fyzických entit.  
* [Vzor velkých entit](table-storage-design-patterns.md#large-entities-pattern) – k ukládání velkých hodnot vlastností použijte úložiště objektů blob.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zajištění konzistence uložených entit
Dalším klíčovým faktorem, který ovlivňuje výběr klíčů pro optimalizaci změn dat, je, jak zajistit konzistenci pomocí atomických transakcí. EGT lze použít pouze k provozu s entitami uloženými ve stejném oddílu.  

Následující vzory v článku [Návrhové vzory tabulky](table-storage-design-patterns.md) řeší správu konzistence:  

* [Vzor sekundárního indexu uvnitř oddílu](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – uložte více kopií každé entity pomocí různých hodnot **RowKey** (ve stejném oddílu), abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot **RowKey.**  
* [Vzor sekundárního indexu mezi oddíly](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – uložte více kopií každé entity pomocí různých hodnot RowKey v samostatných oddílech nebo v samostatných tabulkách, abyste povolili rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých hodnot **RowKey.**  
* [Nakonec konzistentní transakce vzor](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – Povolit nakonec konzistentní chování přes hranice oddílu nebo hranice systému úložiště pomocí front Azure.
* [Vzor entit indexu](table-storage-design-patterns.md#index-entities-pattern) – Udržovat entity indexu, aby bylo možné efektivně vyhledávat, které vracejí seznamy entit.  
* [Vzor denormalizace](table-storage-design-patterns.md#denormalization-pattern) – zkombinujte související data do jedné entity, abyste mohli načíst všechna data, která potřebujete, pomocí dotazu s jedním bodem.  
* [Vzor datových řad](table-storage-design-patterns.md#data-series-pattern) – uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

Informace o transakcích skupiny entit naleznete v části [Transakce skupiny entit](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že váš návrh pro efektivní úpravy usnadňuje efektivní dotazy
V mnoha případech návrh pro efektivní dotazování výsledky efektivní změny, ale vždy byste měli vyhodnotit, zda se jedná o tento případ pro konkrétní scénář. Některé vzory v článku [Návrhy tabulek](table-storage-design-patterns.md) explicitně vyhodnocují kompromisy mezi dotazováním a úpravou entit a vždy byste měli vzít v úvahu počet jednotlivých typů operací.  

Následující vzory v článku [Návrhové vzory tabulky](table-storage-design-patterns.md) řeší kompromisy mezi návrhem efektivních dotazů a návrhem pro efektivní úpravy dat:  

* [Vzor složeného klíče](table-storage-design-patterns.md#compound-key-pattern) – Pomocí složených hodnot **RowKey** umožníte klientovi vyhledávat související data pomocí dotazu s jedním bodem.  
* [Vzor ocasu protokolu](table-storage-design-patterns.md#log-tail-pattern) - načíst *n* entity naposledy přidané do oddílu pomocí **RowKey** hodnotu, která seřadí v obráceném pořadí data a času.  
