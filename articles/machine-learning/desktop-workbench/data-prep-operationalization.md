---
title: Podrobný průvodce o tom, jak používat Azure Machine Learning Data přípravy Operationalization | Microsoft Docs
description: Tento dokument obsahuje informace o provádění dříve určený zdroje dat a dat přípravy balíčků
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="data-preparation-operationalization"></a>Příprava Operationalization dat 

## <a name="operationalization-scenario"></a>Scénář operationalization

Dále jsou různé scénáře Operationalization přípravu dat, které může uživatel setkat.

### <a name="develop-your-model-based-on-training-data"></a>Vývojem vašeho modelu na základě dat školení

Předpokládejme, že pokud chcete vytvořit a nasadit službu v reálném čase vyhodnocování rozhraní API. Prvním krokem je vyvinout model pro výpočet skóre podle některé sadu Cvičná data. Příprava dat importuje jako nový zdroj dat ukázku Cvičná data. Jakmile je připraven data, DataPrep balíček obsahuje přípravné kroky. Pomocí účtu AzureML experimentování, může uživatel iterovat model machine learning pro generování popisky pro každý vstupní v Cvičná data.

Jako funkce potřeba aktualizovat data vrátí uživatele DataPrep balíček, který chcete připravit data nový způsob a uložit tyto kroky. Tento proces iterace generování nových funkcí a jejich model strojového učení postupně je upravujte pokračuje, dokud modelu přesně skóre jejich testovacích datech. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Model nasadit do služby Azure modelu Management

Nyní máte zákaznická data, která chcete ke stanovení skóre v reálném čase. Pomocí služby pro správu Azure modelu, můžete nasadit tento model z rozhraní příkazového řádku Python AzureML jako služba. Nasazené služby zpřístupní koncový bod REST pro příjem dat zákazníka v reálném čase a vrací odpovídající výstupní štítky z trained model.

Koncový bod modelu pro snadné použití, přijímá data ve formátu JSON. Vstup by měl být řetězec JSON představující 2 dimenzí pole dat, která bude předána transformace ReadJSONLiteral a převést na zdroj dat DataPrep. Datový balíček přípravný vytvořené během může fáze experimentování pak jde spustit pro datové proudy JSON. Výsledný dataframe lze předat pak pro cvičný model pro vyhodnocování.

## <a name="read-json-literal-transformation"></a>Čtení JSON literálu transformace

### <a name="description"></a>Popis

Pro účely operationalization obsahuje Příprava dat **ReadJsonLiteral** transformace ke spouštění aktivitu a generovat Pandas nebo Spark Dataframe. Tato transformace jednoznačně přijímá jako vstup stávající data připravená data balíčku a zdroj dat JSON. Tato transformace je k dispozici prostřednictvím rozhraní příkazového řádku DataPrep Python.

### <a name="instructions"></a>Pokyny

#### <a name="pythoncli"></a>PythonCLI

Azure Machine Learning Workbench, otevřete rozhraní příkazového řádku (Soubor > otevřít příkazový řádek).

V tomto příkladu TrainingPreparationSteps datový přípravný balíček slouží k přípravě dat a přidat funkci svazku pro každý vstupní.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` má následující volitelné parametry
 - `dataflow_idx`: Určete index požadovaného toku dat provést v balíčku
 - `secrets`: slovník tajný úložiště (klíč: secretId, hodnota: uložené tajný klíč)
 - `spark`: Zadejte relaci spark pro provedení spark

#### <a name="input"></a>Vstup

Vstup dimenzí 2 pole ("pole polí"). V Python by měl být vstupní seznam seznamů. Vzhledem k tomu, že JSON nemá typ nativní datum, všechny objekty datetime.datetime Python se převede na datum ve formátu ISO řetězce. Pro koncové body REST vstup by měl být řetězcový literál JSON představující pole JSON 2-D.

#### <a name="output"></a>Výstup

Pandas nebo Spark DataFrame. Typ DataFrame je dáno framework vybraný v konfiguraci spuštění (`.runconfig`). Výsledný dataframe lze předat jako vstup pro cvičný model pro vyhodnocování.
