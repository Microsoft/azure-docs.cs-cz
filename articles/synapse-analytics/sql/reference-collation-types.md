---
title: Kolace
description: Typy řazení podporované v Azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431537"
---
# <a name="database-collation-support-for-synapse-sql"></a>Podpora řazení databází pro Synapse SQL

Řazení poskytují národní prostředí, znakovou stránku, pořadí řazení a pravidla citlivosti znaků pro datové typy založené na znaku. Po výběru všechny sloupce a výrazy, které vyžadují informace o řazení, zdědí vybrané řazení z nastavení databáze. Výchozí dědičnost lze přepsat explicitním uvedením jiného kolace pro datový typ založený na znaku.

Při vytváření nové databáze fondu SQL můžete změnit výchozí řazení databáze z portálu Azure. Tato funkce umožňuje ještě více usnadnit vytvoření nové databáze pomocí jednoho z 3800 podporovaných databází kolace.

Můžete zadat výchozí synapse SQL na vyžádání databáze řazení v době vytvoření pomocí příkazu CREATE DATABASE.

## <a name="changing-collation"></a>Změna řazení
Chcete-li změnit výchozí řazení pro databázi fondu SQL, můžete jednoduché aktualizace pole Kolace v prostředí zřizování. Například pokud chcete změnit výchozí řazení na malá a velká písmena, jednoduše přejmenovat řazení z SQL_Latin1_General_CP1_CI_AS na SQL_Latin1_General_CP1_CS_AS. 

Chcete-li změnit výchozí řazení pro databázi NA Vyžádání, můžete použít příkaz ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Seznam nepodporovaných typů řazení
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Fond SQL navíc nepodporuje následující typy řazení:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Kontrola aktuálního kolace
Chcete-li zkontrolovat aktuální řazení databáze, můžete spustit následující fragment T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Při předání 'Kolaation' jako parametr vlastnosti, DatabasePropertyEx funkce vrátí aktuální řazení pro zadanou databázi. Další informace o funkci DatabasePropertyEx na msdn.

## <a name="next-steps"></a>Další kroky

Další informace o doporučených postupech pro fond SQL a SQL na vyžádání naleznete v následujících článcích:

- [Doporučené postupy pro fond SQL](best-practices-sql-pool.md)
- [Doporučené postupy pro SQL na vyžádání](best-practices-sql-on-demand.md)


