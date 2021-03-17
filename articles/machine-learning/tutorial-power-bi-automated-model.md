---
title: 'Kurz: Vytvoření prediktivního modelu pomocí automatizovaného ML (část 1 ze 2)'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet a nasazovat automatizované modely strojového učení, abyste mohli použít nejlepší model pro předpověď výsledků v Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814801"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Kurz: Power BI Integration – Vytvoření prediktivního modelu pomocí automatizovaného strojového učení (část 1 ze 2)

V první části tohoto kurzu budete nasazovat a nasazovat prediktivní model strojového učení. Pomocí automatizovaného strojového učení (ML) můžete v Azure Machine Learning Studio.  V části 2 použijete nejlepší model pro předpověď výsledků v Microsoft Power BI.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte Azure Machine Learning výpočetní cluster.
> * Vytvořte datovou sadu.
> * Vytvořte automatizované spuštění strojového učení.
> * Nasazení nejlepšího modelu do vyhodnocovacího koncového bodu v reálném čase.


Existují tři způsoby, jak vytvořit a nasadit model, který budete používat v Power BI.  Tento článek popisuje možnost C: výuka a nasazování modelů pomocí automatizovaného strojového učení v studiu.  Tato možnost je prostředí pro vytváření obsahu bez kódu. Plně automatizuje automatické vzdělávání dat a přípravu modelu. 

Místo toho ale můžete použít jednu z dalších možností:

* [Možnost a: analýza a nasazení modelů pomocí poznámkových bloků Jupyter](tutorial-power-bi-custom-model.md). Toto prostředí pro vytváření obsahu s prvním kódem používá Jupyter poznámkové bloky, které jsou hostované v Azure Machine Learning Studio.
* [Možnost B: analýza a nasazení modelů pomocí návrháře Azure Machine Learning](tutorial-power-bi-designer-model.md). Toto prostředí pro vytváření s nízkým kódem používá uživatelské rozhraní přetahování myší.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné, můžete použít [bezplatnou zkušební verzi](https://aka.ms/AMLFree). 
- Pracovní prostor služby Azure Machine Learning. Pokud ještě nemáte pracovní prostor, přečtěte si téma [Vytvoření a správa Azure Machine Learningch pracovních prostorů](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Vytvoření výpočetního clusteru

Automatizované strojové učení vlaků mnoho modelů strojového učení, aby bylo možné najít nejlepší algoritmus a parametry. Azure Machine Learning parallelizes spuštění školení modelu přes výpočetní cluster.

Začněte tím, že [Azure Machine Learning Studio](https://ml.azure.com)v nabídce na levé straně vyberete **COMPUTE**. Otevřete kartu **výpočetní clustery** . Pak vyberte **Nový**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Snímek obrazovky ukazující, jak vytvořit výpočetní cluster":::

Na stránce **vytvořit výpočetní cluster** :

1. Vyberte velikost virtuálního počítače. Pro tento kurz je **Standard_D11_v2** počítač v pořádku.
1. Vyberte **Další**.
1. Zadejte platný výpočetní název.
1. Zachovat **minimální počet uzlů** v `0` .
1. Změnit **maximální počet uzlů** na `4` .
1. Vyberte **Vytvořit**.

Stav clusteru se změní na **Vytvoření**.

>[!NOTE]
> Nový cluster má 0 uzlů, takže se neúčtují žádné výpočetní náklady. Náklady se účtují jenom v případě, že se spouští automatizovaná úloha strojového učení. Po 120 sekundách času nečinnosti se cluster automaticky škáluje na 0.


## <a name="create-a-dataset"></a>Vytvoření datové sady

V tomto kurzu použijete [datovou sadu diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Tato datová sada je dostupná v [Azure Open](https://azure.microsoft.com/services/open-datasets/)DataSet.

Chcete-li vytvořit datovou sadu, vyberte v nabídce vlevo možnost datové **sady**. Pak vyberte **vytvořit datovou sadu**. Zobrazí se následující možnosti:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Snímek obrazovky ukazující, jak vytvořit novou datovou sadu.":::

Vyberte **z otevřených datových sad**. Pak na stránce **vytvořit datovou sadu z otevřených datových sad** :

1. Pomocí panelu hledání vyhledejte *diabetes*.
1. Vyberte **Ukázka: diabetes**.
1. Vyberte **Další**.
1. Pojmenujte datovou sadu *diabetes*.
1. Vyberte **Vytvořit**.

Pokud chcete prozkoumat data, vyberte datovou sadu a pak vyberte **prozkoumat**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Snímek obrazovky znázorňující, jak prozkoumat datovou sadu":::

Data mají 10 vstupních proměnných pro standardní hodnoty, jako je věk, pohlaví, hmotnostní index karoserie, průměrný tlak v krvi a šest měření krevního séra. Má také jednu cílovou proměnnou s názvem **Y**. Tato proměnná cíle je kvantitativní míra diabetes průběhu v jednom roce po standardních hodnotách.

## <a name="create-an-automated-machine-learning-run"></a>Vytvoření automatizovaného spuštění strojového učení

V [Azure Machine Learning Studio](https://ml.azure.com)v nabídce na levé straně vyberte **automatizovaná ml**. Pak vyberte **nové automatizované spuštění ml**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Snímek obrazovky znázorňující vytvoření nového automatizovaného spuštění strojového učení":::

Potom vyberte datovou sadu **diabetes** , kterou jste vytvořili dříve. Pak vyberte **Další**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Snímek obrazovky ukazující, jak vybrat datovou sadu":::
 
Na stránce **Konfigurace spuštění** :

1. V části **název experimentu** vyberte **vytvořit novou**.
1. Pojmenujte experiment.
1. V poli **cílový sloupec** vyberte **Y**.
1. V poli **Vybrat výpočetní cluster** vyberte výpočetní cluster, který jste vytvořili dříve. 

Váš dokončený formulář by měl vypadat takto:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Snímek obrazovky, který ukazuje, jak nakonfigurovat automatizované strojové učení.":::

Nakonec vyberte úlohu strojového učení. V tomto případě je úkolem **regrese**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat úlohu":::

Vyberte **Dokončit**.

> [!IMPORTANT]
> Školení modelů 100 vám umožní automatizované strojové učení trvat přibližně 30 minut.

## <a name="deploy-the-best-model"></a>Nasazení nejlepšího modelu

Po dokončení automatizovaného strojového učení uvidíte všechny modely strojového učení, které byly vyzkoušeny, a to tak, že vyberete kartu **modely** . Modely jsou seřazené podle výkonu; jako první se zobrazí model nejlepšího provádění. Po výběru nejlepšího modelu je tlačítko **nasadit** povoleno:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Snímek obrazovky zobrazující seznam modelů":::

Vyberte **nasadit** a otevřete tak okno **nasadit model** :

1. Pojmenujte službu modelu *diabetes-model*.
1. Vyberte **Azure Container Service**.
1. Vyberte **Nasadit**.

Měla by se zobrazit zpráva oznamující, že model byl úspěšně nasazen.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí automatizovaného strojového učení prosazovat a nasazovat model strojového učení. V dalším kurzu se dozvíte, jak tento model ve Power BI spotřebovat (skore).

> [!div class="nextstepaction"]
> [Kurz: využívání modelu v Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
