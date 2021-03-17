---
title: 'Kurz: přetažením můžete vytvořit prediktivní model (část 1 ze 2).'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet a nasazovat prediktivní model Machine Learning pomocí návrháře. Později je můžete použít k předpovědi výsledků v Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814784"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Kurz: Power BI Integration – přetažení pro vytvoření prediktivního modelu (část 1 ze 2)

V části 1 tohoto kurzu můžete pomocí návrháře Azure Machine Learning nasazovat a nasazovat prediktivní model strojového učení. Návrhář je uživatelské rozhraní pro přetahování myší v nedostatku kódu. V části 2 použijete model k předpovídání výsledků v Microsoft Power BI.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte instanci služby Azure Machine Learning Compute.
> * Vytvořte odvozený cluster Azure Machine Learning.
> * Vytvořte datovou sadu.
> * Výuka regresního modelu.
> * Nasaďte model do bodování koncového bodu v reálném čase.


Existují tři způsoby, jak vytvořit a nasadit model, který budete používat v Power BI.  Tento článek se zabývá možností B: výuka a nasazování modelů pomocí návrháře.  Tato možnost je prostředí pro vytváření nízkých kódů, které používá rozhraní návrháře.  

Místo toho ale můžete použít jednu z dalších možností:

* [Možnost a: analýza a nasazení modelů pomocí poznámkových bloků Jupyter](tutorial-power-bi-custom-model.md). Toto prostředí pro vytváření obsahu s prvním kódem používá Jupyter poznámkové bloky, které jsou hostované v Azure Machine Learning Studio.
* [Možnost C: analýza a nasazení modelů pomocí automatizovaného strojového učení](tutorial-power-bi-automated-model.md). Prostředí pro vytváření obsahu bez kódu plně automatizuje automatické školení pro přípravu dat a modelování.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné, můžete použít [bezplatnou zkušební verzi](https://aka.ms/AMLFree). 
- Pracovní prostor služby Azure Machine Learning. Pokud ještě nemáte pracovní prostor, přečtěte si téma [Vytvoření a správa Azure Machine Learningch pracovních prostorů](./how-to-manage-workspace.md#create-a-workspace).
- Úvodní znalosti pracovních postupů strojového učení


## <a name="create-compute-to-train-and-score"></a>Vytváření výpočetních prostředků ke školení a hodnocení

V této části vytvoříte *výpočetní instanci*. Výpočetní instance se používají k výuce modelů strojového učení. Také vytvoříte *cluster odvození* pro hostování nasazeného modelu pro bodování v reálném čase.

Přihlaste se k [Azure Machine Learning Studio](https://ml.azure.com). V nabídce na levé straně vyberte **COMPUTE** a pak **Nový**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Snímek obrazovky ukazující, jak vytvořit výpočetní instanci.":::

Na stránce **vytvořit výpočetní instanci** vyberte velikost virtuálního počítače. Pro tento kurz vyberte **Standard_D11_v2** virtuální počítač. Pak vyberte **Další**. 

Na stránce **Nastavení** pojmenujte instanci Compute. Potom vyberte **Vytvořit**. 

>[!TIP]
> Instanci COMPUTE můžete použít také k vytvoření a spuštění poznámkových bloků.

**Stav** vaší instance COMPUTE se teď **vytváří**. Zřízení počítače trvá přibližně 4 minuty. 

Počkejte, než budete na stránce **COMPUTE** vybírat kartu **odvozené clustery** . Pak vyberte **Nový**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Snímek obrazovky ukazující, jak vytvořit cluster pro odvození":::

Na stránce **vytvořit odvozený cluster** vyberte oblast a velikost virtuálního počítače. Pro tento kurz vyberte **Standard_D11_v2** virtuální počítač. Pak vyberte **Další**. 

Na stránce **Konfigurovat nastavení** postupujte takto:

1. Zadejte platný výpočetní název.
1. Jako účel clusteru vyberte **vývoj a testování** . Tato možnost vytvoří jeden uzel pro hostování nasazeného modelu.
1. Vyberte **Vytvořit**.

**Stav** vašeho odvozeného clusteru se teď **vytváří**. Nasazení clusteru s jedním uzlem trvá přibližně 4 minuty.

## <a name="create-a-dataset"></a>Vytvoření datové sady

V tomto kurzu použijete [datovou sadu diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Tato datová sada je dostupná v [Azure Open](https://azure.microsoft.com/services/open-datasets/)DataSet.

Chcete-li vytvořit datovou sadu, vyberte v nabídce vlevo možnost datové **sady**. Pak vyberte **vytvořit datovou sadu**. Zobrazí se následující možnosti:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Snímek obrazovky ukazující, jak vytvořit novou datovou sadu.":::

Vyberte **z otevřených datových sad**. Na stránce **vytvořit datovou sadu z otevřených datových sad** :

1. Pomocí panelu hledání vyhledejte *diabetes*.
1. Vyberte **Ukázka: diabetes**.
1. Vyberte **Další**.
1. Pojmenujte datovou sadu *diabetes*.
1. Vyberte **Vytvořit**.

Pokud chcete prozkoumat data, vyberte datovou sadu a pak vyberte **prozkoumat**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Snímek obrazovky znázorňující, jak prozkoumat datovou sadu":::

Data mají 10 vstupních proměnných pro standardní hodnoty, jako je věk, pohlaví, hmotnostní index karoserie, průměrný tlak v krvi a šest měření krevního séra. Má také jednu cílovou proměnnou s názvem **Y**. Tato proměnná cíle je kvantitativní míra diabetes průběhu v jednom roce po standardních hodnotách.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Vytvoření modelu Machine Learning pomocí návrháře

Po vytvoření výpočetních a datových sad můžete pomocí návrháře vytvořit model strojového učení. V Azure Machine Learning Studio vyberte **Návrhář** a pak **Nový kanál**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Snímek obrazovky ukazující, jak vytvořit nový kanál.":::

Zobrazí se prázdné *plátno* a nabídka **Nastavení** :

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Snímek obrazovky ukazující, jak vybrat cíl výpočtů":::

V nabídce **Nastavení** zvolte **vybrat cíl výpočtu**. Vyberte výpočetní instanci, kterou jste vytvořili dříve, a pak vyberte **Uložit**. Změňte **název konceptu** na něco srozumitelnější, například *diabetes-model*. Nakonec zadejte popis.

V seznamu assetů rozbalte položku datové **sady** a vyhledejte datovou sadu **diabetes** . Přetáhněte tuto komponentu na plátno:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Snímek obrazovky znázorňující, jak přetáhnout komponentu na plátno":::

V dalším kroku přetáhněte na plátno následující komponenty:

1. **Lineární regrese** (umístěný v **algoritmech Machine Learning**)
1. **Model výuky** (umístěný v **školicím modelu**)

Na plátně si všimněte kružnice v horní a dolní části komponent. Tyto kroužky jsou porty.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Snímek obrazovky znázorňující porty nepřipojených komponent":::
 
Nyní *tyto* komponenty spojíte dohromady. Vyberte port v dolní části datové sady **diabetes** . Přetáhněte ji na port v pravé horní části komponenty **modelu výuky** . V dolní části komponenty **lineární regrese** vyberte port. Přetáhněte ji na port v levé horní části komponenty **modelu výuky** .

Vyberte sloupec DataSet, který má být použit jako proměnná popisku (cíl) pro předpověď. Vyberte komponentu **výukového modelu** a pak vyberte **Upravit sloupec**. 

V dialogovém okně vyberte **zadat název sloupce**  >  **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Snímek obrazovky ukazující, jak vybrat sloupec popisku":::

Vyberte **Uložit**. *Pracovní postup* Machine Learning by měl vypadat takto:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Snímek obrazovky zobrazující připojené součásti":::

Vyberte **Odeslat**. V části **experiment** vyberte **vytvořit novou**. Pojmenujte experiment a pak vyberte **Odeslat**.

>[!NOTE]
> První spuštění experimentu by mělo trvat přibližně 5 minut. Další spuštění jsou mnohem rychlejší, protože Návrhář ukládá do mezipaměti komponenty, které byly spuštěny, aby se snížila latence.

Po dokončení experimentu se zobrazí toto zobrazení:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Snímek obrazovky znázorňující dokončené spuštění":::

Chcete-li zkontrolovat protokoly experimentů, vyberte možnost **model výuky** a pak vyberte možnost **výstupy + protokoly**.

## <a name="deploy-the-model"></a>Nasazení modelu

Pokud chcete model nasadit, v horní části plátna vyberte **vytvořit odvození kanálu**  >  **odvození v reálném čase**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Snímek obrazovky s informacemi o tom, kde vybrat kanál pro odvození v reálném čase":::
 
Kanál se rozhustí jenom na součásti, které jsou potřebné k určení skóre modelu. Při hodnocení dat neznáte hodnoty cílové proměnné. Takže můžete z datové sady odebrat **Y** . 

Chcete-li odebrat **Y**, přidejte do plátna komponentu **Vybrat sloupce v datové sadě** . Nakabelujte komponentu tak, aby diabetes datová sada byla vstupem. Výsledky představují výstup do komponenty **modelu skóre** :

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Snímek obrazovky ukazující, jak odebrat sloupec":::

Na plátně vyberte součást **Vybrat sloupce v datové sadě** a pak vyberte **Upravit sloupce**. 

V dialogovém okně **Vybrat sloupce** vyberte **podle názvu**. Pak se ujistěte, že jsou vybrané všechny vstupní proměnné, ale cíl *není* vybraný:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Snímek obrazovky ukazující, jak odebrat nastavení sloupců":::

Vyberte **Uložit**. 

Nakonec vyberte komponentu **modelu skóre** a ujistěte se, že je zaškrtnuté políčko **připojit sloupce skóre k výstupu** . Aby se snížila latence, předpovědi se pošle zpátky bez vstupů.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Snímek obrazovky s nastavením pro komponentu modelu skóre":::

V horní části plátna vyberte **Odeslat**.

Po úspěšném spuštění kanálu odvození můžete model nasadit do svého clusteru odvození. Vyberte **Nasadit**. 

V dialogovém okně **nastavit koncový bod v reálném** čase vyberte **nasadit nový koncový bod v reálném** čase. Pojmenujte koncový bod *My-diabetes-model*. Vyberte odvození, které jste vytvořili dříve, a pak vyberte **nasadit**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Snímek obrazovky zobrazující nastavení koncového bodu v reálném čase":::
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste viděli, jak se naučit a nasazovat model návrháře. V další části se dozvíte, jak tento model ve Power BI spotřebovat (skore).

> [!div class="nextstepaction"]
> [Kurz: využívání modelu v Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
