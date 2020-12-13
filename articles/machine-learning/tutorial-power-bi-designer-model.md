---
title: 'Kurz: přetažení pro vytvoření prediktivního modelu (část 1 ze 2)'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet a nasazovat prediktivní model Machine Learning pomocí návrháře, takže ho můžete použít k předpovědi výsledků v Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370170"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Kurz: Power BI integraci – přetažení a Vytvoření prediktivního modelu (část 1 ze 2)

V první části tohoto kurzu provedete a nasadíte prediktivní model strojového učení s využitím návrháře Azure Machine Learning – uživatelské rozhraní pro přetahování myší s nízkým kódem. V části 2 pak pomocí modelu předpovídáte výsledky v Microsoft Power BI.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření instance služby Azure Machine Learning COMPUTE
> * Vytvořit odvozený cluster Azure Machine Learning
> * Vytvoření datové sady
> * Trénování regresního modelu
> * Nasazení modelu do vyhodnocovacího koncového bodu v reálném čase


Existují tři různé způsoby, jak vytvořit a nasadit model, který budete používat v Power BI.  Tento článek se zabývá možností B: analýza a nasazení modelů pomocí návrháře.  Tato možnost zobrazuje prostředí pro vytváření nízkých kódů pomocí návrháře (uživatelské rozhraní přetahování myší).  

Místo toho můžete použít:

