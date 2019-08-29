---
title: Nástroje pro zkoumání a vizualizaci dat – Azure | Microsoft Docs
description: Nástroje pro zkoumání a vizualizaci dat pro Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: d60acdf483d418e458f51ef6cf31b17c43b7379f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065909"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Nástroje pro zkoumání a vizualizaci dat na Data Science Virtual Machine

V oblasti datové vědy je klíč pochopit data. Vizualizace a nástroje pro zkoumání dat vám pomůžou urychlit porozumění datům. Následující nástroje, které jsou k dispozici na Data Science Virtual Machine (DSVM), usnadňují tento krok tohoto klíče.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co je to?   | Open Source dotazovací modul SQL pro velké objemy dat    |
| Podporované verze DSVM      | Windows, Linux  |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      |  Nainstalováno `/dsvm/tools/drill*` v pouze v integrovaném režimu   |
| Typické použití      |  Pro průzkum místních dat bez nutnosti extrakce, transformace, načítání (ETL). Dotazování různých zdrojů dat a formátů, včetně sdílených svazků clusteru, formátu JSON, relačních tabulek a Hadoop.     |
| Jak použít a spustit      | Zástupce na ploše  <br/> [Začínáme s procházením za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Související nástroje na DSVM      |   Rattle, weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Kolekce algoritmů strojového učení pro úlohy dolování dat. Tyto algoritmy lze buď použít přímo na datovou sadu nebo volat z vlastního kódu Java. Weka obsahuje nástroje pro předzpracování dat, klasifikaci, regresi, clusteringu, pravidla přidružení a vizualizaci. |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecný nástroj pro strojové učení     |
| Jak použít a spustit      | Na Windows vyhledejte v nabídce Start Weka. V systému Linux se přihlaste pomocí X2Go a potom v nabídce aplikace > vývojové > weka. |
| Odkazy na ukázky      | [Ukázky weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje na DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní (GUI) pro dolování dat pomocí jazyka R   |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecný nástroj pro dolování dat uživatelského rozhraní pro R    |
| Jak použít a spustit      | Uživatelské rozhraní nástroje. Ve Windows otevřete příkazový řádek, spusťte R a potom v R spusťte `rattle()`příkaz. V systému Linux se připojte pomocí X2Go, spusťte terminál, spusťte R a potom v jazyce R spusťte příkaz `rattle()`. |
| Odkazy na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje na DSVM      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroj BI    |
| Podporované verze DSVM      | Windows  |
| Typické použití      |  Vizualizace dat a sestavování řídicích panelů   |
| Jak použít a spustit      | Zástupce na ploše`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Související nástroje na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

