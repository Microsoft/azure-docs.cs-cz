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
ms.openlocfilehash: a243ac45ce29ae824c8f38036d4387da3b1cff8f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453208"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Data vizualizace nástroje pro zkoumání a na virtuální počítač pro datové vědy

Klíče krok pro datové vědy je rozumět datům. Vizualizace a nástroje na prozkoumávání dat pomáhají urychlit Principy data. Tady jsou některé nástroje k dispozici na datové VĚDY, které využívají tento krok klíče. 

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
| Odkazy na ukázky      | [Ukázky weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
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

## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroje BI    |
| Podporované DSVM verze      | Windows  |
| Typické použití      |  Vizualizace dat a vytváření řídicích panelů   |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Související nástroje na datové VĚDY      |   Visual Studio 2017, Visual Studio Code, Juno      |

