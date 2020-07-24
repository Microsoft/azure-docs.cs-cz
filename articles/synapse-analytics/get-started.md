---
title: 'Kurz: Začínáme s Azure synapse Analytics'
description: V tomto kurzu se naučíte základní kroky pro nastavení a používání Azure synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075860"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Začínáme s Azure synapse Analytics

Tento kurz představuje podrobný návod pro hlavní funkce služby Azure synapse Analytics. Tento kurz je ideálním výchozím bodem pro někoho, kdo chce pomocí klíčových scénářů služby Azure synapse Analytics začít prohlídku s průvodcem. Po provedení kroků v tomto kurzu budete mít plně funkční pracovní prostor synapse, ve kterém můžete začít s analýzou dat pomocí SQL, SQL na vyžádání a Apache Spark.

Naučíte se:
* Zřízení pracovního prostoru synapse v předplatném Azure
* Konfigurace řízení přístupu na účtu ADLSGEN2, aby plynule spolupracuje s pracovním prostorem synapse
* Načtěte ukázková data NYCTaxi do pracovního prostoru synapse, aby je bylo možné použít v SQL, na vyžádání SQL a Spark.
* Úpravy a spouštění skriptů SQL a synapse poznámkových bloků pomocí synapse studia
* Dotazování tabulek SQL a tabulek Spark
* Načtení dat z tabulek SQL do datových snímků Spark
* Načtení dat do tabulek SQL z datových rámců Spark
* Prozkoumat obsah účtu ADLSGEN2
* Analýza Parquet DataFile v účtech ADLSGEN2 pomocí Sparku a SQL na vyžádání 
* Vytvoření datového kanálu, který každou hodinu automaticky spustí synapse Poznámkový blok

Postupujte podle kroků *v uvedeném pořadí* a Projděte si spoustu možností a Naučte se, jak využít základní funkce.

* [Krok 1 – Vytvoření a nastavení pracovního prostoru synapse](get-started-create-workspace.md)
* [Krok 2 – analýza pomocí fondu SQL](get-started-analyze-sql-pool.md)
* [Krok 3 – analýza pomocí Sparku](get-started-analyze-spark.md)
* [Krok 4 – analýza pomocí SQL na vyžádání](get-started-analyze-sql-on-demand.md)
* [Krok 5 – analýza dat v účtu úložiště](get-started-analyze-storage.md)
* [Krok 6 – orchestrace pomocí kanálů](get-started-pipelines.md)
* [Krok 7 – vizualizace dat pomocí Power BI](get-started-visualize-power-bi.md)
