---
title: Podpora řazení
description: Podpora typů řazení pro synapse SQL ve službě Azure synapse Analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93311918"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Podpora řazení databáze pro synapse SQL ve službě Azure synapse Analytics 

Kolace poskytují národní prostředí, znakovou stránku, pořadí řazení a pravidla citlivosti znaků pro datové typy založené na znacích. Po výběru budou všechny sloupce a výrazy, které vyžadují informace o kolaci, dědit zvolenou kolaci z nastavení databáze. Výchozí dědičnost lze přepsat explicitním uvedením jiné kolace pro datový typ založený na znacích.

Výchozí kolaci databáze můžete změnit z Azure Portal při vytváření nové vyhrazené databáze fondu SQL. Díky této možnosti je ještě snazší vytvořit novou databázi pomocí jedné z podporovaných databázových kola 3800.

Výchozí kolaci databáze fondu SQL bez serveru můžete zadat při vytváření pomocí příkazu CREATE DATABASE.

## <a name="change-collation"></a>Změnit kolaci
Pokud chcete změnit výchozí kolaci pro vyhrazenou databázi fondu SQL, aktualizujte pole kolace v prostředí zřizování. Například pokud jste chtěli změnit výchozí kolaci na rozlišení velkých a malých písmen, přejmenujte kolaci z SQL_Latin1_General_CP1_CI_AS na SQL_Latin1_General_CP1_CS_AS. 

Chcete-li změnit výchozí kolaci pro databázi fondu SQL bez serveru, můžete použít příkaz ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Seznam nepodporovaných typů kolace
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

Vyhrazený fond SQL navíc nepodporuje následující typy kolace:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Zkontroluje aktuální kolaci.
Chcete-li zjistit aktuální kolaci pro databázi, můžete spustit následující fragment T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Když předáte "kolaci" jako parametr vlastnosti, funkce DatabasePropertyEx vrátí aktuální kolaci pro zadanou databázi. Další informace o funkci DatabasePropertyEx najdete na webu MSDN.

## <a name="next-steps"></a>Další kroky

Další informace o osvědčených postupech pro vyhrazený fond SQL a SQL Server bez serveru najdete v následujících článcích:

- [Osvědčené postupy pro vyhrazený fond SQL](best-practices-sql-pool.md)
- [Osvědčené postupy pro fond SQL bez serveru](best-practices-sql-on-demand.md)


