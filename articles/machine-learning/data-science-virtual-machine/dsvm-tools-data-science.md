---
title: Nástroje strojového učení a datové vědy
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a architekturami strojového učení, které jsou předinstalovány ve virtuálním počítači pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282303"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Nástroje strojového učení a datové vědy na virtuálních počítačích Azure Data Science
Virtuální počítače Azure Data Science (DSVM) mají bohatou sadu nástrojů a knihoven pro strojové učení dostupné v oblíbených jazycích, jako je Python, R a Julia.

Tady jsou některé nástroje a knihovny strojového učení na dsvm.

## <a name="azure-machine-learning-sdk-for-python"></a>Sada Azure Machine Learning SDK pro Python

Podívejte se na úplný odkaz na [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Co je to?   |   Azure Machine Learning je cloudová služba, kterou můžete použít k vývoji a nasazení modelů strojového učení. Modely můžete sledovat při jejich vytváření, trénování, škálování a správě pomocí sady Python SDK. Nasazujte modely jako kontejnery a spouštějte je v cloudu, místně nebo na Azure IoT Edge.   |
| Podporované edice     | Windows (prostředí conda: AzureML), Linux (conda prostředí: py36)    |
| Typická použití      | Obecná platforma strojového učení      |
| Jak je nakonfigurován nebo nainstalován?      |  Nainstalováno s podporou GPU   |
| Jak ji používat nebo spouštět      | Jako sada Python SDK a v azure cli. Aktivujte do prostředí `AzureML` conda v edici Windows *nebo* `py36` na linuxové edici.      |
| Odkaz na ukázky      | Ukázkové poznámkové bloky Jupyter jsou zahrnuty v adresáři `AzureML` pod poznámkovými bloky.  |
| Související nástroje      | Visual Studio Kód, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Co je to?   | Open source platforma AI, která podporuje v paměti, distribuované, rychlé a škálovatelné strojové učení.  |
| Podporované verze      | Linux   |
| Typická použití      | Všeobecné distribuované a škálovatelné strojové učení   |
| Jak je nakonfigurován nebo nainstalován?      | H2O je `/dsvm/tools/h2o`nainstalován v aplikaci .      |
| Jak ji používat nebo spouštět      | Připojte se k virtuálnímu virtuálnímu zařízení pomocí X2Go. Spusťte nový terminál `java -jar /dsvm/tools/h2o/current/h2o.jar`a spusťte . Potom spusťte webový `http://localhost:54321`prohlížeč a připojte se k aplikaci .      |
| Odkaz na ukázky      | Ukázky jsou k dispozici na virtuálním `h2o` počítači v Jupyter pod adresářem.      |
| Související nástroje      | Apache Spark, MXNet, XGBoost, Perlivá voda, Hluboká Voda    |

Existuje několik dalších knihoven strojového učení na DSVMs, jako je například populární `scikit-learn` balíček, který je součástí distribuce Anaconda Python pro DSVMs. Chcete-li se podívat na seznam balíčků dostupných v Pythonu, R a Julii, spusťte příslušné správce balíčků.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Co je to?   | Rychlé, distribuované, vysoce výkonné zvýšení přechodu (GBDT, GBRT, GBM nebo MART) framework založený na algoritmech rozhodovacího stromu. Používá se pro hodnocení, klasifikaci a mnoho dalších úloh strojového učení.    |
| Podporované verze      | Windows, Linux    |
| Typická použití      | Univerzální rámec pro podporu přechodu      |
| Jak je nakonfigurován nebo nainstalován?      | V systému Windows je lightgbm nainstalován jako balíček Pythonu. V Linuxu je spustitelný soubor `/opt/LightGBM/lightgbm`příkazového řádku v aplikaci , je nainstalován balíček R a nainstalovány balíčky Pythonu.     |
| Odkaz na ukázky      | [Průvodce LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Související nástroje      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Co je to?   |   Grafické uživatelské rozhraní pro dolování dat pomocí R.   |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecný nástroj pro dolování dat ui pro R    |
| Jak ji používat nebo spouštět      | Jako nástroj pro ui. V systému Windows spusťte příkazový řádek, `rattle()`spusťte R a potom v klávese R spusťte program . Na Linuxu se spojte s X2Go, spusťte terminál, spusťte R a pak uvnitř R, spusťte `rattle()`. |
| Odkaz na ukázky      | [Rattle](https://togaware.com/onepager/) |
| Související nástroje      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Co je to?   |   Rychlá, open-source, out-of-core výukové ho systému knihovny    |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecná knihovna strojového učení      |
| Jak je nakonfigurován nebo nainstalován?      |  Windows: instalační program msi<br/>Linux: apt-get |
| Jak ji používat nebo spouštět      | Jako nástroj příkazového řádku na`C:\Program Files\VowpalWabbit\vw.exe` cestě `/usr/bin/vw` (v systému Windows, na Linuxu)    |
| Odkaz na ukázky      | [VowPal Wabbit vzorky](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Související nástroje      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Co je to?   |  Kolekce algoritmů strojového učení pro úlohy dolování dat. Algoritmy lze použít buď přímo na datovou sadu, nebo volat z vlastního java kódu. Weka obsahuje nástroje pro předběžné zpracování dat, klasifikaci, regresi, clustering, pravidla přidružení a vizualizaci. |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecný nástroj strojového učení     |
| Jak ji používat nebo spouštět      | V systému Windows vyhledejte weka v nabídce **Start.** Na Linuxu se přihlaste pomocí X2Go a pak přejděte na **Applications** > **Development** > **Weka**. |
| Odkaz na ukázky      | [Weka vzorky](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Související nástroje      |LightGBM, Chrastítko, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Co je to?   |   Rychlá, přenosná a distribuovaná knihovna pro podporu přechodu (GBDT, GBRT nebo GBM) pro Python, R, Java, Scala, C++ a další. Běží na jednom stroji a na Apache Hadoop a Spark.    |
| Podporované edice     | Windows, Linux     |
| Typická použití      | Obecná knihovna strojového učení      |
| Jak je nakonfigurován nebo nainstalován?      |  Nainstalováno s podporou GPU   |
| Jak ji používat nebo spouštět      | Jako knihovna Pythonu (2.7 a 3.5), balíček R`C:\dsvm\tools\xgboost\bin\xgboost.exe` a nástroj `/dsvm/tools/xgboost/xgboost` příkazového řádku na cestě (pro Windows a pro Linux)    |
| Odkazy na ukázky      | Ukázky jsou součástí virtuálního `/dsvm/tools/xgboost/demo` počítače, `C:\dsvm\tools\xgboost\demo` v Linuxu a v systému Windows.   |
| Související nástroje      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Co je to?   | Open-source SQL dotazovací stroj na velké objemy dat    |
| Podporované verze DSVM      | Windows 2019, Linux  |
| Jak je konfigurován a nainstalován na DSVM?      |  Nainstalováno `/dsvm/tools/drill*` pouze ve vloženém režimu   |
| Typická použití      |  Pro zkoumání dat na místě bez nutnosti extrahování, transformace, zatížení (ETL). Dotazujte se na různé zdroje a formáty dat, včetně CSV, JSON, relačních tabulek a Hadoopu.     |
| Jak ji používat a spouštět      | Zástupce na ploše  <br/> [Začínáme s cvičením Drill za 10 minut](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Související nástroje na DSVM      |   Chrastítko, Weka, SQL Server Management Studio      |


