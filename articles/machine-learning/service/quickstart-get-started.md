---
title: Rychlý start prostřednictvím webu Azure portal
titleSuffix: Azure Machine Learning service
description: Začínáme se službou Azure Machine Learning. Vytvoření pracovního prostoru, což je základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely strojového učení pomocí webu Azure portal.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 96c4597c1031257eca329d0247c36f5d60456fe9
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582639"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí webu Azure portal

V tomto rychlém startu pomocí webu Azure Portal vytvoříte pracovní prostor služby Azure Machine Learning. Tento pracovní prostor je základním blokem v cloudu, který můžete využít k experimentování, trénování a nasazování modelů strojového učení pomocí služby Machine Learning. V tomto rychlém startu se používají cloudové prostředky a nevyžaduje se žádná instalace. Místo toho nakonfigurování serveru poznámkové bloky Jupyter najdete v tématu [rychlý start: Začínáme s Azure Machine Learning pomocí Pythonu](quickstart-create-workspace-with-python.md).  
 
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

V tomto rychlém startu jste pomocí následujících kroků:

* Vytvořit pracovní prostor ve vašem předplatném Azure.
* Vyzkoušejte s využitím Pythonu v Azure Poznámkový blok a protokolu hodnoty napříč více iterací.
* Zobrazit zaprotokolované hodnoty z vašeho pracovního prostoru.

Do vašeho pracovního prostoru se automaticky přidají následující prostředky Azure, pokud jsou regionálně dostupné:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Prostředky, které vytvoříte, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Machine Learning. Stejně jako u ostatních služeb Azure platí pro službu Machine Learning omezení určitých prostředků. Příkladem je velikost výpočetního clusteru. Další informace o [výchozí omezení a o tom, aby vám zvýšila kvótu](how-to-manage-quotas.md).

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Na stránce pracovního prostoru vyberte `Explore your Azure Machine Learning service Workspace`.

 ![Prozkoumejte pracovního prostoru](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Použití pracovního prostoru

Teď zjistíte, jak vám pracovní prostor pomůže spravovat vaše skripty strojového učení. V této části provedete následující kroky:

* Otevřete poznámkový blok ve službě Azure Notebooks.
* Spustíte kód, který vytvoří zaprotokolované hodnoty.
* Zobrazíte zaprotokolované hodnoty ve vašem pracovním prostoru.

Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

### <a name="open-a-notebook"></a>Otevření poznámkového bloku 

Poznámkových bloků Azure poskytuje bezplatná Cloudová platforma pro poznámkové bloky Jupyter, které je předem nakonfigurován s všechno, co potřebujete ke spuštění Machine Learning.  

Vyberte `Open Azure Notebooks` a vyzkoušejte první experiment.

 ![Otevření služby Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

Vaše organizace může před přihlášením vyžadovat [souhlas správce](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent).

Po přihlášení se otevře nová karta a zobrazí se výzva `Clone Library`. Vyberte `Clone`.


### <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spolu se dvěma poznámkovými bloky uvidíte soubor `config.json`. Tento konfigurační soubor obsahuje informace o vytvořeném pracovním prostoru.  

Výběrem souboru `01.run-experiment.ipynb` otevřete poznámkový blok.

Spuštění jednotlivé buňky v čase (Shift + Enter). Nebo vyberte `Cells` > `Run All` a spusťte celý poznámkový blok. Když se zobrazí hvězdička, __*__, vedle buňky, je spuštěna. Po dokončení kódu v této buňce se zobrazí číslo. 

Po dokončení spuštění všechny buňky v poznámkovém bloku, zaznamenané hodnoty lze zobrazit v pracovním prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

Po spuštění všech buněk v poznámkovém bloku se vraťte na stránku portálu.  

Vyberte `View Experiments`.

![Zobrazení experimentů](./media/quickstart-get-started/view_exp.png)

Zavře automaticky otevírané okno `Reports`.

Vyberte `my-first-experiment`.

Zobrazit informace o daném spuštění, že právě předvedl. Posuňte se dolů na stránce a najděte tabulku spuštění. Vyberte odkaz s číslem spuštění.

 ![Odkaz na historii spuštění](./media/quickstart-get-started/report.png)

Zobrazí se diagramy, které se automaticky vytvořily ze zaprotokolovaných hodnot. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram.

   ![Zobrazení historie](./media/quickstart-get-started/plots.png)

Protože kód přibližné PI použije náhodné hodnoty, se zobrazí vaše vykreslení různé hodnoty.  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranění jednoho pracovního prostoru. Zobrazení vlastností pracovního prostoru a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

Vytvořili jste prostředky potřebné k experimentování s modely a jejich nasazení. Také jste spustili kód v poznámkovém bloku. Kromě toho jste prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
