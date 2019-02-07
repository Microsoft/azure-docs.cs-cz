---
title: Návrh tabulky pro úpravu dat v úložišti Azure | Dokumentace Microsoftu
description: Návrh tabulek pro úpravu dat ve službě Azure table storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: e993d169025f9b76c5e813bae31ca6cb2a39ba71
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809512"
---
# <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tento článek se zaměřuje na aspekty návrhu pro optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které je optimální pro dotazování na návrhy, které je optimální pro úpravu dat stejně jako je tomu v návrhy pro relační databáze (i když jsou techniky pro správu kompromisy návrhu různé v relační databázi). Vzory návrhu tabulky část popisuje některé podrobné návrhové vzory pro služby Table service a zdůrazňuje některé tyto nevýhody. V praxi zjistíte, že řada návrhů, které jsou optimalizované pro dotazování entity také fungovat dobře pro úpravu entity.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu insert, update a operace odstranění
Aktualizovat nebo odstranit entitu, musíte být schopni identifikovat pomocí **PartitionKey** a **RowKey** hodnoty. V tomto ohledu podle vaší volby **PartitionKey** a **RowKey** úprava entit by podle podobných kritérií zadaným hodnotám pro podporu bodových dotazů, protože chcete, k identifikaci entity jako efektivní nejvíce. Nechcete použijte neefektivní kontroly oddíl nebo tabulky a vyhledejte entitu Chcete-li vyhledat **PartitionKey** a **RowKey** hodnoty je potřeba aktualizovat nebo odstranit.  

Následující vzory v části Adresa vzory návrhu tabulky optimalizace výkonu nebo vložení, aktualizace a odstranění operace:  

* [Velký objem odstranit vzor](table-storage-design-patterns.md#high-volume-delete-pattern) -Povolit odstranění k velkému počtu entit uložením všechny entity pro souběžné odstranění vlastních samostatné tabulky, odstranit entity odstraněním tabulky.  
* [Vzor data řady](table-storage-design-patterns.md#data-series-pattern) -series kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  
* [Vzor široké entity](table-storage-design-patterns.md#wide-entities-pattern) – použití více fyzických entit k ukládání logické entity s více než 252 vlastností.  
* [Vzor velkých entit](table-storage-design-patterns.md#large-entities-pattern) – použití úložiště objektů blob pro uložení hodnoty vlastnosti velké.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zajistit konzistenci v entitách uložené
Klíčovým faktorem, který ovlivňuje podle vašeho výběru klíče pro optimalizaci změny dat je jak zajistit soulad s použitím atomické transakce. Můžete použít pouze EGT pracovat u entit se ukládají do stejného oddílu.  

Následující vzory v článku [vzory návrhu v tabulce](table-storage-design-patterns.md) adresu Správa konzistence:  

* [Vzor sekundární index oddílu uvnitř](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty.  
* [Sekundární index oddílu mezi vzor](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store několik kopií každé entity pomocí různých hodnot RowKey v samostatných oddílech nebo v samostatných tabulkách a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých **RowKey** hodnoty.  
* [Vzor konečnou konzistenci transakcí](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -povolit konzistentní chování v rámci hranic oddílů nebo systému hranice úložiště pomocí front Azure.
* [Model entity indexů](table-storage-design-patterns.md#index-entities-pattern) -udržovat index entity, které umožňují efektivní prohledávání, které vrací seznam entit.  
* [Vzor denormalizace](table-storage-design-patterns.md#denormalization-pattern) -kombinování souvisejících dat společně v jedné entitě umožňuje načíst všechna data, je nutné pomocí dotazu jediný bod.  
* [Vzor data řady](table-storage-design-patterns.md#data-series-pattern) -series kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

Informace o transakcí skupin entit, najdete v části [transakcí skupin entit](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že váš návrh pro efektivní úpravy zajišťuje efektivní dotazy
V mnoha případech by měl návrh pro efektivní dotazování výsledků v efektivní úpravy, ale vždy vyhodnoťte, jestli to platí pro váš konkrétní scénář. Některé tyto vzory se dají v článku [vzory návrhu v tabulce](table-storage-design-patterns.md) explicitně vyhodnotit kompromisy mezi dotazování a úprava entit a by měl vždy vzít v úvahu počty jednotlivých typů operací.  

Následující vzory v článku [vzory návrhu v tabulce](table-storage-design-patterns.md) kompromisy mezi návrhu pro efektivní dotazy a návrh pro úpravu dat efektivní adresa:  

* [Složené klíče vzor](table-storage-design-patterns.md#compound-key-pattern) – použití složené **RowKey** hodnoty umožňují klientům vyhledat související data pomocí dotazu jediný bod.  
* [Vzor log tail](table-storage-design-patterns.md#log-tail-pattern) – načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  
