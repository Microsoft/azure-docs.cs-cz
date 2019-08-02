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
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557763"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Nástroje pro zkoumání a vizualizaci dat na Data Science Virtual Machine

Hlavním krokem v oblasti datové vědy je pochopení dat. Vizualizace a nástroje pro zkoumání dat vám pomůžou urychlit porozumění datům. Tady je několik nástrojů, které jsou k dispozici na DSVM, což usnadňuje tento krok klíče. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co je to?   | Open Source dotazovací modul SQL pro velké objemy dat    |
| Podporované DSVM verze      | Windows, Linux  |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      |  Nainstalováno `/dsvm/tools/drill*` v pouze v integrovaném režimu   |
| Typické použití      |  Zkoumání dat in-situ bez nutnosti ETL Dotazování různých zdrojů dat a formátů, včetně CSV, JSON, relačních tabulek, Hadoop     |
| Jak používat nebo ji spustit?      | Zástupce na ploše  <br/> [Začínáme s procházením za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Související nástroje na datové VĚDY      |   Rattle, weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Weka je kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy mohou být buď aplikovány přímo na datovou sadu, nebo volány z vlastního kódu Java. Weka obsahuje nástroje potřebné pro předběžné zpracování dat, klasifikace, regrese, clusteringu, asociačních pravidel a vizualizace. |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Nástroj General ML     |
| Jak používat nebo ji spustit?      | Ve Windows vyhledejte weka v nabídce Start. V systému Linux se přihlaste pomocí X2Go a pak přejděte do aplikace – > vývoj – > weka. |
| Odkazy na ukázky      | [Ukázky weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje na datové VĚDY      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí jazyka R   |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecný nástroj pro dolování dat uživatelského rozhraní pro R    |
| Jak používat nebo ji spustit?      | Uživatelské rozhraní nástroje. V systému Windows spusťte příkazový řádek, spusťte R a potom v R spusťte `rattle()`. V systému Linux se připojte pomocí X2Go, spusťte terminál, spusťte R a potom v R spusťte `rattle()`. |
| Odkazy na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje na datové VĚDY      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroje BI    |
| Podporované DSVM verze      | Windows  |
| Typické použití      |  Vizualizace dat a vytváření řídicích panelů   |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Související nástroje na datové VĚDY      |   Visual Studio 2019, Visual Studio Code, Juno      |

