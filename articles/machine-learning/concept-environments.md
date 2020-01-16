---
title: Informace o prostředích Azure Machine Learning
titleSuffix: Azure Machine Learning
description: V tomto článku se seznámíte s výhodami strojového učení, které umožňují reprodukovatelná, Auditovaná a přenosné definice závislostí strojového učení napříč různými výpočetními cíli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045857"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co jsou Azure Machine Learning prostředí?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning prostředí určují balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů. Určují také časy spuštění (Python, Spark nebo Docker). Jsou spravované a se správou verzí v rámci vašeho pracovního prostoru Machine Learning umožňují reprodukovatelné pracovní postupy, které lze auditovat a přenosné strojové učení napříč různými výpočetními cíli.

Objekt `Environment` v místním výpočetním prostředí můžete použít k těmto akcím:
* Vytvořte školicí skript.
* Používejte stejné prostředí v Azure Machine Learning COMPUTE pro modelové školení ve velkém měřítku.
* Nasaďte model se stejným prostředím.

Následující diagram znázorňuje, jak můžete použít jeden objekt `Environment` v konfiguraci spuštění, pro školení a konfiguraci nasazení pro nasazení webových služeb.

![Diagram prostředí v pracovním postupu Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typy prostředí

Prostředí se můžou v podstatě rozdělit do tří *kategorií: řízená*, *uživatelsky spravovaná*a *spravovaná systémem*.

Dodaná prostředí jsou poskytována Azure Machine Learning a jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Obsahují kolekce balíčků a nastavení Pythonu, které vám pomůžou začít s různými architekturami strojového učení. 

V prostředích spravovaných uživatelem zodpovídáte za nastavení vašeho prostředí a instalaci každého balíčku, který váš školicí skript potřebuje na výpočetním cíli. Conda nekontroluje vaše prostředí ani neinstaluje cokoli za vás. Pokud definujete vlastní prostředí, musíte jako závislost PIP uvést `azureml-defaults` s verzí `>= 1.0.45`. Tento balíček obsahuje funkce, které jsou potřeba pro hostování modelu jako webové služby.

Prostředí spravovaná systémem se používají, když chcete, aby [conda](https://conda.io/docs/) spravovalo prostředí Pythonu a závislosti skriptů za vás. Služba ve výchozím nastavení předpokládá tento typ prostředí, protože se jedná o užitečnost vzdálených výpočetních cílů, které se nedají ručně konfigurovat.

## <a name="create-and-manage-environments"></a>Vytváření a Správa prostředí

Prostředí můžete vytvořit pomocí:

* Definování nových `Environment` objektů, a to buď pomocí spravovaného prostředí, nebo definováním vlastních závislostí.
* Používání stávajících `Environment` objektů z pracovního prostoru. Tento přístup umožňuje konzistenci a reprodukovatelnosti s vašimi závislostmi.
* Probíhá import z existující definice prostředí Anaconda.
* Použití rozhraní příkazového řádku Azure Machine Learning

Konkrétní ukázky kódu najdete v části Vytvoření prostředí v tématu [opakované použití prostředí pro školení a nasazení](how-to-use-environments.md#create-an-environment). Prostředí je taky možné snadno spravovat přes pracovní prostor. Zahrnují tyto funkce:

* Prostředí se při odeslání experimentu automaticky zaregistrují do vašeho pracovního prostoru. Můžou se taky registrovat ručně.
* Můžete načíst prostředí z pracovního prostoru, která se mají použít pro školení nebo nasazení, nebo upravit definici prostředí.
* Se správou verzí můžete zobrazit změny prostředí v průběhu času, což zajišťuje reprodukovatelnost.
* Image Docker můžete vytvářet automaticky z prostředí.

Ukázky kódu najdete v části "Správa prostředí" v tématu [opakované použití prostředí pro účely školení a nasazení](how-to-use-environments.md#manage-environments).

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet a používat prostředí](how-to-use-environments.md) v Azure Machine Learning.
* Viz Referenční dokumentace sady Python SDK pro [třídu prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Podívejte se na referenční dokumentaci sady R SDK pro [prostředí](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
