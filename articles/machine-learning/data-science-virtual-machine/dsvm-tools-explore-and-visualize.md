---
title: Zkoumání a vizualizaci dat nástroje – Azure | Dokumentace Microsoftu
description: Data vizualizace nástroje pro zkoumání a pro virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 693be80e493a0ba259d147f432dc9d6c07ba876d
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427517"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Data vizualizace nástroje pro zkoumání a na virtuální počítač pro datové vědy

Klíče krok pro datové vědy je rozumět datům. Vizualizace a nástroje na prozkoumávání dat pomáhají urychlit Principy data. Tady jsou některé nástroje k dispozici na datové VĚDY, které usnadňují tento krok klíče. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co je to?   | Opensourcový modul dotazů SQL u velkých objemů dat.    |
| Podporované DSVM verze      | Windows, Linux  |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      |  Nainstalované v `/dsvm/tools/drill*` vložený pouze v režimu   |
| Typické použití      |  Která zkoumání dat bez nutnosti ETL. Dotazy na různých datových zdrojů a formátů, včetně CSV, JSON, relačních tabulkách, Hadoop     |
| Jak používat nebo ji spustit?      | Zástupce na ploše  <br/> [Začínáme se přejít na 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Související nástroje na datové VĚDY      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Weka je kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy můžete buď použít přímo k datové sadě nebo ji volat z kódu Java. Weka obsahuje nástroje potřebné pro předběžné zpracování dat, klasifikace, regrese, clusteringu, asociačních pravidel a vizualizace. |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné nástroje ML     |
| Jak používat nebo ji spustit?      | Na Windows vyhledejte Weka v nabídce Start. V Linuxu, přihlaste se pomocí X2Go a pak přejděte do aplikace -> Vývoj -> Weka. |
| Odkazy na ukázky      | [Ukázky weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje na datové VĚDY      |Xgboost LightGBM Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí jazyka R   |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné uživatelského rozhraní nástroje dolování dat pro R    |
| Jak používat nebo ji spustit?      | Uživatelské rozhraní nástroje. Na Windows, spusťte příkazový řádek, využívejte jazyk R, pak v R spustit `rattle()`. V Linuxu, spojte se s X2Go, spusťte terminál, spusťte R a potom v R spustit `rattle()`. |
| Odkazy na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje na datové VĚDY      |Xgboost LightGBM, Weka,   |

## <a name="power-bi-desktop"></a>Power BI Desktopu 
|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroje BI    |
| Podporované DSVM verze      | Windows  |
| Typické použití      |  Vizualizace dat a vytváření řídicích panelů   |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Související nástroje na datové VĚDY      |   Visual Studio 2019, Visual Studio Code, Juno      |

