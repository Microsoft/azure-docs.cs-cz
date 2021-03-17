---
title: Návrh úložiště tabulek Azure pro úpravu dat | Microsoft Docs
description: Navrhněte tabulky pro úpravu dat v úložišti tabulek Azure. Optimalizujte operace vložení, aktualizace a odstranění. Zajistěte konzistenci uložených entit.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236756"
---
# <a name="design-for-data-modification"></a>Návrh pro úpravu dat
Tento článek se zaměřuje na požadavky návrhu na optimalizaci vložení, aktualizace a odstranění. V některých případech budete muset vyhodnotit kompromis mezi návrhy, které se optimalizují pro dotazování na návrhy, které se optimalizují pro úpravu dat stejně jako v návrzích pro relační databáze (i když postupy pro správu kompromisů v návrhu se liší v relační databázi). Vzory návrhu tabulek oddílu popisují některé podrobné vzory návrhu Table service a zvýrazňují některé z těchto kompromisů. V praxi zjistíte, že mnoho návrhů, které jsou optimalizované pro dotazování entit, také dobře fungují pro úpravy entit.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimalizace výkonu operací vložení, aktualizace a odstranění
Aby bylo možné entitu aktualizovat nebo odstranit, je nutné ji identifikovat pomocí hodnot **PartitionKey** a **RowKey** . V tomto ohledu by vaše volba **PartitionKey** a **RowKey** pro úpravy entit měla dodržovat podobná kritéria pro volbu dotazů na bod podpory, protože chcete co nejefektivnější identifikaci entit. Nechcete vyhledat entitu, abyste mohli zjistit hodnoty **PartitionKey** a **RowKey** , které je třeba aktualizovat nebo odstranit, pomocí neefektivního vyhledávání oddílu nebo tabulky.  

Následující vzory vzorů návrhu tabulek řeší optimalizaci výkonu nebo operací vložení, aktualizace a odstranění:  

* [Vzor pro velké objemy odstranění](table-storage-design-patterns.md#high-volume-delete-pattern) – povolí odstranění velkého objemu entit tím, že se všechny entity pro současné odstranění uloží do vlastní samostatné tabulky. Odstraňte entity odstraněním tabulky.  
* [Vzor datových řad](table-storage-design-patterns.md#data-series-pattern) – uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  
* [Model pro nejrůznější entity](table-storage-design-patterns.md#wide-entities-pattern) – pomocí více fyzických entit můžete ukládat logické entity s více než 252 vlastnostmi.  
* [Vzor velkých entit](table-storage-design-patterns.md#large-entities-pattern) – pomocí úložiště objektů blob můžete ukládat velké hodnoty vlastností.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zajištění konzistence uložených entit
Druhý faktor klíče, který má vliv na volbu klíčů pro optimalizaci úprav dat, je způsob, jak zajistit konzistenci pomocí atomických transakcí. EGT můžete použít jenom pro práci s entitami uloženými ve stejném oddílu.  

Následující vzory v [vzorcích návrhu tabulek](table-storage-design-patterns.md) článků řeší konzistenci správy:  

* [Vzor sekundárního indexu v rámci oddílu](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – pomocí různých hodnot **RowKey** můžete pomocí různých hodnot **RowKey** (ve stejném oddílu) ukládat víc kopií každé entity, aby se povolily rychlé a efektivní vyhledávání a alternativní objednávky řazení.  
* [Vzor sekundárního indexu mezi oddíly](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – pomocí různých hodnot **RowKey** můžete ukládat několik kopií každé entity s použitím různých hodnot RowKey v samostatných oddílech nebo v samostatných tabulkách, abyste mohli rychle a efektivně vyhledávat a alternativní objednávky řazení.  
* [Vzor a nakonec konzistentní transakce](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – umožňují v rámci hranic oddílů nebo hranic systému úložiště použít fronty Azure.
* [Index entity vzor](table-storage-design-patterns.md#index-entities-pattern) – Udržujte entity indexu, které umožňují efektivní hledání, které vrací seznam entit.  
* [Nenormalizovaný vzor](table-storage-design-patterns.md#denormalization-pattern) – kombinuje související data společně v jedné entitě, aby bylo možné načíst všechna potřebná data pomocí dotazu s jedním bodem.  
* [Vzor datových řad](table-storage-design-patterns.md#data-series-pattern) – uložte kompletní datové řady do jedné entity, abyste minimalizovali počet požadavků, které provedete.  

Informace o transakcích skupin entit najdete v oddílu [transakce skupin entit](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Ujistěte se, že návrh efektivních úprav usnadňuje efektivní dotazy.
V mnoha případech je návrh účinného dotazování v důsledku efektivních úprav, ale měli byste vždycky vyhodnotit, jestli se jedná o případ konkrétního scénáře. Některé ze vzorů v [vzorcích návrhu tabulek](table-storage-design-patterns.md) článků explicitně vyhodnocují kompromisy mezi dotazování a úpravou entit a měli byste vždycky vzít v úvahu počet jednotlivých typů operací.  

V následujících vzorcích jsou [vzory návrhu tabulek](table-storage-design-patterns.md) článků řeší kompromisy mezi návrhem pro efektivní dotazy a návrhem pro efektivní úpravu dat:  

* [Vzor složeného klíče](table-storage-design-patterns.md#compound-key-pattern) – pomocí složených hodnot **RowKey** můžete klientovi povolit vyhledávání souvisejících dat pomocí dotazu s jedním bodem.  
* [Vzor funkce log Tail](table-storage-design-patterns.md#log-tail-pattern) – načte entity *n* naposledy přidané do oddílu pomocí hodnoty **RowKey** , která se seřadí v pořadí podle data a času.  
