---
title: Služby k měření mapování pro účet Azure zdarma | Microsoft Docs
description: Pochopení služby k měření mapování pro služby zahrnuté do bezplatný účet.
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
ms.author: amberb
ms.openlocfilehash: 3019fe5f8b44eaa32d4ab309f296d6b4574df1d3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061366"
---
# <a name="understand-free-service-to-meter-mapping"></a>Pochopení bezplatné služby k měření mapování

Všechny služby Azure vysílá využití proti motory, které Azure fakturačních systémů využívá k účtují uživatele pro služby. Abyste lépe pochopili využití bezplatné služby, podíváme se na službu, kterou chcete monitorovat mapování těchto služeb. Naučte se vytvářet bezplatné služby, najdete v tématu [vytvořit bezplatné služby s bezplatný účet Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Služby k měření mapování zdarma účet oprávněných služeb 

|    Služba   | Měřicí název na portálu Azure | Název měřidla v souboru využití nebo rozhraní API | ID měření |
| ------------ | -------------------------- | -------------------------| -------- |
| Virtuální počítač s Linuxem B1S | Výpočetní hodiny - Standard_B1 virtuálních počítačů | Výpočetní čas - volné | 8260cba2-4437-47d1-a31e-2561cd370f50
| Virtuální počítač s Windows B1S | Výpočetní hodiny - Standard_B1 virtuální počítač (Windows) | Výpočetní čas - volné | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S virtuálního počítače – veřejné IP adresy  | Hodiny IP adresy - veřejné IP adresy | Hodiny IP adresy - volné | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Úložiště (GB) – Cosmos DB | Úložiště (GB) – volné | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | Požadované jednotky 100 (hodiny) - Cosmos DB | Požadované jednotky 100 (hodiny) - volné | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standardní v/v – soubory (GB) – místně redundantní | Standardní v/v – soubory (GB) – volné | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standardní V/V – jednotky operací čtení souborů (v desetitisících) | Standardní v/v – soubor pro čtení jednotky operací (v 10,000s) - volné | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standardní V/V – jednotky operací zápisu souborů (v desetitisících) | Standardní v/v – soubor jednotky operací zápisu (v 10,000s) - volné | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standardní V/V – jednotky operací protokolu souborů (v desetitisících) | Standardní v/v – jednotky operací protokolu souborů (v 10,000s) - volné | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standardní V/V – jednotky operací seznamu souborů (v desetitisících) | Standardní v/v – jednotky operací seznamu souborů (v 10,000s) - volné | e8ae79ad-c2ab-4D82-b226-dd3c33dfd40c
| Úložiště objektů Blob bloku aktivní | Standardní v/v – objekt Blob bloku aktivní operace čtení (v 10,000s) | Standardní v/v – objekt Blob bloku aktivní operace (v 10,000s) - volné čtení |fd7cfa1e-026e-4BE1-871b-1c2386e8902e
| Úložiště objektů Blob bloku aktivní | Standardní v/v – aktivní objekt Blob bloku (GB) – místně redundantní | Standardní v/v – aktivní objekt Blob bloku (GB) – volné | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Úložiště objektů Blob bloku aktivní | Standardní v/v – operace zápisu objektů Blob aktivní bloku (v 10,000s) | Standardní v/v – aktivní operace objektů Blob bloku zápisu (v 10,000s) - volné | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Úložiště objektů Blob bloku aktivní  | Standardní v/v – operace zápisu nebo seznamu objektů Blob aktivní bloku (v 10,000s) | Standardní v/v – aktivní operace objektů Blob bloku zápisu nebo jejich výpisu (v 10,000s) - volné | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Spravovaný Disk *  | Standardní spravovaného disku/snímky (GB) – místně redundantní | Standardní spravovaného disku/snímky (GB) – volné | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Spravovaný Disk *  | Operace spravovaných disků na úrovni Standard (v 10,000s) | Operace spravovaných disků na úrovni Standard (v 10,000s) - volné | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Spravovaný Disk *  | Storage úrovně Premium – stránka objektů Blob nebo P6 (jednotky) - místně redundantní | Storage úrovně Premium – stránka objektů Blob nebo P6 (jednotky) - volné | 2b98c168-27CA-4cc1-B509-e887dec87657
| SQL Database | Dny databáze Standard S0 – databáze SQL | Dny databáze Standard S0 - volné | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Sdílené - šířky pásma ** | Odchozí přenosy dat (GB) | Přenos dat sítěmi (GB) – volné | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Pokud chcete vytvořit virtuálního počítače s Windows a vyberte spravovaného disku, bude využívat měření spravovaného disku v rámci virtuálního počítače.

\** Sdílené měřidla, mohou být využívány prostřednictvím více služeb. Virtuální počítače a úložiště pro instanci emitování proti Out(GB) přenosu dat měření využití.





## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
