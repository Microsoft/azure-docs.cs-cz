---
title: Používání systému geograficky umělé Intelligence Data vědecké účely virtuálního počítače – Azure | Microsoft Docs
description: Jak používat geograficky AI virtuálního počítače na platformě Azure.
keywords: přímý učení, AI, vědecké účely nástrojů data, data vědecké účely virtuálního počítače, geoprostorové analytics
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: f7ee109c26f8b2f6107dfcb4853d5517384aef76
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Používání virtuálních počítačů geograficky umělé Intelligence Data vědecké účely

Použijte virtuální počítač geograficky AI Data vědecké účely načíst data pro analýzu, provádět data wrangling a vytvářet modely pro AI aplikace, které využívají geoprostorové informace. Po zřízení virtuálního počítače geograficky AI datové vědy a přihlášení k ArcGIS Pro s vaším účtem ArcGIS, můžete začít interakci s ArcGIS desktop a ArcGis online. Můžete taky přejít ArcGIS z rozhraní Python a most jazyk R předem nakonfigurované na virtuálním počítači vědecké účely Geo-Data. Pokud chcete vytvářet bohaté aplikace AI, pouze s počítačem učení a hloubky učení rozhraní a další software vědecké účely data k dispozici na virtuálním počítači vědecké účely Data.  


## <a name="configuration-details"></a>Podrobnosti konfigurace

Knihovna Python [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), který se používá k rozhraní s ArcGIS je nainstalován v prostředí conda globální kořenové virtuálního počítače vědecké účely Data, která se nachází v ```c:\anaconda```. 

- Pokud používáte Python v příkazovém řádku, spusťte ```activate``` aktivovat do prostředí conda kořenové Python. 
- Pokud používáte IDE nebo Jupyter Poznámkový blok, můžete vybrat jádra zajistit, že jste v prostředí správné conda nebo prostředí. 

R – mostu k ArcGIS je nainstalován jako R knihovny s názvem [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) v hlavní Microsoft R samostatnou instanci serveru nacházející se v ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, Rstudia a Jupyter jsou již předem nakonfigurovaný k použití tohoto prostředí R a bude mít přístup k ```arcgisbinding``` R knihovny. 


## <a name="geo-ai-data-science-vm-samples"></a>Ukázky geograficky AI datové vědy virtuálních počítačů

Kromě ML a přímý učení na základě framework ukázky z virtuálního počítače základní vědecké účely, Data je k dispozici sady vzorků geoprostorové jako část virtuálního počítače geograficky AI Data vědecké účely. Tyto ukázky můžete rychle začít s prací váš vývojový AI aplikace s použitím geoprostorové dat a ArcGIS softwaru. 


1. [Získávání uvedená v geoprostorové analytics s Pythonem](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): úvodní ukázka znázorňující způsob práce s daty geoprostorové pomocí rozhraní Python ArcGIS poskytované [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) knihovny. Také ukazuje, jak můžete kombinovat tradiční strojového učení s daty geoprostorové a vizualizovat výsledek na mapě v ArcGIS. 

2. [Získávání uvedená v geoprostorové analytics s R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): úvodní ukázka, která ukazuje, jak pracovat s daty geoprostorové pomocí rozhraní R ArcGIS poskytované [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) knihovny. 

3. [Klasifikace použít úrovni pixelů krajině](https://github.com/Azure/pixel_level_land_classification): kurz, který ukazuje, jak vytvořit model hluboké neuronové sítě, který přijímá leteckou bitovou kopii jako vstup a vrátí krajině titulní štítek. Příkladem krajině titulní popisky jsou "forested" nebo "horních." Model vrátí štítek, pro každý pixel v bitové kopii. Model je sestaven pomocí společnosti Microsoft open-source [kognitivní Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) hloubkové learning framework. Tento příklad také ukazuje, jak chcete škálovat školení na [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) a využívat předpovědi modelu v ArcGIS Pro software. 


## <a name="next-steps"></a>Další postup

Další příklady používané virtuální počítač vědecké účely dat jsou k dispozici zde:

* [Ukázky](dsvm-samples-and-walkthroughs.md)

