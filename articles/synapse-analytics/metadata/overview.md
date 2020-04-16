---
title: Model sdílených metadat Azure Synapse Analytics
description: Azure Synapse Analytics umožňuje různým výpočetním modulům pracovního prostoru sdílet databáze a tabulky mezi fondy Spark (preview), sql on-demand engine (preview) a fondy SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424115"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Sdílená metadata Azure Synapse Analytics

Azure Synapse Analytics umožňuje různým výpočetním modulům pracovního prostoru sdílet databáze a tabulky mezi fondy Spark (preview), sql on-demand engine (preview) a fondy SQL.

[!INCLUDE [preview](../includes/note-preview.md)]



Sdílení podporuje takzvaný moderní vzor datového skladu a poskytuje modulu SQL v pracovním prostoru přístup k databázím a tabulkám vytvořeným pomocí Spark. Také umožňuje sql motory vytvářet vlastní objekty, které nejsou sdíleny s ostatními motory.

## <a name="support-the-modern-data-warehouse"></a>Podpora moderního datového skladu

Model sdílených metadat podporuje moderní vzor datového skladu následujícím způsobem:

1. Data z datového jezera jsou připravena a strukturována efektivně pomocí Spark ukládáním připravených dat do (případně rozdělených) tabulek podporovaných parketami obsažených v několika databázích.

2. Vytvořené databáze Spark a všechny jejich tabulky se stanou viditelnými v některé z instancí fondu fondu Azure Synapse Spark a lze je použít z libovolné úlohy Spark. Tato funkce podléhá [oprávnění,](#security-model-at-a-glance) protože všechny fondy Spark v pracovním prostoru sdílejí stejné základní úložiště meta katalogů.

3. Vytvořené databáze Spark a jejich parkety-couval tabulky stanou viditelné v pracovním prostoru SQL na vyžádání motoru. [Databáze](database.md) jsou vytvářeny automaticky v metadatech SQL na vyžádání a [externí i spravované tabulky](table.md) vytvořené úlohou Spark jsou přístupné jako `dbo` externí tabulky v metadatech SQL na vyžádání ve schématu odpovídající databáze. <!--For more details, see [ADD LINK].-->

4. Pokud jsou v pracovním prostoru instance fondu SQL, které mají povolenou synchronizaci metadat <!--[ADD LINK]--> nebo pokud je vytvořena nová instance fondu SQL s povolenou synchronizací metadat, vytvořené databáze Spark a jejich tabulky zálohované parketami budou automaticky namapovány do databáze fondu SQL, jak je popsáno ve [sdílené databázi Azure Synapse Analytics](database.md).

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synchronizace objektů probíhá asynchronně. Objekty budou mít mírné zpoždění několik sekund, dokud se nezobrazí v kontextu SQL. Jakmile se zobrazí, mohou být dotazovány, ale nejsou aktualizovány ani změněny moduly SQL, které k nim mají přístup.

## <a name="which-metadata-objects-are-shared"></a>Které objekty metadat jsou sdíleny

Spark umožňuje vytvářet databáze, externí tabulky, spravované tabulky a zobrazení. Vzhledem k tomu, že zobrazení Spark vyžadují modul Spark ke zpracování definujícího příkazu Spark SQL a nelze je zpracovat pomocí modulu SQL, jsou s moduly SQL pracovního prostoru sdíleny pouze databáze a jejich obsažené externí a spravované tabulky, které používají formát úložiště parket. Zobrazení Spark jsou sdílena pouze mezi instancemi fondu Spark.

## <a name="security-model-at-a-glance"></a>Přehled bezpečnostních modelů

Databáze a tabulky Spark, spolu s jejich synchronizované reprezentace v sql motory, jsou zabezpečeny na základní úrovni úložiště. Pokud je tabulka dotazována některou z motorů, které má předkladatel dotazu právo používat, je objekt zabezpečení předkladatele dotazu předáván podkladovým souborům. Oprávnění jsou kontrolována na úrovni systému souborů.

Další informace naleznete v [tématu Sdílená databáze Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Změna údržby

Pokud je objekt metadat odstraněn nebo změněn pomocí Spark, změny jsou zvednuty a rozšířeny do modulu NA vyžádání SQL a fondů SQL, které mají objekty synchronizovány. Synchronizace je asynchronní a změny se projeví v sql engines po krátké prodlevě.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených databázích metadat Azure Synapse Analytics](database.md)
- [Další informace o sdílených tabulkách metadat Azure Synapse Analytics](table.md)

