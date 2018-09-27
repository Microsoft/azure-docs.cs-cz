---
title: Machine learning a datové vědy nástrojů – Azure | Dokumentace Microsoftu
description: Nástroje Machine learning a datové vědy
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9a9dc868c4f22f95ca5027e3c95513d176c69eac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392394"
---
# <a name="machine-learning-and-data-science-tools"></a>Nástroje Machine learning a datové vědy
Na Data virtuálního počítače VĚDY obsahuje bohatou sadu nástrojů a knihovny pro machine learning k dispozici v oblíbenými jazyky, jako je Python, R, Julia. 

Tady jsou některé strojového učení, nástroje a knihovny na datové VĚDY. 

## <a name="azure-machine-learning-servicehttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Služba Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Co je to?   |   Služba Azure Machine Learning je Cloudová služba, která můžete použít k vývoji a nasazování modelů strojového učení.  Modely můžete sledovat, jak vytvářet, trénovat, škálovat a spravovat je pomocí sady Python SDK. Nasazení modelů jako kontejnery a spouštět v cloudu, místního prostředí nebo na hraničních zařízeních IoT.   |
| Podporované DSVM edice     | Windows (prostředí Conda: Azure ml), Linux (prostředí Conda: py36)    |
| Typické použití      | Obecné ML platformy      |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      |  Nainstalovaná s podporou GPU   |
| Jak používat nebo ji spustit?      | Python SDK a nástroje příkazového řádku Azure (AZ rozhraní příkazového řádku). Aktivovat prostředí conda `AzureML` na verzi Windows nebo na `py36` na edice pro Linux.      |
| Odkazy na ukázky      | Poznámkové bloky Jupyter ukázky jsou součástí `AzureML` adresáře v části poznámkových bloků  |
| Související nástroje na datové VĚDY      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co je to?   |    Rychlá, přenosné a distribuované přechodu zvýšení skóre (GBDT, GBRT nebo GBM) knihovny pro Python, R, Java, Scala, C++ a další. Běží na jednom počítači, Hadoop, Spark    |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné knihovny ML      |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      |  Nainstalovaná s podporou GPU   |
| Jak používat nebo ji spustit?      | Jako knihovna Python 2.7 a 3.5 balíčků R a nástroje příkazového řádku cestu (`C:\dsvm\tools\xgboost\bin\xgboost.exe` pro Windows, `/dsvm/tools/xgboost/xgboost` pro Linux)    |
| Odkazy na ukázky      | Ukázky jsou součástí na virtuálním počítači, `/dsvm/tools/xgboost/demo` v Linuxu a `C:\dsvm\tools\xgboost\demo` na Windows   |
| Související nástroje na datové VĚDY      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co je to?   |   Vowpal Wabbit (označované také jako "Zobrazit") je opensourcová rychlé out z core učení systémová knihovna    |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné knihovny ML      |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      |  Windows – instalační program msi, Linux - apt-get |
| Jak používat nebo ji spustit?      | Jako nástroj příkazového řádku na cestu (`C:\Program Files\VowpalWabbit\vw.exe` na Windows, `/usr/bin/vw` v Linuxu)    |
| Odkazy na ukázky      | [Ukázky VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Související nástroje na datové VĚDY      |XGBoost LightGBM MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Weka je kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy můžete buď použít přímo k datové sadě nebo ji volat z kódu Java. Weka obsahuje nástroje potřebné pro předběžné zpracování dat, klasifikace, regrese, clusteringu, asociačních pravidel a vizualizace. |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné nástroje ML     |
| Jak používat nebo ji spustit?      | Na Windows vyhledejte Weka v nabídce Start. V Linuxu, přihlaste se pomocí X2Go a pak přejděte do aplikace -> Vývoj -> Weka. |
| Odkazy na ukázky      | [Ukázky weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje na datové VĚDY      |XGBooost LightGBM Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí jazyka R   |
| Podporované DSVM edice     | Windows, Linux     |
| Typické použití      | Obecné uživatelského rozhraní nástroje dolování dat pro R    |
| Jak používat nebo ji spustit?      | Uživatelské rozhraní nástroje. Na Windows, spusťte příkazový řádek, využívejte jazyk R, pak v R spustit `rattle()`. V Linuxu, spojte se s X2Go, spusťte terminál, spusťte R a potom v R spustit `rattle()`. |
| Odkazy na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje na datové VĚDY      |XGBoost LightGBM, Weka,   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co je to?   | Rychlé, distribuovaná, vysoce výkonné přechodem zvýšení skóre (GBDT, GBRT, GBM nebo TRŽIŠTĚ) rozhraní založené na algoritmy rozhodovacího stromu, použít pro hodnocení, klasifikace a mnoho dalších strojového učení úlohy.    |
| Podporované DSVM verze      | Windows, Linux    |
| Typické použití      | Rozhraní pro obecné účely přechodu zvýšení skóre      |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      | Na Windows je nainstalován LightGBM jako balíček Pythonu. V systému Linux, je spustitelný soubor příkazového řádku v `/opt/LightGBM/lightgbm`, instalaci balíčku R a Python balíčky se nainstalují.     |
| Odkazy na ukázky      | [Průvodce LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Související nástroje na datové VĚDY      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co je to?   | Platforma AI open source podpora v paměti, distribuovaného, rychlé a škálovatelného strojového učení  |
| Podporované DSVM verze      | Linux   |
| Typické použití      | Obecné účely distribuovaných, škálovatelných ML   |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      | H2O se instaluje v `/dsvm/tools/h2o`.      |
| Jak používat nebo ji spustit?      | Připojení k virtuálnímu počítači pomocí X2Go. Spusťte novém terminálu a spusťte `java -jar /dsvm/tools/h2o/current/h2o.jar`. Potom spusťte webový prohlížeč a připojte se k `http://localhost:54321`.      |
| Odkazy na ukázky      | Ukázky jsou dostupné na virtuálním počítači v Jupyter v rámci `h2o` adresáře.      |
| Související nástroje na datové VĚDY      | Apache Spark, MXNet, XGBoost, šumivá vody, hloubkové vody    |

Existuje několik dalších knihoven ML na datové VĚDY jako oblíbené `scikit-learn` balíček, který je součástí dané distribuce Anaconda Python, který je nainstalován na datové VĚDY. Nezapomeňte se podívat na seznam balíčků dostupných v Pythonu, R a Julie spuštěním Správce příslušných balíčků. 
