---
title: 'Rychlý start: Vytvoření pracovního prostoru služby Machine Learning na webu Azure Portal – Azure Machine Learning'
description: Použijte portál Azure Portal k vytvoření pracovního prostoru Azure Machine Learning. Tento pracovní prostor je základním blokem v cloudu, který můžete využít k experimentování, trénování a nasazování modelů strojového učení pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b81e40298eae0f0b44f37e7f8f16beaddad999a5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456809"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme se službou Azure Machine Learning s využitím webu Azure Portal

V tomto rychlém startu pomocí webu Azure Portal vytvoříte pracovní prostor služby Azure Machine Learning. Tento pracovní prostor je základním blokem v cloudu, který můžete využít k experimentování, trénování a nasazování modelů strojového učení pomocí služby Machine Learning. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

V tomto rychlém startu:

* Vytvořit pracovní prostor ve vašem předplatném Azure.
* Vyzkoušíte ho pomocí Pythonu v poznámkovém bloku Azure a budete protokolovat hodnoty napříč několika iteracemi.
* Zobrazíte zaprotokolované hodnoty ve vašem pracovním prostoru.

Do vašeho pracovního prostoru se automaticky přidají následující prostředky Azure, pokud jsou regionálně dostupné:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Prostředky, které vytvoříte, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Machine Learning. Stejně jako u ostatních služeb Azure platí pro službu Machine Learning omezení určitých prostředků. Příkladem je velikost clusteru Azure Batch AI. Informace o výchozích omezeních a postup navýšení kvóty najdete v [tomto článku](how-to-manage-quotas.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Na stránce pracovního prostoru vyberte `Explore your Azure Machine Learning service workspace`.

 ![Prozkoumání pracovního prostoru](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Použití pracovního prostoru

Teď zjistíte, jak vám pracovní prostor pomůže spravovat vaše skripty strojového učení. V této části:

* Otevřete poznámkový blok ve službě Azure Notebooks.
* Spustíte kód, který vytvoří zaprotokolované hodnoty.
* Zobrazíte zaprotokolované hodnoty ve vašem pracovním prostoru.

Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

### <a name="open-a-notebook"></a>Otevření poznámkového bloku 

Azure Notebooks nabízí bezplatnou cloudovou platformu pro poznámkové bloky Jupyter, které jsou předem nakonfigurované vším, co potřebujete ke spuštění služby Machine Learning.  

Vyberte `Open Azure Notebooks` a vyzkoušejte první experiment.

 ![Otevření služby Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

Vaše organizace může před přihlášením vyžadovat [souhlas správce](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent).

Po přihlášení se otevře nová karta a zobrazí se výzva `Clone Library`. Vyberte `Clone`


### <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spolu se dvěma poznámkovými bloky uvidíte soubor `config.json`. Tento konfigurační soubor obsahuje informace o vytvořeném pracovním prostoru.  

Výběrem souboru `01.run-experiment.ipynb` otevřete poznámkový blok.

Jednotlivé buňky můžete spustit pomocí `Shift`+`Enter`. Nebo vyberte `Cells` > `Run All` a spusťte celý poznámkový blok. Když se vedle buňky zobrazí hvězdička [*], znamená to, že je spuštěná. Po dokončení kódu v této buňce se zobrazí číslo.

Může se zobrazit výzva k přihlášení. Zkopírujte kód ve zprávě. Potom vyberte odkaz a vložte kód do nového okna. Dávejte pozor, abyste nezkopírovali mezeru před nebo za kódem. Přihlaste se pomocí stejného účtu, který jste použili na webu Azure Portal.

 ![Přihlásit se](./media/quickstart-get-started/login.png)

Druhá buňka v poznámkovém bloku čte z `config.json` pro připojení k vašemu pracovnímu prostoru.
```
ws = Workspace.from_config()
```

Třetí buňka kódu spustí experiment s názvem „my-first-experiment“. Tento název použijte k vyhledání informací o spuštění zpět ve vašem pracovním prostoru.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

V poslední buňce poznámkového bloku si všimněte hodnot, které se zapisují do souboru protokolu.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Tyto hodnoty můžete zobrazit ve svém pracovním prostoru po spuštění kódu.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

Po spuštění všech buněk v poznámkovém bloku se vraťte na stránku portálu.  

Vyberte `View Experiments`.

![Zobrazení experimentů](./media/quickstart-get-started/view_exp.png)

Zavře automaticky otevírané okno `Reports`.

Vyberte `my-first-experiment`.

Prohlédněte si informace o spuštění, které jste právě provedli. Posuňte se dolů na stránce a najděte tabulku spuštění. Vyberte odkaz s číslem spuštění.

 ![Odkaz na historii spuštění](./media/quickstart-get-started/report.png)

Zobrazí se diagramy, které se automaticky vytvořily ze zaprotokolovaných hodnot.  

   ![Zobrazení historie](./media/quickstart-get-started/plots.png)

Vzhledem k tomu, že kód pro odhad čísla pí používá náhodné hodnoty, ve vašich diagramech se zobrazí jiné hodnoty.

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Vytvořili jste prostředky potřebné k experimentování s modely a jejich nasazení. Také jste spustili kód v poznámkovém bloku. Kromě toho jste prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Podrobný pracovní postup najdete v kurzech pro Machine Learning věnovaných trénování a nasazení modelu.  

> [!div class="nextstepaction"]
> [Kurz: Školení modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
