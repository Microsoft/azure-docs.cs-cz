---
title: Machine learning a datové vědy nástrojů – Azure | Dokumentace Microsoftu
description: Další informace o machine learningu nástrojů a architektur, které jsou předem nainstalované na virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: d8c607b5f1d338ca3a2bd3844b26ef51d801c720
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086066"
---
# <a name="machine-learning-and-data-science-tools"></a>Nástroje Machine learning a datové vědy
Azure virtuální počítače pro datové vědy obsahuje bohatou sadu nástrojů a knihovny pro machine learning (ML) k dispozici v oblíbených jazyků, jako je Python, R a Julie. 

Tady jsou některé z knihoven na virtuální počítače pro datové vědy a ML nástrojů. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Co je to?   |   Azure Machine Learning je Cloudová služba, která můžete použít k vývoji a nasazování modelů ML. Modely můžete sledovat, jak vytvářet, trénovat, škálování a spravovat je pomocí sady Python SDK. Nasazení modelů jako kontejnery a spusťte je v cloudu, v místním prostředí nebo ve službě Azure IoT Edge.   |
| Podporované edice     | Windows (prostředí conda: Azure ml), Linux (prostředí conda: py36)    |
| Typické použití      | Obecné ML platformy      |
| Jak je nakonfigurován nebo nainstalovat?      |  Nainstalovaná s podporou GPU   |
| Jak používat nebo ji spustit      | Jako Python SDK a Azure CLI. Aktivovat prostředí conda `AzureML` v edici Windows *nebo* k `py36` na edice pro Linux.      |
| Odkaz na ukázky      | Jsou součástí ukázkové poznámkové bloky Jupyter `AzureML` adresáře v části poznámkových bloků.  |
| Související nástroje      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co je to?   |    XGBoost je rychlý, přenosných a distribuované přechod zvýšení skóre (GBDT, GBRT nebo GBM) knihovny pro Python, R, Java, Scala, C++ a další. Běží na jednom počítači, Hadoop a Spark.    |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecné knihovny ML      |
| Jak je nakonfigurován nebo nainstalovat?      |  Nainstalovaná s podporou GPU   |
| Jak používat nebo ji spustit      | Jako Python knihovny (2.7 a 3.5), balíček R a nástroje příkazového řádku cestu (`C:\dsvm\tools\xgboost\bin\xgboost.exe` pro Windows, `/dsvm/tools/xgboost/xgboost` pro Linux)    |
| Odkazy na ukázky      | Ukázky jsou součástí na virtuálním počítači, `/dsvm/tools/xgboost/demo` v Linuxu, a `C:\dsvm\tools\xgboost\demo` na Windows.   |
| Související nástroje      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co je to?   |   Vowpal Wabbit (označované také jako "Zobrazit") je open source, rychlé, out z core učení knihovna systému.    |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecné knihovny ML      |
| Jak je nakonfigurován nebo nainstalovat?      |  Windows – instalační program msi, Linux – apt-get |
| Jak používat nebo ji spustit      | Jako nástroj příkazového řádku na cestu (`C:\Program Files\VowpalWabbit\vw.exe` na Windows, `/usr/bin/vw` v Linuxu)    |
| Odkaz na ukázky      | [Ukázky VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Související nástroje      |XGBoost LightGBM MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Weka je kolekce algoritmy pro úlohy dolování dat. Algoritmy lze použít přímo na datové sady nebo volat z kódu Java. Weka obsahuje nástroje potřebné pro předběžné zpracování dat, klasifikace, regrese, clusteringu, asociačních pravidel a vizualizace. |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecné nástroje ML     |
| Jak používat nebo ji spustit      | Na Windows vyhledejte v nabídce Start Weka. V Linuxu, přihlaste se pomocí X2Go a potom přejděte ke **aplikací** > **vývoj** > **Weka**. |
| Odkaz na ukázky      | [Ukázky weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje      |XGBoost LightGBM Rattle,   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Rattle je grafické uživatelské rozhraní pro dolování dat s použitím R.   |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecné uživatelského rozhraní nástroje pro dolování dat pro R    |
| Jak používat nebo ji spustit      | Uživatelské rozhraní nástroje. Na Windows, spusťte příkazový řádek, spusťte R a pak spusťte uvnitř R `rattle()`. V Linuxu, spojte se s X2Go, spusťte terminál, spusťte R a pak spusťte uvnitř R `rattle()`. |
| Odkaz na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje      |XGBoost LightGBM, Weka,   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Co je to?   | LightGBM je rychlé, distribuovaná, vysoce výkonné přechodem zvýšení skóre (GBDT, GBRT, GBM nebo TRŽIŠTĚ) rozhraní založené na algoritmy rozhodovacího stromu. Používá se pro hodnocení, klasifikace a mnoho dalších úkolů ML.    |
| Podporované verze      | Windows, Linux    |
| Typické použití      | Rozhraní pro obecné účely přechodu zvýšení skóre      |
| Jak je nakonfigurován nebo nainstalovat?      | Na Windows je nainstalován LightGBM jako balíček Pythonu. V systému Linux, je spustitelný soubor příkazového řádku v `/opt/LightGBM/lightgbm`, instalaci balíčku R a Python balíčky se nainstalují.     |
| Odkaz na ukázky      | [Průvodce LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Související nástroje      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Co je to?   | H2O je open source platforma AI, která podporuje v paměti, distribuované, rychlé a škálovatelné ML.  |
| Podporované verze      | Linux   |
| Typické použití      | Pro obecné účely distribuované a škálovatelné ML   |
| Jak je nakonfigurován nebo nainstalovat?      | H2O se instaluje v `/dsvm/tools/h2o`.      |
| Jak používat nebo ji spustit      | Připojení k virtuálnímu počítači s použitím X2Go. Spusťte novém terminálu a spusťte `java -jar /dsvm/tools/h2o/current/h2o.jar`. Potom spusťte webový prohlížeč a připojte se k `http://localhost:54321`.      |
| Odkaz na ukázky      | Ukázky jsou dostupné na virtuálním počítači v Jupyter v části `h2o` adresáře.      |
| Související nástroje      | Apache Spark, MXNet, XGBoost, šumivá vody, hloubkové vody    |

Existuje několik dalších knihoven ML na virtuální počítače pro datové vědy, jako je například oblíbené `scikit-learn` balíček, který je součástí dané distribuce Anaconda Python, který je nainstalován na virtuální počítače pro datové vědy. Podívejte se na seznam balíčků dostupných v Pythonu, R a Helena, spuštěním Správce příslušných balíčků.
