---
title: 'Kurz: Vytvoření prediktivního modelu pomocí automatizovaného ML (část 1 ze 2)'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet a nasazovat automatizované modely ML, abyste mohli použít nejlepší model pro předpověď výsledků v Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370196"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Kurz: Power BI Integration – Vytvoření prediktivního modelu pomocí automatizovaného strojového učení (část 1 ze 2)

V první části tohoto kurzu provedete a nasadíte prediktivní model strojového učení s využitím automatizovaného strojového učení v Azure Machine Learning Studiu.  V části 2 pak pomocí modelu nejlepšího provádění předpovídáte výsledky v Microsoft Power BI.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření clusteru Azure Machine Learning COMPUTE
> * Vytvoření datové sady
> * Vytvoření automatizovaného běhu na ML
> * Nasazení nejlepšího modelu do vyhodnocovacího koncového bodu v reálném čase


Existují tři různé způsoby, jak vytvořit a nasadit model, který budete používat v Power BI.  Tento článek popisuje možnost C: výuka a nasazování modelů pomocí automatizovaného ML v studiu.  Tato možnost zobrazuje prostředí pro vytváření obsahu bez kódu, které plně automatizuje přípravu dat a školení modelů. 

Místo toho můžete použít:

* [Možnost a: výuka a nasazování modelů pomocí poznámkových bloků](tutorial-power-bi-custom-model.md) – prostředí pro vytváření obsahu s využitím poznámkových bloků Jupyter hostovaných v Azure Machine Learning Studiu.
* [Možnost B: analýza a nasazení modelů pomocí návrháře](tutorial-power-bi-designer-model.md)– prostředí pro vytváření nízkých kódů pomocí návrháře (uživatelské rozhraní přetahování myší).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure ([k dispozici je bezplatná zkušební verze](https://aka.ms/AMLFree)). 
- Pracovní prostor služby Azure Machine Learning. Pokud ještě nemáte pracovní prostor, postupujte podle pokynů v tématu [jak vytvořit pracovní prostor Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Vytvořit výpočetní cluster

Automatizované ML automaticky převlakuje spoustu různých modelů strojového učení a hledá nejlepší algoritmus a parametry. Azure Machine Learning parallelizes provádění školicího modelu v rámci výpočetního clusteru.

V [Azure Machine Learning Studio](https://ml.azure.com)vyberte **COMPUTE** v nabídce na levé straně a potom na kartě **výpočetní clustery** . Vyberte **Nový**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Snímek obrazovky ukazující, jak vytvořit výpočetní cluster":::

Na obrazovce **vytvořit Compute Cluster** :

1. Vyberte velikost virtuálního počítače (pro účely tohoto kurzu `Standard_D11_v2` je počítač v pořádku).
1. Vyberte **Další**.
1. Zadejte platný výpočetní název.
1. Zachovat **minimální počet uzlů** na 0
1. Změna **maximálního počtu uzlů** na 4
1. Vyberte **Vytvořit**.

Můžete vidět, že stav clusteru se změnil na **Vytvoření**.

>[!NOTE]
> Když se cluster vytvoří, bude mít 0 uzlů, což znamená, že se neúčtují žádné výpočetní náklady. Náklady se účtují jenom při spuštění úlohy automatizovaného ML. Cluster se po 120 sekundách času nečinnosti automaticky škáluje na 0.


## <a name="create-dataset"></a>Vytvoření datové sady

V tomto kurzu použijete [datovou sadu diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), která je dostupná v [otevřených datových sadách Azure](https://azure.microsoft.com/services/open-datasets/).

Pokud chcete vytvořit datovou sadu, vyberte v nabídce na levé straně datové **sady** a pak **Vytvořte datovou sadu** – zobrazí se následující možnosti:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Snímek obrazovky ukazující, jak vytvořit novou datovou sadu":::

Vyberte **z otevřených datových sad** a pak na obrazovce **vytvořit datovou sadu z otevřených datových sad** :

1. Hledání *diabetes* pomocí panelu hledání
1. Vyberte **ukázku: diabetes**
1. Vyberte **Další**.
1. Zadejte název datové sady – *diabetes*
1. Vyberte **Vytvořit**.

Data můžete prozkoumat tak, že vyberete datovou sadu a **prozkoumáte**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Snímek obrazovky znázorňující, jak prozkoumat datovou sadu":::

Data mají 10 vstupních proměnných standardních hodnot (například věk, pohlaví, hmotnostní index karoserie, průměrný tlak na krevní tlak a šest měření krevního séra) a jednu cílovou proměnnou s názvem **Y** (kvantitativní míra diabetes průběhu v jednom roce po základu).

## <a name="create-automated-ml-run"></a>Vytvořit automatizované spuštění ML

V nabídce na levé straně [Azure Machine Learning Studio](https://ml.azure.com) vyberte v nabídce vlevo možnost **automatizovaná ml** , za kterou následuje **nový automatizovaný běh ml**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Snímek obrazovky znázorňující vytvoření nového automatizovaného běhu na ML":::

Potom vyberte datovou sadu **diabetes** , kterou jste vytvořili dříve, a vyberte **Další**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Snímek obrazovky ukazující, jak vybrat datovou sadu":::
 
Na obrazovce **Konfigurace spuštění** :

1. V části **název experimentu** vyberte **vytvořit novou** .
1. Zadejte název experimentu.
1. V poli cílový sloupec vyberte **Y** .
1. V poli **Vybrat výpočetní cluster** vyberte výpočetní cluster, který jste vytvořili dříve. 

Váš dokončený formulář by měl vypadat nějak takto:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Snímek obrazovky ukazující postup konfigurace automatizovaného ML":::

Nakonec musíte vybrat úlohu Machine Learning, která se má provést, což je **regrese**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat úlohu":::

Vyberte **Dokončit**.

> [!IMPORTANT]
> Automatizovanému ML bude trvat přibližně 30 minut, než se dokončí školením různých modelů 100.

## <a name="deploy-the-best-model"></a>Nasazení nejlepšího modelu

Po dokončení automatizovaného použití ML můžete zobrazit seznam všech různých modelů strojového učení, které byly vyzkoušeny, a to tak, že vyberete kartu **modely** . Modely jsou seřazené v pořadí podle výkonu – model nejlepšího provádění se zobrazí jako první. Když vyberete nejlepší model, povolí se tlačítko **nasadit** :

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Snímek obrazovky zobrazující seznam modelů":::

Po výběru možnosti **nasadit** se zobrazí obrazovka **nasadit model** :

1. Zadejte název služby modelu – použijte **diabetes-model** .
1. Vyberte **Azure Container Service**
1. Vybrat **nasazení**

Měla by se zobrazit zpráva oznamující, že model byl úspěšně nasazen.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí automatizovaného ML naučit a nasazovat model strojového učení. V dalším kurzu se zobrazuje, jak tento model z Power BI spotřebovat (skóre).

> [!div class="nextstepaction"]
> [Kurz: využití modelu v Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
