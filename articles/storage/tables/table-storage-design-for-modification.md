---
title: Návrh tabulky úložiště Azure pro úpravu dat | Microsoft Docs
description: Návrh tabulky pro úpravu dat ve službě Azure table storage.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 6c008175f01521ce4f96d13e58244dc72d9f6990
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660760"
---
# <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tento článek se zaměřuje na aspekty návrhu pro optimalizaci vložení, aktualizace a odstraní. V některých případech musíte vyhodnotit kompromis mezi návrhů, které je optimální pro dotazování na návrhů, které je optimální pro úpravu dat stejným způsobem jako v návrhy pro relační databáze (i když techniky pro správu kompromisy návrhu se liší v relační databázi). V části [vzory návrhu tabulky](#table-design-patterns) popisuje některé vzory podrobné návrhu pro službu tabulky a zvýrazňuje některé tyto kompromis. V praxi zjistíte, že řada návrhů optimalizované pro dotazování entity také fungovat i pro úpravy entity.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu insert, update a operace odstranění
Aktualizace nebo odstranění entity, musí být schopen identifikovat pomocí **PartitionKey** a **RowKey** hodnoty. V tomto ohledu vaši volbu **PartitionKey** a **RowKey** pro úpravy entity postupujte podobné kritéria na požadavek na podporu dotazů bod, protože chcete co možná nejefektivnější identifikovat entity. Nechcete pomocí neefektivní kontroly oddíl nebo tabulka, chcete-li vyhledat vyhledejte entitu **PartitionKey** a **RowKey** hodnoty, je potřeba aktualizovat nebo odstranit.  

Tyto vzory v části [vzory návrhu tabulky](#table-design-patterns) adres optimalizace výkonu nebo vaší insert, update a operace odstranění:  

* [Velkému odstranit vzor](table-storage-design-patterns.md#high-volume-delete-pattern) -Povolit odstranění k velkému počtu entity uložením všechny entity pro souběžné odstranění vlastní samostatné tabulky; odstranit entity odstraněním v tabulce.  
* [Vzorek dat řady](table-storage-design-patterns.md#data-series-pattern) -úložiště dokončení datové řady v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  
* [Vzor široké entity](table-storage-design-patterns.md#wide-entities-pattern) -používat více fyzických entit k uložení logických entit s více než 252 vlastností.  
* [Vzor velké entity](table-storage-design-patterns.md#large-entities-pattern) -použití úložiště objektů blob k ukládání velkých vlastnost hodnoty.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zajistit konzistenci vaše uložené entity
Další klíčovým faktorem, který ovlivňuje vaši volbu klíče pro optimalizaci změny dat je zajištění souladu s použitím jednotlivé transakce. Můžete použít pouze EGT pracovat na entity, které jsou uložené ve stejném oddílu.  

Tyto vzory v článku [vzory návrhu tabulky](table-storage-design-patterns.md) adresu Správa konzistence:  

* [Vzor sekundární index Intra-partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -uložit více kopií každou entitu s využitím různých **RowKey** hodnoty (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení pomocí různých **RowKey** hodnoty.  
* [Vzor mezi oddíl sekundární index](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – ukládání více kopií každé entity pomocí různých hodnot RowKey v samostatné oddíly nebo v samostatných tabulkách umožňující rychlé a efektivní vyhledávání a řazení alternativní řadí pomocí různých **RowKey** hodnoty.  
* [Nakonec byl konzistentní transakce vzor](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -povolit nakonec byl konzistentní chování v rámci hranice oddílů nebo hranice systému úložiště pomocí front Azure.
* [Index entity vzor](table-storage-design-patterns.md#index-entities-pattern) -udržovat index entity umožňující efektivní hledání, které vrátí seznamy entit.  
* [Vzor denormalization](table-storage-design-patterns.md#denormalization-pattern) -zkombinujte související data společně v jedné entity, které vám umožňují načíst všechna data, musíte pomocí dotazu jediný bod.  
* [Vzorek dat řady](table-storage-design-patterns.md#data-series-pattern) -úložiště dokončení datové řady v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

Informace o transakcích skupiny entity, najdete v části [Entity skupiny transakce](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že návrhu pro efektivní úpravy usnadňuje efektivní dotazy
V mnoha případech by měl návrh pro efektivní dotazování výsledků v efektivní úpravy, ale vždy vyhodnocení, zda toto platí pro konkrétní scénář. Některé vzory v článku [vzory návrhu tabulky](table-storage-design-patterns.md) explicitně vyhodnocení kompromis mezi dotazování a úprava entity a byste měli vždy vzít v úvahu počet jednotlivých typů operace.  

Tyto vzory v článku [vzory návrhu tabulky](table-storage-design-patterns.md) adres kompromis mezi návrhu pro efektivní dotazy a návrhu pro úpravu efektivní dat:  

* [Složené klíče vzor](table-storage-design-patterns.md#compound-key-pattern) -použití složené **RowKey** hodnoty, aby klient k vyhledání souvisejících dat pomocí dotazu jediný bod.  
* [Vzor protokolu poškozené databáze](table-storage-design-patterns.md#log-tail-pattern) -načíst *n* entity naposledy přidané do oddílu pomocí **RowKey** hodnotu, která seřadí zpětné datum a čas pořadí.  
