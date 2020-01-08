---
title: Co jsou to prostředí ML
titleSuffix: Azure Machine Learning
description: V tomto článku se seznámíte s výhodami strojového učení, které umožňují reprodukovatelná, Auditovaná a přenositelné definice závislostí strojového učení napříč různými výpočetními cíli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692729"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co jsou Azure Machine Learning prostředí?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Prostředí určují balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů a časy spuštění (Python, Spark nebo Docker). Jsou spravované a se správou verzí v rámci vašeho pracovního prostoru Azure Machine Learning umožňují reprodukovatelné pracovní postupy, které lze auditovat a přenosné strojové učení napříč různými výpočetními cíli.

K vývoji školicího skriptu můžete použít objekt prostředí v místním výpočetním prostředí, použít stejné prostředí v Azure Machine Learning výpočetním prostředí pro modelově škálovatelné školení a dokonce model nasadit do stejného prostředí.

Níže vidíte, že stejný objekt prostředí lze použít jak v konfiguraci spuštění pro školení, tak v konfiguraci nasazení pro nasazení webových služeb.

![Diagram prostředí v pracovním postupu Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typy prostředí

Prostředí se můžou v podstatě rozdělit do tří **kategorií: řízená**, **uživatelsky spravovaná** a **spravovaná systémem**.

Dodaná prostředí jsou poskytována Azure Machine Learning a jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Obsahují kolekce balíčků a nastavení Pythonu, které vám pomůžou začít s různými architekturami strojového učení. 

V případě prostředí spravovaného uživatelem zodpovídáte za nastavení vašeho prostředí a instalaci každého balíčku, který je potřebný pro školicí skript na výpočetním cíli. Conda nebude kontrolovat vaše prostředí ani instalovat cokoli za vás. Počítejte s tím, že pokud definujete vlastní prostředí, je nutné uvést `azureml-defaults` s verzí `>= 1.0.45` jako závislostí PIP. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby.

Prostředí spravovaná systémem se používají, když chcete, aby [conda](https://conda.io/docs/) spravovat prostředí Pythonu a závislosti skriptů za vás. Služba ve výchozím nastavení předpokládá tento typ prostředí kvůli jeho užitečnosti pro vzdálené výpočetní cíle, které se nedají ručně konfigurovat.

## <a name="creating-and-managing-environments"></a>Vytváření a Správa prostředí

Prostředí lze vytvořit pomocí:

* Definování nových `Environment` objektů, a to buď pomocí spravovaného prostředí, nebo definováním vlastních závislostí
* Používání stávajících `Environment` objektů z pracovního prostoru. To umožňuje konzistenci a reprodukovatelnosti s vašimi závislostmi.
* Probíhá import z existující definice prostředí Anaconda.

Příklady specifických kódů [naleznete v tématu](how-to-use-environments.md#create-an-environment) . Prostředí je také možné snadno spravovat přes pracovní prostor a zahrnovat následující funkce:

* Prostředí se při odeslání experimentu automaticky zaregistrují do vašeho pracovního prostoru. Můžou se taky zaregistrovat ručně.
* Načtěte prostředí z pracovního prostoru a používejte je pro školení, nasazení nebo úpravy v definici prostředí.
* Správa verzí umožňuje zobrazit změny v prostředí v průběhu času a zajistit reprodukovatelnost.
* Automatické sestavení imagí Docker z prostředí

Viz část [Správa prostředí](how-to-use-environments.md#manage-environments) v tématu Postupy pro ukázky kódu.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet a používat prostředí](how-to-use-environments.md) v Azure Machine Learning
* Podívejte se na referenční dokumentace sady Python SDK pro [třídu prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Podívejte se na referenční dokumentace k sadě R SDK pro [prostředí](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).