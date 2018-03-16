---
title: "Umožňuje importovat data do nástroje Machine Learning Studio | Microsoft Docs"
description: "Jak importovat data do Azure Machine Learning Studio z různých zdrojů dat. Zjistěte, jaké datové typy a formáty dat jsou podporovány."
keywords: "Importujte dat, formát dat, datové typy, zdroje dat, Cvičná data"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: garye;bradsev
ms.openlocfilehash: eb22f516f298df9396ca809acaa9c8cb62589c2a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Import cvičných dat do nástroje Azure Machine Learning Studio z různých zdrojů dat
Chcete-li použít vlastní data v nástroji Machine Learning Studio pro vývoj a cvičení řešení prediktivní analýzy, můžete: 

* nahrání dat z **místního souboru** předem z pevného disku pro vytvoření datové sady modulu v pracovním prostoru
* přístup k datům z jednoho z několika **zdroje dat online** experimentu je spuštěn pomocí [importovat Data] [ import-data] modulu 
* použít data z jiného Azure Machine learning **experimentovat** uložit jako datové sady
* použít data z místního **databáze systému SQL Server**

Každá z těchto možností je popsán v tématech v nabídce níže. Tato témata ukazují, jak importovat data z různých zdrojů dat pro použití v Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Nejsou k dispozici v Machine Learning Studio, který můžete použít pro Cvičná data několik ukázkových datových sad. Informace naleznete v tématu [pomocí ukázkových datových sad v nástroji Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

Toto úvodní téma také popisuje, jak získat data připravená k použití v nástroji Machine Learning Studio a popisuje, jaké formáty dat a datové typy jsou podporovány. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Příprava dat pro použití v nástroji Azure Machine Learning Studio
Machine Learning Studio je navržen pro práci s daty obdélníková nebo tabulkovém, jako je například textová data, která má oddělený nebo strukturovaná data z databáze, i když se v některých případech může použít obdélníkový data.

Je nejvhodnější Pokud vaše data jsou relativně vyčistit. To znamená, že budete chtít postará problémy, jako jsou třeba nekotovaných řetězce před nahráním dat do experimentu.

Však nejsou k dispozici v Machine Learning Studio, které umožňují některé manipulaci s daty v rámci experimentu moduly. V závislosti na algoritmů strojového učení, které budete používat, musíte se rozhodnout, jak budete pracovat strukturální problémy dat, například chybějící hodnoty a zhuštěných dat a jsou moduly, které mohou pomoci s které. Oblast hledání **transformaci dat** části palety modulů pro moduly, které provádějí tyto funkce.

V libovolném bodě v experimentu můžete zobrazit nebo stáhnout data, která je produkovaný modul kliknutím na výstupní port. V závislosti na modulu, je možné možnosti různých stahování k dispozici, nebo bude pravděpodobně možné k vizualizaci dat webového prohlížeče v nástroji Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Data formátů a datové typy podporované
Můžete importovat do experimentu několik typů dat, v závislosti na tom, jaký mechanismus použijete k importu dat a odkud pocházejí z:

* Prostý text (TXT)
* Hodnot oddělených čárkami (CSV) s hlavičkou (CSV) nebo bez (. nh.csv)
* Karta s oddělovači (TSV) s hlavičkou (TSV) nebo bez (. nh.tsv)
* Excelový soubor
* Tabulka Azure
* Tabulku Hive
* Tabulka databází SQL
* Hodnoty OData
* Data SVMLight (.svmlight) (najdete v článku [SVMLight definice](http://svmlight.joachims.org/) formátu informace)
* Atribut dat vztah soubor formátu (ARFF) (.arff) (najdete v článku [ARFF definice](http://weka.wikispaces.com/ARFF) formátu informace)
* Soubor ZIP (.zip)
* R objekt nebo prostoru souboru (. RData)

Pokud importujete data ve formátu, například ARFF, který obsahuje metadata, Machine Learning Studio používá tato metadata zadat záhlaví a datový typ jednotlivých sloupců.

Pokud importujete data, jako jsou TSV nebo CSV formátu, který neobsahuje tato metadata, Machine Learning Studio odvodí datový typ pro každý sloupec vzorkováním data. Pokud data také nemá záhlaví sloupců, Machine Learning Studio obsahuje výchozí názvy.

Můžete explicitně zadat nebo změnit hlavičky a datové typy pro sloupce pomocí [upravit Metadata][edit-metadata].

Následující **datové typy** rozpoznává Machine Learning Studio:

* Řetězec
* Integer
* Dvojitý
* Logická hodnota
* DateTime
* TimeSpan

Machine Learning Studio používá typ interních datových názvem ***tabulky dat*** k předávání dat mezi moduly. Můžete explicitně převést svá data pomocí formátu Data tabulky [převést na datovou sadu] [ convert-to-dataset] modulu.

Libovolný modul, který přijímá formáty než tabulky datového převede data do tabulky Data bez upozornění před jeho odesláním další modul.

V případě potřeby můžete převést formát Data tabulky zpět do sdíleného svazku clusteru, TSV, ARFF nebo SVMLight formátu pomocí převodu z ostatních modulů.
Oblast hledání **převody formát dat** části palety modulů pro moduly, které provádějí tyto funkce.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
