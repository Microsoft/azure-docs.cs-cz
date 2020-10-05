---
title: Model sdílených metadat
description: Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi svými fondy Spark (Preview), modulem SQL na vyžádání (Preview) a fondy SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: c11a0ccb08f03775a07716e6c547d849cda347dd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87387332"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Sdílená metadata Azure synapse Analytics

Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi svými fondy Spark (Preview) a modulem SQL na vyžádání (Preview).

[!INCLUDE [preview](../includes/note-preview.md)]

Sdílení podporuje vzor pro moderní datový sklad, který se označuje jako moderní, a poskytuje přístup k databázím a tabulkám vytvořeným pomocí Sparku v prostředích SQL. Umožňuje také modulům SQL vytvářet vlastní objekty, které nejsou sdíleny s jinými moduly.

## <a name="support-the-modern-data-warehouse"></a>Podpora moderních datových skladů

Model Shared metadata podporuje následující vzor pro moderní datové sklady následujícím způsobem:

1. Data z Data Lake se připravují a strukturují efektivně pomocí Sparku uložením připravených dat do (případně děleného) Parquet tabulek, které jsou v některých databázích.

2. Vytvořené databáze Sparku a všechny jejich tabulky se budou zobrazovat ve všech instancích fondu Azure synapse v pracovním prostoru Spark a dají se použít z kterékoli úlohy Sparku. Tato funkce podléhá [oprávněním](#security-model-at-a-glance) , protože všechny fondy Spark v pracovním prostoru sdílejí stejné základní úložiště metadat katalogu.

3. Vytvořené databáze Sparku a jejich Parquet tabulky se budou zobrazovat v modulu SQL na vyžádání v pracovním prostoru. [Databáze](database.md) se vytvářejí automaticky v metadatech na vyžádání SQL a [externí i spravované tabulky](table.md) vytvořené pomocí úlohy Sparku jsou dostupné jako externí tabulky v metadatech SQL na vyžádání ve `dbo` schématu odpovídající databáze. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synchronizace objektů probíhá asynchronně. Objekty budou mít mírné zpoždění v průběhu několika sekund, dokud se nezobrazí v kontextu SQL. Jakmile se zobrazí, můžou se dotazovat, ale ne aktualizovat ani měnit moduly SQL, které k nim mají přístup.

## <a name="shared-metadata-objects"></a>Sdílené objekty metadat

Spark umožňuje vytvářet databáze, externí tabulky, spravované tabulky a zobrazení. Vzhledem k tomu, že zobrazení Spark vyžadují pro zpracování definování příkazu Spark SQL modul Spark, a nemůže je zpracovat modulem SQL, sdílí se s modulem SQL pracovního prostoru jenom databáze a jejich obsažené externí a spravované tabulky, které používají formát úložiště Parquet. Zobrazení Spark se sdílí jenom mezi instancemi fondů Spark.

## <a name="security-model-at-a-glance"></a>Přehled o modelu zabezpečení

Databáze a tabulky Spark spolu s jejich synchronizovanými reprezentacemi v modulu SQL jsou zabezpečeny na základní úrovni úložiště. Pokud je tabulka dotazována jakýmkoli modulem, který má odesílatel dotazu právo k použití, je objekt zabezpečení odesílatele dotazu předán do podkladových souborů. Oprávnění jsou kontrolována na úrovni systému souborů.

Další informace najdete v tématu [sdílená databáze Azure synapse Analytics](database.md).

## <a name="change-maintenance"></a>Změna údržby

Pokud se objekt metadat odstraní nebo změní pomocí Sparku, změny se vybírají a rozšíří na modul SQL na vyžádání. Synchronizace je asynchronní a změny se projeví v modulu SQL po krátké prodlevě.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených databázích metadat služby Azure synapse Analytics](database.md)
- [Další informace o sdílených tabulkách metadat služby Azure synapse Analytics](table.md)

