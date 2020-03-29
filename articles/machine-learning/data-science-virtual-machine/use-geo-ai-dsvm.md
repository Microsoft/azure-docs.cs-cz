---
title: Použití geo ai
titleSuffix: Azure Data Science Virtual Machine
description: Naučte se používat virtuální počítač Geo AI Data Science k analýze dat a vytváření modelů založených na geoprostorových datech.
keywords: hluboké učení, AI, nástroje pro datovou vědu, virtuální stroj pro datové vědy, geoprostorová analytika
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278417"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Použití virtuálního počítače geoumělé inteligence pro datové vědy

Virtuální modul Geo AI Data Science slouží k načtení dat pro analýzu, provádění hádek s daty a vytváření modelů pro aplikace ai, které využívají geoprostorové informace. Po zřízení virtuálního počítače Geo AI Data Science a přihlášení k ArcGIS Pro prostřednictvím účtu ArcGIS můžete začít komunikovat s desktopem ArcGIS a ArcGIs online. ArcGIS můžete také přistupovat z rozhraní Pythonu a most jazyka R, který je předkonfigurovaný na virtuálním počítači geo-data science. Chcete-li vytvářet bohaté aplikace pro umělou ai, kombinujte virtuální počítač Geo-Data Science s architekturami strojového učení a hloubkového učení a dalším softwarem pro datové vědy, které jsou na něm k dispozici.  


## <a name="configuration-details"></a>Podrobnosti o konfiguraci

Knihovna Pythonu, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), která se používá k rozhraní s ArcGIS, se nainstaluje v globálním ```c:\anaconda```kořenovém prostředí conda virtuálního virtuálního mandu pro datové vědy, který se nachází na .

- Pokud používáte Python na příkazovém ```activate``` řádku, spusťte aktivaci do prostředí conda root Python.
- Pokud používáte poznámkový blok IDE nebo Jupyter, můžete vybrat prostředí nebo jádro a ujistit se, že jste ve správném prostředí conda.

R most arcgis je nainstalován jako knihovna R s názvem [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) v hlavní microsoft ```C:\Program Files\Microsoft\ML Server\R_SERVER```machine learning server samostatnou instanci, která je umístěna na adrese . Visual Studio, RStudio a Jupyter jsou již předkonfigurované pro ```arcgisbinding``` použití tohoto prostředí R a budou mít přístup ke knihovně R. 


## <a name="geo-ai-data-science-vm-samples"></a>Ukázky virtuálních mích geo ai dat

Kromě vzorků založených na rozhraní machine-learning a hloubkového učení ze základního virtuálního počítače pro datové vědy je sada geoprostorových vzorků k dispozici také jako součást virtuálního počítače geo ai data science. Tyto ukázky vám mohou pomoci nastartovat vývoj aplikací ai pomocí geoprostorových dat a softwaru ArcGIS:


1. [Začínáme s geoprostorovou analýzou s Pythonem](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Úvodní ukázka, která ukazuje, jak pracovat s geoprostorovými daty prostřednictvím rozhraní Pythonu na ArcGIS, poskytuje [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) library. Také ukazuje, jak kombinovat tradiční strojové učení s geoprostorovými daty a pak vizualizovat výsledek na mapě v ArcGIS.

2. [Začínáme s geoprostorovou analýzou s R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Úvodní ukázka, která ukazuje, jak pracovat s geoprostorovými daty pomocí rozhraní R na ArcGIS, které poskytuje [knihovna arcgisbinding.](https://github.com/R-ArcGIS/r-bridge) 

3. [Klasifikace využití půdy na úrovni pixelů](https://github.com/Azure/pixel_level_land_classification): Kurz, který ilustruje, jak vytvořit model hluboké neuronové sítě, který přijímá letecký obraz jako vstup a vrací popisek pozemního krytu. Příklady označení půdy jsou *zalesněné* a *voda*. Model vrátí takový popisek pro každý obrazový bod v obraze. 


## <a name="next-steps"></a>Další kroky

Další ukázky, které používají virtuální ms datové vědy jsou k dispozici zde:

* [ukázky](dsvm-samples-and-walkthroughs.md)