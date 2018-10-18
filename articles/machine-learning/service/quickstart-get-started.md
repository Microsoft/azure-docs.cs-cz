---
title: 'Rychlý start: Vytvoření pracovního prostoru služby Machine Learning na webu Azure Portal – Azure Machine Learning'
description: Použijte Azure Portal k vytvoření pracovního prostoru služby Azure Machine Learning. Tento pracovní prostor je základním blokem v cloudu pro experimentování, trénování a nasazení modelů strojového učení pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b6f0201a36a676e7647b9f5e60bc2df3415b9594
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831326"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning-service"></a>Rychlý start: Začínáme se službou Azure Machine Learning s využitím webu Azure Portal

V tomto rychlém startu pomocí webu Azure Portal vytvoříte pracovní prostor služby Azure Machine Learning. Tento pracovní prostor je základním blokem v cloudu pro experimentování, trénování a nasazení modelů strojového učení pomocí služby Azure Machine Learning. 

V tomto kurzu provedete následující:

* Vytvoření pracovního prostoru ve vašem předplatném Azure
* Jeho vyzkoušení ho pomocí Pythonu v Azure Notebooku a protokolování hodnot napříč více iteracemi
* Zobrazení protokolovaných hodnot ve vašem pracovním prostoru

Abychom vám usnadnili práci, jsou do pracovního prostoru automaticky přidané tyto prostředky Azure, pokud jsou regionálně dostupné: [registr kontejneru](https://azure.microsoft.com/services/container-registry/), [úložiště](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) a [trezor klíčů](https://azure.microsoft.com/services/key-vault/).

Prostředky, které vytvoříte, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Azure Machine Learning. Stejně jako u ostatních služeb Azure platí omezení určitých prostředků (např. velikost clusteru BatchAI) přidružených ke službě Azure Machine Learning. Přečtěte si tento [článek](how-to-manage-quotas.md) o výchozích omezeních a způsobu, jak požádat o navýšení kvóty.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Na stránce pracovního prostoru klikněte na `Explore your Azure Machine Learning service workspace`

 ![prozkoumání pracovního prostoru](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Použití pracovního prostoru

Teď zjistíte, jak vám pracovní prostor pomůže spravovat vaše skripty strojového učení. V této části provedete:

* Otevření poznámkového bloku v Azure Notebooks
* Spuštění kódu, který vytvoří zaznamenané hodnoty
* Zobrazení protokolovaných hodnot ve vašem pracovním prostoru

Toto je jeden příklad, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

### <a name="open-a-notebook"></a>Otevření poznámkového bloku 

Azure Notebooks nabízí bezplatnou cloudovou platformu pro poznámkové bloky Jupyter, které jsou předem nakonfigurované vším, co potřebujete ke spuštění služby Azure Machine Learning.  

Kliknutím na tlačítko `Open Azure Notebooks` můžete vyzkoušet svůj první experiment.

 ![Spuštění poznámkového bloku Azure](./media/quickstart-get-started/explore_ws.png)

Po přihlášení se otevře nová karta a zobrazí se výzva `Clone Library`.  Klikněte na `Clone`


### <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spolu se dvěma poznámkovými bloky uvidíte soubor `config.json`.  Tento konfigurační soubor obsahuje informace o pracovním prostoru, který jste právě vytvořili.  

Kliknutím na `01.run-experiment.ipynb` otevřete poznámkový blok.

Jednotlivé buňky můžete spustit pomocí `Shift`+`Enter`.  Nebo pomocí nabídky `Cells` > `Run All` spusťte celý poznámkový blok.  Pokud se vedle buňky zobrazí [*], znamená to, že je spuštěná.  Jakmile se kód buňky dokončí, zobrazí se číslo.

Může se zobrazit výzva k přihlášení.  Zkopírujte kód ve zprávě, pak klikněte na odkaz a vložte kód do nového okna.  Dávejte pozor, abyste nezkopírovali mezeru před nebo za kódem.  Přihlaste se pomocí stejného účtu, který jste použili na webu Azure Portal.

 ![přihlášení](./media/quickstart-get-started/login.png)

Druhá buňka v poznámkovém bloku čte z `config.json` pro připojení k vašemu pracovnímu prostoru.
```
ws = Workspace.from_config()
```

Třetí buňka kódu spustí experiment s názvem „my-first-experiment“.  Tento název použijete k vyhledání informací o spuštění zpět ve vašem pracovním prostoru.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Všimněte si v poslední buňce poznámkového bloku hodnot, které se zapisují do souboru protokolu.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Tyto hodnoty můžete zobrazit ve svém pracovním prostoru po spuštění kódu.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

Po dokončení všech buněk v poznámkovém bloku přejděte zpět na stránku portálu.  

Klikněte na `View Experiments`.

![zobrazení experimentů](./media/quickstart-get-started/view_exp.png)

Zavře automaticky otevírané okno `Reports`.

Klikněte na `my-first-experiment`.

Prohlédněte si informace o spuštění, které jste právě provedli.  Posuňte se dolů na stránce a najděte tabulku spuštění a klikněte na odkaz s číslem spuštění.

 ![odkaz historie spuštění](./media/quickstart-get-started/report.png)

Zobrazí se diagramy, které se automaticky vytvořily z protokolovaných hodnot:

   ![zobrazení historie](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor tak, že zobrazíte vlastnosti pracovního prostoru a vyberete tlačítko Odstranit.

## <a name="next-steps"></a>Další kroky

Teď máte vytvořené prostředky potřebné k tomu, abyste mohli začít experimentovat a nasazovat modely. Také jste spustili kód v poznámkovém bloku a prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Podrobný pracovní postup najdete v kurzech pro Azure Machine Learning, kde můžete trénovat a nasadit model.  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
