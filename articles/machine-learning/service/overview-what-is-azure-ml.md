---
title: Co je Azure Machine Learning? | Dokumenty Microsoft
description: Dočtete se o základních konceptech strojového učení v cloudu, dozvíte se, k čemu ho můžete využít, a seznámíte se s terminologií strojového učení. Přehled služby Azure Machine Learning, která představuje integrované, komplexní řešení datové vědy pro profesionální datové vědce, které slouží k vývoji, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 09/21/2017
ms.openlocfilehash: 3e744b0e4a7ccebcdedac5a822ff717bed6b1f72
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268412"
---
# <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Pomocí strojového učení se počítače můžou učit, aniž by byly explicitně naprogramované.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Při online nakupování je díky strojovému učení možné na základě již pořízeného zboží doporučit produkty, které by se vám mohly líbit. Po protažení platební karty čtečkou porovnává strojově učený proces danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="what-is-azure-machine-learning"></a>Co je Azure Machine Learning?
Azure Machine Learning je integrované, komplexní řešení datové vědy a pokročilé analýzy. Umožňuje datovým vědcům připravovat data, vyvíjet experimenty a nasazovat modely na úrovni cloudu.

Azure Machine Learning má tyto hlavní součásti:
- Aplikace Azure Machine Learning Workbench
- Experimentování ve službě Azure Machine Learning
- Správa modelů ve službě Azure Machine Learning
- Knihovny Microsoft Machine Learning pro Apache Spark (knihovna MMLSpark)
- Visual Studio Code Tools pro AI

Tyto aplikace a služby společně pomáhají výrazně urychlit vývoj a nasazení vašich projektů datové vědy. 

![Koncepty služby Azure Machine Learning](./media/overview-what-is-azure-ml/aml-concepts.png)


## <a name="open-source-compatible"></a>Kompatibilita s technologiemi open source

