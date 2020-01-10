---
title: Nástroje Machine learning a datové vědy
description: Seznamte se s nástroji pro strojové učení a architekturami, které jsou předinstalované na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c77e1834804dba0d2e5fd6eaec574ffce19d1d45
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771202"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine Learning a nástroje pro datové vědy na Azure Data věda Virtual Machines
Azure Data věda Virtual Machines (DSVMs) mají bohatou sadu nástrojů a knihoven pro strojové učení, které jsou dostupné v oblíbených jazycích, jako je Python, R nebo Helena.

Tady jsou některé z nástrojů pro strojové učení a knihovny v DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>Sada Azure Machine Learning SDK pro Python

Podívejte se na kompletní odkaz na [sadu SDK Azure Machine Learning pro Python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Co je to?   |   Azure Machine Learning je cloudová služba, kterou můžete použít k vývoji a nasazení modelů strojového učení. Modely můžete sledovat, jak vytvářet, trénovat, škálování a spravovat je pomocí sady Python SDK. Nasazení modelů jako kontejnery a spusťte je v cloudu, v místním prostředí nebo ve službě Azure IoT Edge.   |
| Podporované edice     | Windows (prostředí conda: Azure ml), Linux (prostředí conda: py36)    |
| Typické použití      | Obecná platforma pro strojové učení      |
| Jak je nakonfigurován nebo nainstalovat?      |  Nainstalovaná s podporou GPU   |
| Jak používat nebo ji spustit      | Jako SDK Pythonu a v Azure CLI. Aktivovat prostředí conda `AzureML` v edici Windows *nebo* k `py36` na edice pro Linux.      |
| Odkaz na ukázky      | Jsou součástí ukázkové poznámkové bloky Jupyter `AzureML` adresáře v části poznámkových bloků.  |
| Související nástroje      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Co je to?   | Open Source platforma AI podporující v paměti, distribuované, rychlé a škálovatelné strojové učení.  |
| Podporované verze      | Linux   |
| Typické použití      | Distribuované a škálovatelné strojové učení pro obecné účely   |
| Jak je nakonfigurován nebo nainstalovat?      | H2O se instaluje v `/dsvm/tools/h2o`.      |
| Jak používat nebo ji spustit      | Připojení k virtuálnímu počítači s použitím X2Go. Spusťte novém terminálu a spusťte `java -jar /dsvm/tools/h2o/current/h2o.jar`. Potom spusťte webový prohlížeč a připojte se k `http://localhost:54321`.      |
| Odkaz na ukázky      | Ukázky jsou dostupné na virtuálním počítači v Jupyter v části `h2o` adresáře.      |
| Související nástroje      | Apache Spark, MXNet, XGBoost, šumivá vody, hloubkové vody    |

K dispozici je několik dalších knihoven strojového učení v DSVMs, jako je oblíbený `scikit-learn` balíček, který je součástí distribuce Pythonu Anaconda pro DSVMs. Podívejte se na seznam balíčků dostupných v Pythonu, R a Helena, spuštěním Správce příslušných balíčků.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Co je to?   | Rychlé a distribuované prostředí s vysokým výkonem (GBDT, GBRT, GBM nebo TRŽIŠTě) založené na algoritmech rozhodovacího stromu. Používá se pro hodnocení, klasifikaci a mnoho dalších úloh strojového učení.    |
| Podporované verze      | Windows, Linux    |
| Typické použití      | Architektura pro posilování přechodu na obecné účely      |
| Jak je nakonfigurován nebo nainstalovat?      | Na Windows je nainstalován LightGBM jako balíček Pythonu. V systému Linux, je spustitelný soubor příkazového řádku v `/opt/LightGBM/lightgbm`, instalaci balíčku R a Python balíčky se nainstalují.     |
| Odkaz na ukázky      | [Průvodce LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Související nástroje      | MXNet XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí jazyka R.   |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecná data uživatelského rozhraní – nástroj dolování pro R    |
| Jak používat nebo ji spustit      | Jako nástroj uživatelského rozhraní. V systému Windows spusťte příkazový řádek, spusťte R a potom v jazyce R spusťte `rattle()`. V systému Linux se připojte pomocí X2Go, spusťte terminál, spusťte R a potom v jazyce R spusťte `rattle()`. |
| Odkaz na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje      |XGBoost LightGBM, Weka,   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co je to?   |   Rychlá a open source knihovna výukového systému, která je mimo jádro    |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecná knihovna strojového učení      |
| Jak je nakonfigurován nebo nainstalovat?      |  Windows: Instalační program MSI<br/>Linux: apt-get |
| Jak používat nebo ji spustit      | Jako nástroj příkazového řádku na cestu (`C:\Program Files\VowpalWabbit\vw.exe` na Windows, `/usr/bin/vw` v Linuxu)    |
| Odkaz na ukázky      | [Ukázky VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Související nástroje      |XGBoost LightGBM MXNet,   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy lze použít přímo na datové sady nebo volat z kódu Java. Weka obsahuje nástroje potřebné pro předběžné zpracování dat, klasifikace, regrese, clusteringu, asociačních pravidel a vizualizace. |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecný nástroj pro strojové učení     |
| Jak používat nebo ji spustit      | Ve Windows vyhledejte weka v nabídce **Start** . V Linuxu, přihlaste se pomocí X2Go a potom přejděte ke **aplikací** > **vývoj** > **Weka**. |
| Odkaz na ukázky      | [Ukázky weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje      |XGBoost LightGBM Rattle,   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co je to?   |   Rychlá, přenosná a distribuovaná knihovna pro posilování barev (GBDT, GBRT nebo GBM) pro Python, R, Java, Scala, C++a další. Spouští se na jednom počítači a na Apache Hadoop a Sparku.    |
| Podporované edice     | Windows, Linux     |
| Typické použití      | Obecná knihovna strojového učení      |
| Jak je nakonfigurován nebo nainstalovat?      |  Nainstalovaná s podporou GPU   |
| Jak používat nebo ji spustit      | Jako knihovna Pythonu (2,7 a 3,5), balíček R a nástroj příkazového řádku on-Path (`C:\dsvm\tools\xgboost\bin\xgboost.exe` pro Windows a `/dsvm/tools/xgboost/xgboost` pro Linux)    |
| Odkazy na ukázky      | Ukázky jsou součástí na virtuálním počítači, `/dsvm/tools/xgboost/demo` v Linuxu, a `C:\dsvm\tools\xgboost\demo` na Windows.   |
| Související nástroje      | LightGBM MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co je to?   | Open Source dotazovací modul SQL pro velké objemy dat    |
| Podporované verze DSVM      | Windows 2019 (Preview), Linux  |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      |  Nainstalováno v `/dsvm/tools/drill*` pouze v režimu Embedded   |
| Typické použití      |  Pro průzkum místních dat bez nutnosti extrakce, transformace, načítání (ETL). Dotazování různých zdrojů dat a formátů, včetně sdílených svazků clusteru, formátu JSON, relačních tabulek a Hadoop.     |
| Jak použít a spustit      | Zástupce na ploše  <br/> [Začínáme s procházením za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Související nástroje na DSVM      |   Rattle, weka, SQL Server Management Studio      |


