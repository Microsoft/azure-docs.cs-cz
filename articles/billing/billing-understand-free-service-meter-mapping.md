---
title: Mapování služeb na měřiče pro bezplatný účet Azure
description: Seznamte se s mapováním služeb na měřiče pro služby, které jsou součástí bezplatného účtu.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 00f0db0107cee4a7f52dbf4e04a586c5e7920d61
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709399"
---
# <a name="understand-free-service-to-meter-mapping"></a>Principy mapování bezplatných služeb na měřiče

Každá služba Azure vysílá do měřičů data o využití, na jejichž základě fakturační systém Azure účtuje služby zákazníkům. Pro lepší pochopení využití bezplatných služeb se podívejme na mapování těchto služeb na měřiče. Informace o vytváření bezplatných služeb najdete v tématu [Vytváření bezplatných služeb s využitím bezplatného účtu Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Mapování služeb na měřiče pro oprávněné služby

|    Služba   | Název měřiče na webu Azure Portal | Název měřiče v souboru s informacemi o využití nebo v rozhraní API | ID měření |
| ------------ | -------------------------- | -------------------------| -------- |
| Virtuální počítač B1S s Linuxem | Výpočetní hodiny – Virtuální počítač Standard_B1 | Výpočetní hodiny – Free | 8260cba2-4437-47d1-a31e-2561cd370f50
| Virtuální počítač B1S s Windows | Výpočetní hodiny – Virtuální počítač Standard_B1 (Windows) | Výpočetní hodiny – Free | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| Virtuální počítač B1S – Veřejné IP adresy  | Hodiny IP adresy – Veřejné IP adresy | Hodiny IP adresy – Free | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Úložiště (GB) – Cosmos DB | Úložiště (GB) – Free | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 jednotek žádostí (hodin) – Cosmos DB | 100 jednotek žádostí (hodin) – Free | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| File Storage | Standardní V/V – soubory (GB) – místně redundantní | Standardní V/V – soubory (GB) – Free | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standardní V/V – jednotky operací čtení souborů (v desetitisících) | Standardní V/V – jednotky operací čtení souborů (v desetitisících) – Free | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standardní V/V – jednotky operací zápisu souborů (v desetitisících) | Standardní V/V – jednotky operací zápisu souborů (v desetitisících) – Free | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standardní V/V – jednotky operací protokolu souborů (v desetitisících) | Standardní V/V – jednotky operací protokolu souborů (v desetitisících) – Free | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standardní V/V – jednotky operací seznamu souborů (v desetitisících) | Standardní V/V – jednotky operací seznamu souborů (v desetitisících) – Free | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Úložiště horkých objektů blob bloku | Standardní V/V – operace čtení horkých objektů blob bloku (v desetitisících) | Standardní V/V – operace čtení horkých objektů blob bloku (v desetitisících) – Free |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Úložiště horkých objektů blob bloku | Standardní V/V – horké objekty blob bloku (GB) – místně redundantní | Standardní V/V – horké objekty blob bloku (GB) – Free | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Úložiště horkých objektů blob bloku | Standardní V/V – operace zápisu horkých objektů blob bloku (v desetitisících) | Standardní V/V – operace zápisu horkých objektů blob bloku (v desetitisících) – Free | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Úložiště horkých objektů blob bloku  | Standardní V/V – operace zápisu/výpisu horkých objektů blob bloku (v desetitisících) | Standardní V/V – operace zápisu/výpisu horkých objektů blob bloku (v desetitisících) – Free | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Spravovaný disk <sup>1</sup>  | Standardní spravovaný disk/snímky (GB) – místně redundantní | Standardní spravovaný disk/snímky (GB) – Free | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Spravovaný disk <sup>1</sup>  | Operace spravovaných disků úrovně Standard ( v desetitisících) | Operace spravovaných disků úrovně Standard ( v desetitisících) – Free | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Spravovaný disk <sup>1</sup>  | Premium Storage – objekt blob stránky / P6 (jednotky) – místně redundantní | Premium Storage – objekt blob stránky / P6 (jednotky) – Free | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Dny databáze Standard S0 – SQL Database | Dny databáze Standard S0 – Free | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Sdílený – šířka pásma <sup>2</sup> | Přenos odchozích dat (GB) | Přenos odchozích dat (GB) – Free | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> Pokud vytvoříte virtuální počítač s Windows a zvolíte spravovaný disk, budete v rámci virtuálního počítače využívat měřič spravovaných disků.

<sup>2</sup> Sdílené měřiče může využívat více služeb. Například virtuální počítače i úložiště vysílají data o využití do měřiče Přenos odchozích dat (GB).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Upgrade předplatného](billing-upgrade-azure-subscription.md)
