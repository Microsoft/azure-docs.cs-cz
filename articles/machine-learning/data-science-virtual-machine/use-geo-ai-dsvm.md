---
title: Pomocí geografickou umělou inteligencí virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Zjistěte, jak použít počítač pro virtuální Geo AI datové vědy pro analýzu dat a vytvářet modely v závislosti na Geoprostorová data.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: vijetaj
ms.openlocfilehash: 9bca7089e6137b3780e3d22f50887e880be29d8e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565080"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Použití virtuálního počítače geografickou umělou inteligencí datové vědy

Virtuální počítač pro geograficky AI datové vědy můžete načíst data pro analýzu, provádět tahání dat a vytvářet modely AI aplikací, které spotřebovávají geoprostorové informace. Po zřízení virtuálního počítače geograficky AI datové vědy a přihlášení ArcGIS Pro pomocí svého účtu ArcGIS, můžete začít interakci s ArcGIS plochy a ArcGis online. ArcGIS se můžete dostat taky z rozhraní Python a přemostění jazyka R předem nakonfigurované na virtuální počítač pro geograficky datové vědy. K vytváření aplikací s bohatým obsahem AI, ho spojovat se strojové učení a obsáhlý learning rozhraní a další software vědy data k dispozici na virtuální počítač pro datové vědy.  


## <a name="configuration-details"></a>Podrobnosti konfigurace

Knihovna Python [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), který se používá k v prostředí conda globální kořenový virtuální počítač datové vědy, který se nachází v umístění je nainstalováno rozhraní s ArcGIS ```c:\anaconda```. 

- Pokud používáte Python v příkazovém řádku, spusťte ```activate``` aktivovat do prostředí conda kořenové Python. 
- Pokud používáte prostředí IDE nebo aplikace Jupyter notebook, můžete vybrat prostředí nebo jádra Ujistěte se, že jste v prostředí správné conda. 

Most R ArcGIS je nainstalován jako knihovny R s názvem [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) v hlavní Microsoft R samostatnou instanci serveru nachází v ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio a Jupyter jsou už nakonfigurovaná tak, aby použít toto prostředí R a bude mít přístup k ```arcgisbinding``` R knihovny. 


## <a name="geo-ai-data-science-vm-samples"></a>Virtuální počítač pro geograficky AI datové vědy ukázky

Vedle ML a hloubkové učení založené na platformě ukázky ze základní virtuální počítač pro datové vědy řadu ukázek v geoprostorové také poskytuje jako součást virtuální počítač pro geograficky AI datové vědy. Tyto ukázky vám můžou pomoct vám nastartování vývoje aplikací AI s využitím geoprostorových dat a ArcGIS softwaru. 


1. [Seznámení se geoprostorové analýzou pomocí Pythonu](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Úvodní ukázka ukazující, jak pracovat se Geoprostorovémi daty pomocí rozhraní Python k ArcGIS poskytovaného knihovnou [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Také ukazuje, jak můžete kombinovat tradiční strojového učení s geoprostorových dat a vizualizaci výsledků na mapě v ArcGIS. 

2. [Načítají se geoprostorové analýzy pomocí jazyka R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Úvodní vzorek, který ukazuje, jak pracovat se Geoprostorovémi daty pomocí rozhraní R k ArcGIS poskytovaného knihovnou [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) . 

3. [Klasifikace použití půdy na úrovni pixelů](https://github.com/Azure/pixel_level_land_classification): Kurz, který ukazuje, jak vytvořit model rozsáhlého neuronové sítě, který přijímá leteckou Image jako vstup a vrací popisek pozemkového pokrytí. Příklady pozemního titulní popisků, které jsou "forested" nebo "water". Model vrátí popisek pro každý pixel na obrázku. Model se vytvořil pomocí open source společnosti Microsoft [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) framework hloubkového učení. 


## <a name="next-steps"></a>Další postup

Další ukázky, které používají virtuální počítač pro datové vědy jsou k dispozici zde:

* [Ukázky](dsvm-samples-and-walkthroughs.md)

