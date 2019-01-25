---
title: Služby pro mapování měřičů pro bezplatného účtu Azure | Dokumentace Microsoftu
description: Principy mapování měřičů pro služeb zahrnutých v bezplatném účtu služby.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 8a6993f19f32e1bdb0bd10013f8f7e3dde844edd
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903923"
---
# <a name="understand-free-service-to-meter-mapping"></a>Principy mapování měřičů bezplatná služba

Každé služby Azure vydává využití podle měřičů, které Azure fakturační systém využívá strhávat uživatelé služby. Abyste lépe pochopili využití bezplatných služeb, Podívejme se na službu pro mapování měřičů pro tyto služby. Zjistěte, jak vytvořit bezplatné služby, najdete v článku [vytvořit bezplatné služby s bezplatným účtem Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Služby k měření mapování zdarma účet opravňující služby 

|    Služba   | Název měřiče na portálu Azure portal | Název měřiče v souboru využití nebo rozhraní API | ID měření |
| ------------ | -------------------------- | -------------------------| -------- |
| Virtuální počítač s Linuxem B1S | Výpočetní hodiny – Standard_B1 virtuálního počítače | Výpočetní hodiny – Free | 8260cba2-4437-47d1-a31e-2561cd370f50
| Windows virtuálního počítače B1S | Výpočetní hodiny – Standard_B1 virtuálního počítače (Windows) | Výpočetní hodiny – Free | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S virtuálního počítače – veřejné IP adresy  | Hodiny IP adresy – veřejné IP adresy | Hodiny IP adresy – Free | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Úložiště (GB) – služby Cosmos DB | Storage (GB) – Free | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | Jednotky zahrnující 100 žádostí (hodiny) – služby Cosmos DB | Jednotky zahrnující 100 žádostí (hodiny) – Free | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standardní v/v – soubory (GB) – místně redundantní | Standardní v/v – soubory (GB) – Free | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standardní V/V – jednotky operací čtení souborů (v desetitisících) | Standardní v/v – jednotky operací čtení souborů (v 10,000s) – Free | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standardní V/V – jednotky operací zápisu souborů (v desetitisících) | Standardní v/v – soubor jednotky operací zápisu (v 10,000s) – Free | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standardní V/V – jednotky operací protokolu souborů (v desetitisících) | Standardní v/v – jednotky protokolu souborů operace (v 10,000s) – Free | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standardní V/V – jednotky operací seznamu souborů (v desetitisících) | Standardní v/v – soubor jednotky operací seznamu (v 10,000s) – Free | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Úložiště objektů Blob bloku | Standardní v/v – operace čtení horkých objektů Blob bloku (v 10,000s) | Standardní v/v – horký objekt Blob bloku přečíst operace (v 10,000s) – Free |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Úložiště objektů Blob bloku | Standardní v/v – horký objekt Blob bloku (GB) – místně redundantní | Standardní v/v – horký objekt Blob bloku (GB) – Free | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Úložiště objektů Blob bloku | Standardní v/v – operace zápisu objektů Blob bloku (v 10,000s) | Standardní v/v – objekt Blob operace zápisu horkých bloku (v 10,000s) – Free | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Úložiště objektů Blob bloku  | Standardní v/v – operace zápisu/výpisu objektů Blob bloku (v 10,000s) | Standardní v/v – horké operace objektů Blob bloku zápisu/výpisu (v 10,000s) – Free | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Spravovaný Disk *  | Standardní spravovaný Disk/snímky (GB) – místně redundantní | Standardní spravovaný Disk/snímky (GB) – Free | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Spravovaný Disk *  | Operace spravovaných disků úrovně Standard (v 10,000s) | Operace spravovaných disků úrovně Standard (v 10,000s) – Free | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Spravovaný Disk *  | Storage úrovně Premium – objekt Blob stránky/P6 (jednotky) – místně redundantní | Storage úrovně Premium – objekt Blob stránky/P6 (jednotky) – Free | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Dny databáze Standard S0 – SQL Database | Počet dnů používání databáze Standard S0 – Free | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Na úrovni Shared – šířky pásma ** | Odchozí přenosy dat (GB) | Přenos dat si (GB) – Free | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Je-li vytvořit virtuální počítač s Windows a zvolte spravovaný disk, budete využívat měřiče spravovaného disku v rámci virtuálního počítače.

\** Přes několik služeb, které mohou být spotřebovány sdílené měřiče. Například virtuálních počítačů a úložiště generování využití proti Out(GB) přenosu dat měřidla.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
