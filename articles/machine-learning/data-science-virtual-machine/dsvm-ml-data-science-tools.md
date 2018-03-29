---
title: Počítač učení a vědecké účely nástroje – Azure | Microsoft Docs
description: Nástroje Machine learning a data vědecké účely
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
ms.openlocfilehash: f8d7fff30d5f5289c362d78ad89027b8141bbbe6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="machine-learning-and-data-science-tools"></a>Nástroje Machine learning a data vědecké účely
Datové vědy virtuálního počítače (DSVM) obsahuje bohatou sadu nástrojů a knihoven pro machine learning v oblíbených jazyků jako Dita Python, R, k dispozici. 

Zde jsou některé strojového učení nástroje a knihovny na DSVM. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co je to?   |    Fast přenositelností a distribuované přechodu zvyšovat skóre (GBDT, GBRT nebo GBM) knihovny pro Python, R, Java, Scala, C++ a další. Běží na jednom počítači, Hadoop, Spark    |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné ML knihovny      |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?      |  Nainstalovaná s podporou GPU   |
| Jak se použít nebo ji spustit?      | Jako knihovna Python (2.7 a 3.5) balíček R a na nástroj příkazového řádku cestu (`C:\dsvm\tools\xgboost\bin\xgboost.exe` pro Windows, `/dsvm/tools/xgboost/xgboost` pro Linux)    |
| Odkazy na ukázky      | Ukázky jsou součástí na virtuálním počítači, `/dsvm/tools/xgboost/demo` v systému Linux a `C:\dsvm\tools\xgboost\demo` v systému Windows   |
| Na DSVM souvisejících nástrojích      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>K dispozici Vowpal
|    |           |
| ------------- | ------------- |
| Co je to?   |   K dispozici Vowpal (také označované jako "Zobrazit") je typu open source rychlého na více systémů z – základní učení knihovna systému    |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné ML knihovny      |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?      |  -Msi Instalační služba systému Windows, Linux - výstižný get |
| Jak se použít nebo ji spustit?      | Jako nástroj příkazového řádku na cestu (`C:\Program Files\VowpalWabbit\vw.exe` v systému Windows, `/usr/bin/vw` v systému Linux)    |
| Odkazy na ukázky      | [Ukázky VowPal k dispozici](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Na DSVM souvisejících nástrojích      |XGBoost LightGBM, MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Weka je kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy můžete být použitých přímo na datovou sadu nebo volat z kódu Java. Weka obsahuje nástroje pro předběžné zpracování dat, klasifikace, regrese, clustering, přidružení pravidel a vizualizace. |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné nástroje ML     |
| Jak se použít nebo ji spustit?      | V systému Windows vyhledejte Weka v nabídce Start. V systému Linux, přihlaste se pomocí X2Go a pak přejděte do aplikace -> Vývoj -> Weka. |
| Odkazy na ukázky      | [Ukázky weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Na DSVM souvisejících nástrojích      |XGBooost LightGBM, Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí R   |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Nástroj pro dolování dat obecné uživatelského rozhraní pro R    |
| Jak se použít nebo ji spustit?      | Nástroj uživatelského rozhraní. V systému Windows, spusťte příkazový řádek, spusťte R a pak uvnitř R spustit `rattle()`. V systému Linux, spojte se s X2Go, počáteční terminál, spusťte R a pak uvnitř R spustit `rattle()`. |
| Odkazy na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Na DSVM souvisejících nástrojích      |XGBoost LightGBM, Weka,   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co je to?   | Rychlé, distribuované, vysoce výkonné přechodu zvyšovat skóre framework (GBDT, GBRT, GBM nebo TRŽIŠTĚ) podle rozhodovacího stromu algoritmů, používá k hodnocení, klasifikace a mnoho dalších strojového učení úlohy.    |
| Podporované DSVM verze      | Windows, Linux    |
| Typické použití      | Pro obecné účely přechodu zvýšení skóre framework      |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?      | V systému Windows je nainstalována LightGBM jako balíček Python. V systému Linux, spustitelný soubor příkazového řádku se nachází v `/opt/LightGBM/lightgbm`, je nainstalován balíček R a Python balíčky jsou nainstalované.     |
| Odkazy na ukázky      | [Průvodce LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Na DSVM souvisejících nástrojích      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co je to?   | Platformě AI open-source podpora v paměti, distribuované, rychlé a škálovatelné machine learning  |
| Podporované DSVM verze      | Linux   |
| Typické použití      | Obecné účely distribuována, škálovatelné ML   |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?      | Je nainstalovaný H2O `/dsvm/tools/h2o`.      |
| Jak se použít nebo ji spustit?      | Připojte k virtuálnímu počítači pomocí X2Go. Spusťte nové terminál a spusťte `java -jar /dsvm/tools/h2o/current/h2o.jar`. Potom spusťte webový prohlížeč a připojte se k `http://localhost:54321`.      |
| Odkazy na ukázky      | Ukázky jsou dostupné na virtuálním počítači v Jupyter pod `h2o` adresáře.      |
| Na DSVM souvisejících nástrojích      | Apache Spark, MXNet, XGBoost, šumivých horních, hloubkové horních    |

Na DSVM jako oblíbených nejsou několik další knihovny ML `scikit-learn` balíček, který se dodává jako součást distribuční Anaconda Python, který je nainstalován na DSVM. Ujistěte se, projděte si seznam balíčků dostupných ve Python, R a Dita spuštěním Správce příslušných balíčku. 
