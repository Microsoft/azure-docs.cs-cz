---
title: Import dat do nástroje Machine Learning Studio | Dokumentace Microsoftu
description: Jak importovat data do Azure Machine Learning Studio z různých zdrojů dat. Zjistěte, jaké datové typy a formáty dat jsou podporovány.
keywords: Importujte dat, formát dat, datové typy, zdroje dat, trénovacích dat
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: a5750555802489b41b007831164767beb953ebc4
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "34837459"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Import cvičných dat do nástroje Azure Machine Learning Studio z různých zdrojů dat
Chcete-li použít vlastní data ve službě Machine Learning Studio k vývoji a trénování řešení prediktivní analýzy, můžete: 

* Nahrávání dat z **místního souboru** předem z vašeho pevného disku, jak vytvořit modul datovou sadu v pracovním prostoru
* přístup k datům z jednoho z několika **online zdroje dat** experimentu je spuštěn pomocí [Import dat] [ import-data] modulu 
* používat data z jiného Azure Machine learning **experimentovat** uložit jako datovou sadu
* používat data z místní **databáze systému SQL Server**

Každá z těchto možností je popsán v tématech v následující nabídce. Tato témata ukazují, jak importovat data z těchto různých zdrojů dat pro použití v nástroji Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Existuje několik ukázkových datových sad v nástroji Machine Learning Studio, můžete použít pro trénovací data k dispozici. Informace naleznete v tématu [použití ukázkových datových sad v nástroji Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

Tento úvodní téma také popisuje, jak získat data připravená k použití v nástroji Machine Learning Studio a popisuje datové typy a formáty dat podporovaných. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Získat data připravená k použití v nástroji Azure Machine Learning Studio
Machine Learning Studio je navržena pro práci s obdélníkové nebo tabulkových dat, jako jsou textová data, která má s oddělovači nebo strukturovaná data z databáze, i když se v některých případech může použít neobdélníkových data.

Je vhodné, pokud vaše data jsou relativně vyčistit. To znamená, že budete chtít postará o problémy, jako je bez uvozovek řetězce před odesláním dat do experimentu.

Však nejsou k dispozici v nástroji Machine Learning Studio, která umožňují některé manipulaci s daty v rámci experimentu moduly. V závislosti na algoritmech strojového učení, které budete používat, musíte se rozhodnout, jak budete zpracovávat data strukturální problémy, jako je například chybějící hodnoty a řídkých datových a moduly, které vám ho mohl pomoct, které jsou. Podívejte se **transformace dat** části palety modulů pro moduly, které provádějí tyto funkce.

Kdykoli v experimentu můžete zobrazit nebo stáhnout data, který je vytvořen po kliknutí na výstupní port modulu. V závislosti na modulu můžou existovat jiné stahování možnosti k dispozici nebo je možné vizualizovat data v rámci webového prohlížeče v nástroji Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Data formátů a datové typy podporované
Počet typů dat můžete importovat do experimentu, v závislosti na tom, jaký mechanismus použijete k importu dat a odkud pocházejí od:

* Prostý text (TXT)
* Hodnot oddělených čárkami (CSV) s hlavičkou (CSV) nebo bez něj (. nh.csv)
* Hodnoty oddělené tabulátorem (TSV) s hlavičkou (TSV) nebo bez něj (. nh.tsv)
* Excelový soubor
* Tabulka Azure
* Tabulka Hive
* Tabulka databází SQL
* Hodnoty OData
* SVMLight data (.svmlight) (viz [SVMLight definice](http://svmlight.joachims.org/) pro informace o formátu)
* Atribut formátu arff (Relation File Format) data (.arff) (viz [ARFF definice](http://weka.wikispaces.com/ARFF) pro informace o formátu)
* Soubor ZIP (.zip)
* Soubor objektu nebo pracovního prostoru R (. RData)

Pokud importujete data ve formátu například ARFF, který zahrnuje jejich metadata, Machine Learning Studio používá tato metadata k definování záhlaví a datový typ jednotlivých sloupců.

Pokud importujete data, jako je formát TSV nebo sdíleného svazku clusteru, který nezahrnuje tato metadata, Machine Learning Studio odvodí typ dat pro každý sloupec vzorkováním data. Pokud data také nemá záhlaví sloupců, Machine Learning Studio poskytuje výchozí názvy.

Můžete explicitně zadat nebo změnit hlavičky a datové typy sloupců pomocí [upravit Metadata][edit-metadata].

Následující **datové typy** jsou rozpoznány modulem pro Machine Learning Studio:

* Řetězec
* Integer
* Double
* Logická hodnota
* DateTime
* Časový interval

Machine Learning Studio používá interní data typ, který volá ***tabulka dat*** k předávání dat mezi moduly. Můžete explicitně převést vaše data do tabulky Data pomocí formátu [převod na datové sadě] [ convert-to-dataset] modulu.

Libovolného modulu, který přijímají se formáty než tabulka dat se převede data na tabulka dat bez upozornění před předáním k dalšímu modulu.

V případě potřeby můžete převést formát Data tabulky zpátky do CSV, TSV, ARFF nebo SVMLight formátu a používat ostatní moduly převodu.
Podívejte se **převody formátů dat** části palety modulů pro moduly, které provádějí tyto funkce.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