Azure Machine Learning plně podporuje technologie s otevřeným zdrojem (open source). Můžete používat desetitisíce balíčků Python s otevřeným zdrojem, například následující architektury strojového učení:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Své experimenty můžete spouštět ve spravovaných prostředích, jako jsou kontejnery Docker a clustery Spark. Můžete také k urychlení spouštění používat pokročilý hardware, jako jsou [virtuální počítače s povoleným grafickým procesorem v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

Řešení Azure Machine Learning je postavené na těchto technologiích s otevřeným zdrojem:

- [Poznámkový blok Jupyter](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Zahrnuje také vlastní technologie s otevřeným zdrojem Microsoftu, například [knihovnu Microsoft Machine Learning pro Apache Spark](https://github.com/Azure/mmlspark) a sadu Cognitive Toolkit.

Kromě toho můžete také využívat některé z nejvyspělejších a nejlépe prověřených technologií strojového učení vyvinutých Microsoftem, které slouží k řešení problémů ve velkém měřítku. Ty se osvědčily v boji v řadě produktů Microsoftu, jako jsou tyto:

- Windows
- Bing
- Xbox
- Office
- SQL Server

Toto jsou některé technologie strojového učení Microsoftu, které jsou součástí služby Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (syntéza programů využívající příklady)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Aplikace Azure Machine Learning Workbench
Azure Machine Learning Workbench je desktopová aplikace s nástroji příkazového řádku podporovaná v systému Windows i macOS. Umožňuje spravovat řešení strojového učení v průběhu celého životního cyklu datové vědy:

- Přijímání a příprava dat
- Vývoj modelů a správa experimentů
- Nasazení modelů v různých cílových prostředích

Toto jsou základní funkce, které nabízí aplikace Azure Machine Learning Workbench:

- Nástroj pro přípravu dat, který se dokáže na příkladech učit logiku transformace dat
- Abstrakce zdrojů dat přístupná přes uživatelské prostředí a kód Python
- Sada Python SDK pro volání vizuálně zkonstruovaných balíčků pro přípravu dat
- Integrovaná služba Jupyter Notebook a klientské uživatelské prostředí
- Monitorování a správa experimentů prostřednictvím zobrazení historie spuštění
- Řízení přístupu na základě rolí, které umožňuje sdílení a spolupráci prostřednictvím Azure Active Directory
- Automatické snímky projektu pro každé spuštění a explicitní správa verzí díky nativní integraci Gitu 
- Integrace s oblíbenými integrovanými vývojovými prostředími (IDE) Python

Další informace najdete v následujících článcích:
- [Uživatelský průvodce přípravou dat](../desktop-workbench/data-prep-user-guide.md)
- [Použití Gitu se službou Azure Machine Learning](../desktop-workbench/using-git-ml-project.md)
- [Použití aplikace Jupyter Notebook ve službě Azure Machine Learning](../desktop-workbench/how-to-use-jupyter-notebooks.md)
- [Roaming a sdílení](../desktop-workbench/roaming-and-collaboration.md)
- [Průvodce historií spuštění](../desktop-workbench/how-to-use-run-history-model-metrics.md)
- [Integrace integrovaného vývojového prostředí (IDE)](../desktop-workbench/how-to-configure-your-ide.md)

## <a name="azure-machine-learning-experimentation-service"></a>Experimentování ve službě Azure Machine Learning
Služba Experimentování zpracovává spouštění experimentů strojového učení. Poskytuje také podporu aplikaci Workbench v podobě správy projektů, integrace Gitu, řízení přístupu, roamingu a sdílení. 

Po jednoduché konfiguraci můžete spouštět své experimenty v různých výpočetních prostředích:

- Místní nativní
- Místní kontejner Docker
- Kontejner Docker ve vzdáleném virtuálním počítači
- Horizontální navýšení kapacity clusteru Spark v Azure

Služba Experimentování vytváří virtuální prostředí, abyste mohli zjistit, jestli se dá váš skript izolovaně spouštět s reprodukovatelnými výsledky. Zaznamenává informace o historii spuštění a poskytuje vám vizuální znázornění této historie. Z dostupných spuštění experimentu si můžete snadno vybrat nejlepší model. 

Další informace najdete v tématu [Konfigurace služby Experimentování](../desktop-workbench/experimentation-service-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Správa modelů ve službě Azure Machine Learning

Služba Správa modelů umožňuje datovým vědcům a týmům vývojářů nasazovat prediktivní modely v nejrůznějších prostředích. Od tréninkových spuštění až po nasazení se sledují verze modelů a rodokmeny. Modely se ukládají, registrují a spravují v cloudu. 

Pomocí jednoduchých příkazů v rozhraní příkazového řádku můžete umístit svůj model, hodnoticí skripty a závislosti do kontejneru v imagích Dockeru. Tyto image jsou registrované ve vašem vlastním registru Dockeru hostovaném v Azure (Azure Container Registry). Dají se spolehlivě nasadit do těchto cílů:

- Místní počítače
- Místní servery
- Cloud
- Hraniční zařízení IoT

Ke cloudovému nasazení s více instancemi se používá systém Kubernetes spuštěný ve službě ACS (Azure Container Service). Ve službě AppInsights se zaznamenávají telemetrická data spouštění modelů pro účely vizuální analýzy. 

Další informace o službě Správa modelů najdete v tématu [Přehled služby Správa modelů](../desktop-workbench/model-management-overview.md).


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Knihovna Microsoft Machine Learning pro Apache Spark

[MMLSpark](https://github.com/Azure/mmlspark)(knihovna Microsoft Machine Learning pro Apache Spark) je balíček Spark s otevřeným zdrojem, který poskytuje nástroje pro hloubkové učení a datové vědy pro Apache Spark. Zajišťuje integraci [kanálů Spark Machine Learning](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) se sadou [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) a knihovnou [OpenCV](http://opencv.org/). Umožňuje rychle vytvářet výkonné a vysoce škálovatelné prediktivní a analytické modely pro velké datové sady obrázků a textu. Mezi nejdůležitější funkce patří:

- Snadný příjem imagí z HDFS do Spark DataFrame
- Předběžné zpracování imagí pomocí transformací z OpenCV
- Zpracování imagí pomocí předem vytrénovaných hloubkových neuronových sítí prostřednictvím sady Microsoft Cognitive Toolkit 
- Používání předem vytrénovaných obousměrných sítí LSTM z rozhraní Keras k extrakci lékařských entit
- Trénování modelů klasifikace imagí založených na hlubokých neuronových sítích (DNN) ve virtuálních počítačích s grafickým procesorem řady N v Azure
- Zpracování volných textových dat pomocí praktických rozhraní API vedle primitiv v ve SparkML pomocí jednoho transformátoru
- Snadné trénování klasifikačních a regresních modelů prostřednictvím implicitního zpracování dat
- Výpočetní využití pestré škály hodnoticích metrik včetně metrik pro jednotlivé instance

Další informace najdete v článku [Používání MMLSpark ve službě Azure Machine Learning](../desktop-workbench/how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools pro AI
Sada Visual Studio Code Tools pro AI představuje rozšíření nástroje Visual Studio Code sloužící k sestavení, testování a nasazení řešení hloubkového učení a AI. Obsahuje řadu bodů integrace se službou Azure Machine Learning, včetně těchto:
- Zobrazení historie spuštění, které informuje o výkonu tréninkových spuštění a metrikách zaznamenaných do protokolu
- Zobrazení galerie, které umožňuje uživatelům procházet a spouštět nové projekty pomocí sady Microsoft Cognitive Toolkit, TensorFlow a řady dalších architektur hloubkového učení. 
- Zobrazení průzkumníka umožňující výběr cílových výpočetních prostředí pro spouštění vašich skriptů
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Jaké možnosti strojového učení nabízí Microsoft?
Vedle služby Azure Machine Learning je v Azure dostupná široká škála možností pro sestavení, nasazení a správu modelů strojového učení. [Další informace o nich najdete tady.](../desktop-workbench/overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Další kroky
* [Instalace a vytvoření služby Azure Machine Learning](quickstart-installation.md)