* [Možnost a: výuka a nasazování modelů pomocí poznámkových bloků](tutorial-power-bi-custom-model.md) – prostředí pro vytváření obsahu s využitím poznámkových bloků Jupyter hostovaných v Azure Machine Learning Studiu.
* [Možnost C: analýza a nasazení modelů pomocí automatizovaného](tutorial-power-bi-automated-model.md) prostředí pro vytváření obsahu, které plně automatizuje přípravu dat a školení k modelu.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure ([k dispozici je bezplatná zkušební verze](https://aka.ms/AMLFree)). 
- Pracovní prostor služby Azure Machine Learning. Pokud ještě nemáte pracovní prostor, postupujte podle pokynů v tématu [jak vytvořit pracovní prostor Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Úvodní znalosti pracovních postupů strojového učení


## <a name="create-compute-for-training-and-scoring"></a>Vytváření výpočetních prostředků pro školení a bodování

V této části vytvoříte *výpočetní instanci*, která se používá pro školení modelů strojového učení. Také můžete vytvořit *odvozený cluster* , který bude použit k hostování nasazeného modelu pro bodování v reálném čase.

Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com) a v nabídce na levé straně vyberte **COMPUTE** a pak klikněte na **Nová**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Snímek obrazovky ukazující, jak vytvořit výpočetní instanci":::

Na obrazovce výsledná **instance vytvoření výpočetní instance** vyberte velikost virtuálního počítače (pro tento kurz vyberte `Standard_D11_v2` ) a potom klikněte na **Další**. Na stránce nastavení zadejte platný název pro instanci COMPUTE a potom vyberte **vytvořit**. 

>[!TIP]
> Instanci COMPUTE lze použít také k vytvoření a spuštění poznámkových bloků.

Teď můžete vidět, že **stav** instance služby COMPUTE se **vytváří** – pro zřízení počítače bude trvat přibližně 4 minuty. Až budete čekat, vyberte kartu **odvozený cluster** na stránce COMPUTE a pak klikněte na **Nová**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Snímek obrazovky ukazující, jak vytvořit cluster pro odvození":::

Na stránce výsledný **cluster pro vytvoření odvození** vyberte oblast následovanou velikostí virtuálního počítače (pro tento kurz vyberte `Standard_D11_v2` ) a pak vyberte **Další**. Na stránce **Konfigurovat nastavení** postupujte takto:

1. Zadejte platný výpočetní název.
1. Jako účel clusteru vyberte **vývoj a testování** (vytvoří jeden uzel pro hostování nasazeného modelu).
1. Vyberte **Vytvořit**.

Teď můžete vidět, že váš odvozený cluster má **stav** probíhá **vytváření** . pro nasazení clusteru s jedním uzlem bude trvat přibližně 4 minuty.

## <a name="create-a-dataset"></a>Vytvoření datové sady

V tomto kurzu použijete [datovou sadu diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), která je dostupná v [otevřených datových sadách Azure](https://azure.microsoft.com/services/open-datasets/).

Pokud chcete vytvořit datovou sadu, v nabídce vlevo vyberte datové **sady** a pak **Vytvořte datovou sadu** – zobrazí se následující možnosti:

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

## <a name="create-a-machine-learning-model-using-designer"></a>Vytvoření modelu Machine Learning pomocí návrháře

Po vytvoření výpočetních a datových sad můžete přejít k vytvoření modelu Machine Learning pomocí návrháře. V Azure Machine Learning Studiu vyberte **Návrhář** následovaný **novým kanálem**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Snímek obrazovky znázorňující vytvoření nového kanálu":::

Zobrazí se prázdné *plátno* , kde můžete zobrazit také **nabídku nastavení**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Snímek obrazovky ukazující, jak vybrat cíl výpočtů":::

V **nabídce nastavení** **Vyberte COMPUTE Target** a pak vyberte výpočetní instanci, kterou jste vytvořili dříve a pak klikněte na **Uložit**. Přejmenujte svůj **název konceptu** na něco srozumitelného (například *diabetes-model*) a zadejte popis.

V dalším kroku rozbalíme datové **sady** a vyhledáte datovou sadu **diabetes** – přetáhněte tento modul na plátno:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Snímek obrazovky ukazující, jak komponentu přetáhnout":::

V dalším kroku přetáhněte na plátno následující komponenty:

1. Lineární regrese (umístěný v **algoritmech Machine Learning**)
1. Model výuky (umístěný v **školicím modelu**)

Vaše plátno by mělo vypadat jako (Všimněte si, že horní a dolní část komponent má malé kroužky s názvem porty zvýrazněné červeně):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Snímek obrazovky znázorňující, jak nepřipojené komponenty":::
 
Dále *musíte tyto komponenty* propojit dohromady. Vyberte port v dolní části datové sady **diabetes** a přetáhněte ho do pravého portu v horní části komponenty **modelu vlakové soupravy** . Vyberte port ve spodní části **lineární regrese** a přetáhněte ho na levý port v horní části portu **modelu vlaku** .

Vyberte sloupec v datové sadě, který má být použit jako proměnná popisku (cíl) pro předpověď. Vyberte komponentu pro **výukový model** následovaný **sloupcem upravit**. Z dialogového okna vyberte v rozevíracím seznamu **Zadejte název sloupce** následovaný písmenem **Y** :

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Snímek – výběr sloupce popisku":::

Vyberte **Uložit**. *Pracovní postup* Machine Learning by měl vypadat takto:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Snímek obrazovky ukazující, jak připojené součásti":::

Vyberte **Odeslat** a pak **vytvořte novou** v části experiment. Zadejte název experimentu následovaný **odesláním**.

>[!NOTE]
> Dokončení prvního spuštění experimentu by mělo trvat přibližně 5 minut. Další spuštění jsou mnohem rychlejší v mezipaměti návrháře, které už spouští komponenty, aby se snížila latence.

Po dokončení experimentu se zobrazí tyto informace:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Snímek obrazovky znázorňující dokončené spuštění":::

Protokoly experimentu můžete zkontrolovat tak, že vyberete **model výuky** následovaný **výstupy + protokoly**.

## <a name="deploy-the-model"></a>Nasazení modelu

Chcete-li model nasadit, vyberte možnost **vytvořit odvozený kanál** (umístěný v horní části plátna) následovaný **kanálem pro odvození v reálném čase**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Snímek obrazovky, který ukazuje kanál odvození v reálném čase":::
 
Kanál se rozhustí jenom na součásti, které jsou nezbytné pro vyhodnocování modelu. Když vydáte skóre dat, neznáte hodnoty cílových proměnných, a proto můžeme z datové sady odebrat **Y** . Pokud ho chcete odebrat, přidejte ho na plátno a **Vyberte sloupce v komponentě datové sady** . Nakabelujte komponentu tak, aby diabetes datová sada a výsledky jsou výstupem do komponenty **bodového modelu** .

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Snímek obrazovky znázorňující odebrání sloupce":::

Na plátně vyberte součást **Vybrat sloupce v datové sadě** a pak **upravte sloupce**. V dialogovém okně Vybrat sloupce vyberte **podle názvu** a pak ověřte, že jsou vybrané všechny vstupní proměnné, ale **ne** cíl:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Snímek obrazovky znázorňující odebrání nastavení sloupce":::

Vyberte **Uložit**. Nakonec vyberte komponentu **bodového modelu** a ujistěte se, že zaškrtávací políčko **připojovat sloupce skóre k výstupu** není zaškrtnuté (místo vstupních hodnot a předpovědi se posílají jenom předpovědi *a* snižují latenci):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Snímek obrazovky znázorňující nastavení komponenty modelu skóre":::

V horní části plátna vyberte **Odeslat** .

Po úspěšném spuštění kanálu odvození můžete model nasadit do svého clusteru odvození. Vyberte **nasadit**, ve kterém se zobrazí dialogové okno **nastavit koncový bod v reálném** čase. Vyberte **nasadit nový koncový bod v reálném čase**, pojmenujte koncový bod **My-diabetes-model**, vyberte odvození, které jste vytvořili dříve, a pak vyberte **nasadit**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Snímek obrazovky zobrazující nastavení koncového bodu v reálném čase":::
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste viděli, jak se naučit a nasazovat model návrháře. V další části se dozvíte, jak tento model z Power BI spotřebovat (skore).

> [!div class="nextstepaction"]
> [Kurz: využití modelu v Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
