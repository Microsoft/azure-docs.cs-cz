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
ms.openlocfilehash: 22c0d7d604ca41044d2d969d4ddbd2ae1a4d23d5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170488"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Použití virtuálního počítače geografickou umělou inteligencí datové vědy

Virtuální počítač pro geograficky AI datové vědy můžete načíst data pro analýzu, provádět tahání dat a vytvářet modely AI aplikací, které spotřebovávají geoprostorové informace. Po zřízení geografického AI Data Science VM a přihlášení k ArcGIS pro prostřednictvím účtu ArcGIS můžete začít pracovat s ArcGIS desktopem a ArcGIs online. K ArcGIS můžete také přistupovat z rozhraní Pythonu a z mostu jazyka R, který je předem nakonfigurovaný na geografickou Data Science VM. Pokud chcete vytvářet bohaté aplikace AI, slučte geografické Data Science VM s architekturami strojového učení a hloubkového učení a dalším softwarem pro datové vědy, který je na něm k dispozici.  


## <a name="configuration-details"></a>Podrobnosti konfigurace

Knihovna Pythonu, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), která se používá k rozhraní ArcGIS, je nainstalovaná v globálním kořenovém conda prostředí data Science VM, které najdete na adrese ```c:\anaconda```.

- Pokud používáte Python na příkazovém řádku, spusťte ```activate``` příkaz k aktivaci do conda kořenového prostředí Pythonu.
- Pokud používáte notebook IDE nebo Jupyter, můžete vybrat prostředí nebo jádro, abyste se ujistili, že jste ve správném prostředí conda.

Most R pro ArcGIS je nainstalován jako knihovna R s názvem [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) v hlavní instanci Microsoft Machine Learning Server samostatnou v umístění ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Sady ```arcgisbinding``` Visual Studio, RStudio a Jupyter už jsou předem nakonfigurované tak, aby používaly toto prostředí jazyka r a budou mít přístup ke knihovně r. 


## <a name="geo-ai-data-science-vm-samples"></a>Virtuální počítač pro geograficky AI datové vědy ukázky

Kromě ukázek na základě architektury strojového učení a hloubkového učení ze základní Data Science VM se jako součást geografického AI Data Science VM poskytuje sada geoprostorovéch ukázek. Tyto ukázky vám mohou pomoci při řešení vývoje aplikací AI pomocí geoprostorových dat a ArcGIS softwaru:


1. [Začínáme se geoprostorové analýzou pomocí Pythonu](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Úvodní ukázka ukazující, jak pracovat se geoprostorovémi daty prostřednictvím rozhraní Pythonu do ArcGIS, je poskytována knihovnou [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Také ukazuje, jak kombinovat tradiční počítačové učení s geoprostorovémi daty a pak vizualizovat výsledek na mapě v ArcGIS.

2. [Začínáme se geoprostorové analýzou R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Úvodní ukázka, která ukazuje, jak pracovat s geoprostorovémi daty pomocí rozhraní R, které je k dispozici v ArcGIS knihovně [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) . 

3. [Klasifikace použití půdy na úrovni pixelů](https://github.com/Azure/pixel_level_land_classification): Kurz, který ukazuje, jak vytvořit model rozsáhlého neuronové sítě, který přijímá leteckou Image jako vstup a vrací popisek pozemkového pokrytí. Příklady štítků na úrovni pozemků jsou *doménové struktury* a *voda*. Model vrátí popisek pro každý pixel na obrázku. Model se sestavuje pomocí architektury hloubkového učení Microsoft Open-source [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) .


## <a name="next-steps"></a>Další postup

Další ukázky, které používají virtuální počítač pro datové vědy jsou k dispozici zde:

* [Ukázky](dsvm-samples-and-walkthroughs.md)

