---
title: Podrobná příručka, jak používat rozhraní API pro Azure Machine Learning Data přípravy spuštění | Dokumentace Microsoftu
description: Tento dokument obsahuje podrobnosti o spuštění dříve určený balíčky zdroje dat a příprava dat
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 5280ed4143ef2ee980300cebe234de7f2020a14f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968097"
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Spouštění balíčků zdroje dat a příprava dat z Pythonu

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Použití zdroje dat Machine Learning Azure nebo Azure Machine Learning Data přípravy balíček v Pythonu:

1. Přejděte **Data** kartu vašeho projektu.

2. Klikněte pravým tlačítkem na příslušné zdroje.

3. Zvolte **generovat soubor s kódem přístupu k datům.**

Tato akce vytvoří krátký skript v jazyce Python, který spustí balíček a vrací datový rámec.

## <a name="data-sources"></a>Zdroje dat

`azureml.dataprep.datasource` Modul obsahuje jedné funkce k provedení zdroje dat a vrácení datový rámec: `load_datasource(path, secrets=None, spark=None)`.
- `path` je cesta ke zdroji dat (soubor .dsource).
- `secrets` je volitelný slovník, který mapuje klíče k tajným kódům.
- `spark` Volitelná logická hodnota, která určuje, jestli se mají vrátit Spark dataframe nebo Pandas dataframe je. Ve výchozím nastavení Azure Machine Learning Workbench Určuje, jaký druh datového rámce vrátit za běhu na základě kontextu.

## <a name="data-preparations-packages"></a>Balíčky přípravy dat

`azureml.dataprep.package` Modul obsahuje tři funkce, které jsou spouštěny datový tok od přípravy dat balíčku.

### <a name="execution-functions"></a>Spuštění funkce

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` odešle tok zadaná data pro spuštění, ale nevrací datový rámec.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` Spustí tok zadaná data a vrátí výsledky jako datový rámec.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` Spustí zadaný datový tok na základě zdroje dat v paměti a vrátí výsledky jako datový rámec. `user_config` Argument je slovník, který mapuje absolutní cesta zdroje dat (soubor .dsource) ke zdroji dat v paměti, vyjádřené seznamů.

### <a name="common-arguments"></a>Běžné argumenty

- `package_path` je cesta k balíčku Příprava dat (soubor .dprep).
- `dataflow_idx` je index založený na nule, který toku dat v balíčku k provedení. Je-li zadaného datového toku odkazuje na jiné datové toky nebo zdroje dat, jsou spuštěny také.
- `secrets` je volitelný slovník, který mapuje klíče k tajným kódům.
- `spark` Volitelná logická hodnota, která určuje, jestli se mají vrátit Spark dataframe nebo Pandas dataframe je. Ve výchozím nastavení aplikace Workbench určí jaký druh datového rámce vrátit za běhu na základě kontextu.
