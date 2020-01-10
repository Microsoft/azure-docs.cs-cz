---
title: Návrh úložiště tabulek Azure pro úpravu dat | Microsoft Docs
description: Navrhněte tabulky pro úpravu dat v úložišti tabulek Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771542"
---
# <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tento článek se zaměřuje na požadavky návrhu na optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které je optimální pro dotazování na návrhy, které je optimální pro úpravu dat stejně jako je tomu v návrhy pro relační databáze (i když jsou techniky pro správu kompromisy návrhu různé v relační databázi). Vzory návrhu tabulek oddílu popisují některé podrobné vzory návrhu Table service a zvýrazňují některé z těchto kompromisů. V praxi zjistíte, že řada návrhů, které jsou optimalizované pro dotazování entity také fungovat dobře pro úpravu entity.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu operací vložení, aktualizace a odstranění
Aktualizovat nebo odstranit entitu, musíte být schopni identifikovat pomocí **PartitionKey** a **RowKey** hodnoty. V tomto ohledu podle vaší volby **PartitionKey** a **RowKey** úprava entit by podle podobných kritérií zadaným hodnotám pro podporu bodových dotazů, protože chcete, k identifikaci entity jako efektivní nejvíce. Nechcete použijte neefektivní kontroly oddíl nebo tabulky a vyhledejte entitu Chcete-li vyhledat **PartitionKey** a **RowKey** hodnoty je potřeba aktualizovat nebo odstranit.  

Následující vzory vzorů návrhu tabulek řeší optimalizaci výkonu nebo operací vložení, aktualizace a odstranění:  

* [Velký objem odstranit vzor](table-storage-design-patterns.md#high-volume-delete-pattern) -Povolit odstranění k velkému počtu entit uložením všechny entity pro souběžné odstranění vlastních samostatné tabulky, odstranit entity odstraněním tabulky.  
* [Vzor data řady](table-storage-design-patterns.md#data-series-pattern) -series kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  
* [Vzor široké entity](table-storage-design-patterns.md#wide-entities-pattern) – použití více fyzických entit k ukládání logické entity s více než 252 vlastností.  
* [Vzor velkých entit](table-storage-design-patterns.md#large-entities-pattern) – použití úložiště objektů blob pro uložení hodnoty vlastnosti velké.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zajištění konzistence uložených entit
Klíčovým faktorem, který ovlivňuje podle vašeho výběru klíče pro optimalizaci změny dat je jak zajistit soulad s použitím atomické transakce. Můžete použít pouze EGT pracovat u entit se ukládají do stejného oddílu.  

Následující vzory v [vzorcích návrhu tabulek](table-storage-design-patterns.md) článků řeší konzistenci správy:  

* [Vzor sekundární index oddílu uvnitř](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store několik kopií každého entitu s využitím různých **RowKey** hodnot (ve stejném oddílu) Chcete-li povolit rychlé a efektivní vyhledávání a alternativní pořadí řazení s použitím různých **RowKey** hodnoty.  
* [Sekundární index oddílu mezi vzor](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store několik kopií každé entity pomocí různých hodnot RowKey v samostatných oddílech nebo v samostatných tabulkách a umožňuje rychlé a efektivní vyhledávání a řazení alternativní objednávky s použitím různých **RowKey** hodnoty.  
* [Vzor konečnou konzistenci transakcí](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -povolit konzistentní chování v rámci hranic oddílů nebo systému hranice úložiště pomocí front Azure.
* [Index entity vzor](table-storage-design-patterns.md#index-entities-pattern) – Udržujte entity indexu, které umožňují efektivní hledání, které vrací seznam entit.  
* [Vzor denormalizace](table-storage-design-patterns.md#denormalization-pattern) -kombinování souvisejících dat společně v jedné entitě umožňuje načíst všechna data, je nutné pomocí dotazu jediný bod.  
* [Vzor data řady](table-storage-design-patterns.md#data-series-pattern) -series kompletní data Store v jedné entity, chcete-li minimalizovat počet požadavků, které provedete.  

Informace o transakcích skupin entit najdete v oddílu [transakce skupin entit](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že návrh efektivních úprav usnadňuje efektivní dotazy.
V mnoha případech by měl návrh pro efektivní dotazování výsledků v efektivní úpravy, ale vždy vyhodnoťte, jestli to platí pro váš konkrétní scénář. Některé ze vzorů v [vzorcích návrhu tabulek](table-storage-design-patterns.md) článků explicitně vyhodnocují kompromisy mezi dotazování a úpravou entit a měli byste vždycky vzít v úvahu počet jednotlivých typů operací.  

V následujících vzorcích jsou [vzory návrhu tabulek](table-storage-design-patterns.md) článků řeší kompromisy mezi návrhem pro efektivní dotazy a návrhem pro efektivní úpravu dat:  

* [Vzor složeného klíče](table-storage-design-patterns.md#compound-key-pattern) – pomocí složených hodnot **RowKey** můžete klientovi povolit vyhledávání souvisejících dat pomocí dotazu s jedním bodem.  
* [Vzor log tail](table-storage-design-patterns.md#log-tail-pattern) – načíst *n* naposledy přidaný do oddílu s použitím entity **RowKey** hodnotu, která seřadí reverzní datum a čas objednávky.  
