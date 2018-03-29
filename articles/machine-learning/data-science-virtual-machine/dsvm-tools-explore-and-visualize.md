---
title: Zkoumání a vizualizace dat nástroje - Azure | Microsoft Docs
description: Zkoumání a vizualizace nástrojů data pro virtuální počítač vědecké účely Data.
keywords: datové vědy nástroje, datové vědy virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: a89c51ae5b4b0c9d58aed04515042ed8b08dcce6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Zkoumání a vizualizace nástrojů data na Data virtuálního počítače vědecké účely

Klíče krok v vědecké zpracování dat je pochopit data. Vizualizace a nástroje pro zkoumání dat pomáhají urychlit Principy data. Tady jsou některé nástroje k dispozici na DSVM této faciliate krok tohoto klíče. 

## <a name="apache-drill"></a>Apache procházení
|    |           |
| ------------- | ------------- |
| Co je to?   | Stroj dotazů SQL s otevřeným zdrojem velkých objemů dat    |
| Podporované DSVM verze      | Windows, Linux  |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?      |  Nainstalované v `/dsvm/tools/drill*` embedded pouze v režimu   |
| Typické použití      |  Zkoumání dat na místě bez nutnosti ETL. Dotaz na různé datové zdroje a formáty includign CSV, formát JSON, relační tabulky, Hadoop     |
| Jak se použít nebo ji spustit?      | Zástupce na ploše  <br/> [Začínáme s procházení za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Na DSVM souvisejících nástrojích      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Weka je kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy můžete být použitých přímo na datovou sadu nebo volat z kódu Java. Weka obsahuje nástroje pro předběžné zpracování dat, klasifikace, regrese, clustering, přidružení pravidel a vizualizace. |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné nástroje ML     |
| Jak se použít nebo ji spustit?      | V systému Windows vyhledejte Weka v nabídce Start. V systému Linux, přihlaste se pomocí X2Go a pak přejděte do aplikace -> Vývoj -> Weka. |
| Odkazy na ukázky      | [Ukázky weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Na DSVM souvisejících nástrojích      |Xgboost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí R   |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Nástroj pro dolování dat obecné uživatelského rozhraní pro R    |
| Jak se použít nebo ji spustit?      | Nástroj uživatelského rozhraní. V systému Windows, spusťte příkazový řádek, spusťte R a pak uvnitř R spustit `rattle()`. V systému Linux, spojte se s X2Go, počáteční terminál, spusťte R a pak uvnitř R spustit `rattle()`. |
| Odkazy na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Na DSVM souvisejících nástrojích      |Xgboost LightGBM, Weka,   |

## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj BI a interaktivní vizualizaci dat    |
| Podporované DSVM verze      | Windows  |
| Typické použití      |  Vizualizace dat a vytváření řídicích panelů   |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Na DSVM souvisejících nástrojích      |   Visual Studio 2017, Visual Studio Code, Juno      |

