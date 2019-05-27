---
title: Rychlý start spuštění poznámkového bloku na svůj vlastní server poznámkového bloku
titleSuffix: Azure Machine Learning service
description: Začínáme se službou Azure Machine Learning. Použijte vlastní server místní poznámkový blok k vyzkoušení vašeho pracovního prostoru.  Váš pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely machine learningu.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 53e495a3c2d82738e1008ead84a4124e44435c9a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864383"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí serveru poznámkového bloku

Začínáme se službou Azure Machine Learning pomocí svoje vlastní prostředí Pythonu a Server poznámkového bloku Jupyter.  Rychlý start s bez instalace sady SDK, naleznete v tématu [rychlý start: Začínáme s Azure Machine Learning pomocí serveru založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md).

Tento rychlý start ukazuje, jak můžete použít [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md) k udržení přehledu o vašich se strojovým učením. Budete spouštět kód Pythonu, který hodnoty do protokolu do pracovního prostoru.

Zobrazte video verzi tohoto rychlého startu:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisites"></a>Požadavky

* Server poznámkového bloku Python 3.6 s sadu SDK Azure Machine Learning nainstalovaná
* Pracovní prostor služby Azure Machine Learning service
* Konfigurační soubor pracovního prostoru (**.azureml/config.json** ).

Získejte všechny tyto požadavky z [vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Použití pracovního prostoru

Vytvořit skript nebo spustit Poznámkový blok ve stejném adresáři jako konfigurační soubor pracovního prostoru. Spusťte tento kód, který používá základní rozhraní API sady SDK pro sledování spuštění experimentu.

1. Vytvoření experimentu v pracovním prostoru.
1. Přihlaste se ke experiment jednu hodnotu.
1. Seznam hodnot přihlaste experimentu.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Zobrazení zaprotokolovaných výsledků

Po dokončení spuštění můžete experimentální spuštění zobrazit na webu Azure Portal. Tisk adresu URL, která přejde na výsledcích posledního spuštění, použijte následující kód:

```python
print(run.get_portal_url())
```

Tento kód vrátí odkaz, který můžete použít k zobrazení zaznamenané hodnoty na webu Azure Portal v prohlížeči.

![Zaznamenané hodnoty na webu Azure Portal](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 

>[!IMPORTANT]
>Můžete použít zdroje, které jste vytvořili jako požadavky na další kurzy Machine Learning a s postupy články.

Pokud nemáte v úmyslu používat prostředky, které jste vytvořili v tomto článku, je vyhnout poplatkům za odstraníte.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili prostředky, které je potřeba experimentovat a nasazovat modely. Spuštění kódu v poznámkovém bloku a prozkoumali historii spuštění pro kód ve vašem pracovním prostoru v cloudu.

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)

Můžete také prozkoumat [pokročilejší příklady na Githubu](https://aka.ms/aml-notebooks) nebo se podívejte [uživatelská příručka k sadě SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
