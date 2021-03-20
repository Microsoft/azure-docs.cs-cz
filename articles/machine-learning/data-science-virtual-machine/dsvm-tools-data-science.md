---
title: Machine Learning a nástroje pro datové vědy
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji pro strojové učení a architekturami, které jsou předinstalované na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: ac71d8d7830f1d740d0156886a364b1d53ab247f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519554"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine Learning a nástroje pro datové vědy na Azure Data věda Virtual Machines
Azure Data věda Virtual Machines (DSVMs) mají bohatou sadu nástrojů a knihoven pro strojové učení, které jsou dostupné v oblíbených jazycích, jako je Python, R nebo Helena.

Tady jsou některé z nástrojů pro strojové učení a knihovny v DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>Sada Azure Machine Learning SDK pro Python

Podívejte se na kompletní odkaz na [sadu SDK Azure Machine Learning pro Python](../overview-what-is-azure-ml.md).

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   |   Azure Machine Learning je cloudová služba, kterou můžete použít k vývoji a nasazení modelů strojového učení. Modely můžete sledovat při sestavování, školení, škálování a správě pomocí sady Python SDK. Nasaďte modely jako kontejnery a spouštějte je v cloudu, v místním prostředí nebo na Azure IoT Edge.   |
| Podporované edice     | Windows (prostředí conda: AzureML), Linux (prostředí conda: py36)    |
| Typická použití      | Obecná platforma pro strojové učení      |
| Jak je nakonfigurovaná nebo nainstalovaná?      |  Instalace s podporou GPU   |
| Jak ji použít nebo spustit      | Jako SDK Pythonu a v Azure CLI. Aktivujte prostředí conda `AzureML` v edici Windows *nebo* na `py36` edici Linux.      |
| Odkaz na ukázky      | Ukázky Jupyter poznámkových bloků jsou součástí `AzureML` adresáře v poznámkových blocích.  |
| Související nástroje      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Open Source platforma AI podporující v paměti, distribuované, rychlé a škálovatelné strojové učení.  |
| Podporované verze      | Linux   |
| Typická použití      | Distribuované a škálovatelné strojové učení pro obecné účely   |
| Jak je nakonfigurovaná nebo nainstalovaná?      | V nástroji je nainstalováno `/dsvm/tools/h2o` .      |
| Jak ji použít nebo spustit      | Připojte se k virtuálnímu počítači pomocí X2Go. Spusťte nový terminál a spusťte příkaz `java -jar /dsvm/tools/h2o/current/h2o.jar` . Pak spusťte webový prohlížeč a připojte se k `http://localhost:54321` .      |
| Odkaz na ukázky      | Ukázky jsou k dispozici na virtuálním počítači v Jupyter v `h2o` adresáři.      |
| Související nástroje      | Apache Spark, MXNet, XGBoost, Sparková voda, hluboká voda    |

K dispozici je několik dalších knihoven strojového učení v DSVMs, jako je oblíbený `scikit-learn` balíček, který je součástí distribuce Pythonu Anaconda pro DSVMs. Pokud chcete zjistit seznam balíčků dostupných v Pythonu, R a Helena, spusťte příslušné správce balíčků.

## <a name="lightgbm"></a>LightGBM

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Rychlé a distribuované prostředí s vysokým výkonem (GBDT, GBRT, GBM nebo TRŽIŠTě) založené na algoritmech rozhodovacího stromu. Používá se pro hodnocení, klasifikaci a mnoho dalších úloh strojového učení.    |
| Podporované verze      | Windows, Linux    |
| Typická použití      | Architektura pro posilování přechodu na obecné účely      |
| Jak je nakonfigurovaná nebo nainstalovaná?      | Ve Windows se LightGBM nainstaluje jako balíček Pythonu. V systému Linux je spustitelný soubor příkazového řádku v systému `/opt/LightGBM/lightgbm` , je nainstalován balíček R a jsou nainstalovány balíčky python.     |
| Odkaz na ukázky      | [Průvodce LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Související nástroje      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   |   Grafické uživatelské rozhraní pro dolování dat pomocí jazyka R.   |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecná data uživatelského rozhraní – nástroj dolování pro R    |
| Jak ji použít nebo spustit      | Jako nástroj uživatelského rozhraní. V systému Windows spusťte příkazový řádek, spusťte R a potom v jazyce R spusťte příkaz `rattle()` . V systému Linux se připojte pomocí X2Go, spusťte terminál, spusťte R a potom v jazyce R spusťte příkaz `rattle()` . |
| Odkaz na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   |   Rychlá a open source knihovna výukového systému, která je mimo jádro    |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecná knihovna strojového učení      |
| Jak je nakonfigurovaná nebo nainstalovaná?      |  Windows: Instalační program MSI<br/>Linux: apt-get |
| Jak ji použít nebo spustit      | Jako nástroj příkazového řádku on-Path ( `C:\Program Files\VowpalWabbit\vw.exe` ve Windows, `/usr/bin/vw` na Linux)    |
| Odkaz na ukázky      | [Ukázky pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Související nástroje      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   |  Kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy mohou být buď aplikovány přímo na sadu dat, nebo volány z vlastního kódu Java. Weka obsahuje nástroje pro předběžné zpracování dat, klasifikaci, regresi, clusteringu, pravidla přidružení a vizualizaci. |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecný nástroj pro strojové učení     |
| Jak ji použít nebo spustit      | Ve Windows vyhledejte weka v nabídce **Start** . V systému Linux se přihlaste pomocí X2Go a pak navštivte **aplikace**  >  **vývoj** aplikací  >  **weka**. |
| Odkaz na ukázky      | [Ukázky weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   |   Rychlá, přenosná a distribuovaná knihovna pro posilování barev (GBDT, GBRT nebo GBM) pro Python, R, Java, Scala, C++ a další. Spouští se na jednom počítači a na Apache Hadoop a Sparku.    |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecná knihovna strojového učení      |
| Jak je nakonfigurovaná nebo nainstalovaná?      |  Instalace s podporou GPU   |
| Jak ji použít nebo spustit      | Jako knihovna Pythonu (2,7 a 3,5), balíček R a nástroj příkazového řádku na cestě ( `C:\dsvm\tools\xgboost\bin\xgboost.exe` pro Windows a `/dsvm/tools/xgboost/xgboost` pro Linux)    |
| Odkazy na ukázky      | Ukázky jsou součástí virtuálních počítačů, v `/dsvm/tools/xgboost/demo` systémech Linux a `C:\dsvm\tools\xgboost\demo` Windows.   |
| Související nástroje      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Open Source dotazovací modul SQL pro velké objemy dat    |
| Podporované verze DSVM      | Windows 2019, Linux  |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      |  Nainstalováno v `/dsvm/tools/drill*` pouze v integrovaném režimu   |
| Typická použití      |  Pro průzkum místních dat bez nutnosti extrakce, transformace, načítání (ETL). Dotazování různých zdrojů dat a formátů, včetně sdílených svazků clusteru, formátu JSON, relačních tabulek a Hadoop.     |
| Jak použít a spustit      | Zástupce na ploše  <br/> [Začínáme s procházením za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Související nástroje na DSVM      |   Rattle, weka, SQL Server Management Studio      |