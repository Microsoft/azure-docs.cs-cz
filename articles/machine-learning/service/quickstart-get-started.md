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
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 0dab14e9c8f99a10b1dc26c2adb1e88d3b18ee90
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468147"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí webu Azure portal

Použijte portál Azure Portal k vytvoření pracovního prostoru Azure Machine Learning. Tento pracovní prostor je základním blokem v cloudu, který můžete využít k experimentování, trénování a nasazování modelů strojového učení pomocí služby Machine Learning. V tomto rychlém startu se používají cloudové prostředky a nevyžaduje se žádná instalace. Místo toho nakonfigurování serveru poznámkové bloky Jupyter najdete v tématu [rychlý start: Začínáme s Azure Machine Learning pomocí Pythonu](quickstart-create-workspace-with-python.md).  
 
V tomto rychlém startu jste pomocí následujících kroků:

* Vytvořit pracovní prostor ve vašem předplatném Azure.
* Vyzkoušejte s Pythonem v Jupyter notebook a protokolu hodnoty napříč více iterací.
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


## <a name="use-the-workspace"></a>Použití pracovního prostoru

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Teď zjistěte, jak pracovní prostor vám pomůže spravovat vaše skriptů strojového učení. V této části provedete následující kroky:

* Otevřete poznámkový blok ve službě Azure Notebooks.
* Spustíte kód, který vytvoří zaprotokolované hodnoty.
* Zobrazíte zaprotokolované hodnoty ve vašem pracovním prostoru.

Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

### <a name="open-a-notebook"></a>Otevření poznámkového bloku 

Poznámkových bloků Azure poskytuje bezplatná Cloudová platforma pro poznámkové bloky Jupyter, které je předem nakonfigurován s všechno, co potřebujete ke spuštění Machine Learning. Z pracovního prostoru můžete spustit tuto platformu, abyste mohli začít používat váš pracovní prostor služby Azure Machine Learning.

1. Na stránce pracovního prostoru vyberte **prozkoumání služby Azure Machine Learning Workspace**.

 ![Prozkoumejte pracovního prostoru](./media/quickstart-get-started/explore_aml.png)

1. Vyberte **otevřít poznámkových bloků Azure** vyzkoušet prvního experimentu v Azure poznámkových bloků.  Poznámkových bloků Azure je samostatná služba, která umožňuje spouštět poznámkové bloky Jupyter zdarma v cloudu.  Pokud použijete tento odkaz na službu, informace o tom, jak se připojit k vašemu pracovnímu prostoru se přidají do knihovny, kterou jste vytvořili v poznámkových bloků Azure.

 ![Otevření služby Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

1. Přihlaste se k Azure poznámkových bloků.  Ujistěte se, že se že přihlásíte pomocí stejného účtu, který jste použili pro přihlášení na webu Azure portal. Vaše organizace může před přihlášením vyžadovat [souhlas správce](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent).

1. Po přihlášení se otevře nová karta a zobrazí se výzva `Clone Library`. Klonování tuto knihovnu načte sadu poznámkových bloků a jiných souborů do účtu poznámkových bloků Azure.  Tyto soubory můžete prozkoumat možnosti služby Azure Machine Learning.

1. Zrušte zaškrtnutí políčka **veřejné** tak, aby informace o pracovním prostoru není sdílet s ostatními.

1. Vyberte **klonování**.

 ![Klonovat do knihovny](./media/quickstart-get-started/clone.png)

1. Pokud se zobrazí, že v zastaveném stavu projektu, klikněte na **spustili bezplatnou Compute** používat server zdarma poznámkového bloku.

    ![Spuštění projektu na bezplatnými výpočetními funkcemi](./media/quickstart-get-started/run-project.png)

### <a name="run-the-notebook"></a>Spuštění poznámkového bloku

V seznamu souborů pro tento projekt, se zobrazí `config.json` souboru. Tento konfigurační soubor obsahuje informace o pracovním prostoru, kterou jste vytvořili na webu Azure Portal.  Tento soubor umožňuje váš kód k připojení a přidání informací do pracovního prostoru.

1. Vyberte **01.run experiment.ipynb** otevřete Poznámkový blok.

1. Oblasti stav zjistíte Počkejte, až jádro byla spuštěna.  Zpráva zmizí, jakmile je jádro připraveno.

    ![Vyčkat, než jádra spuštění](./media/quickstart-get-started/wait-for-kernel.png)

1. Po spuštění jádra spuštění jednotlivé buňky v čase pomocí **Shift + Enter**. Nebo vyberte **buňky** > **spustit všechny** celý poznámkový blok spustit. Když se zobrazí hvězdička, __*__, vedle buňky, je stále spuštěna buňku. Po dokončení kódu v této buňce se zobrazí číslo. 

1. Postupujte podle pokynů v poznámkovém bloku k ověření vašeho předplatného Azure.

Po dokončení spuštění všechny buňky v poznámkovém bloku, zaznamenané hodnoty lze zobrazit v pracovním prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

1. Výstup `run` buňka obsahuje odkaz zpět na webu Azure portal pro zobrazení výsledků testu ve vašem pracovním prostoru. 

    ![Zobrazení experimentů](./media/quickstart-get-started/view_exp.png)

1. Klikněte na tlačítko **odkaz na webu Azure Portal** zobrazíte informace o daném spuštění ve vašem pracovním prostoru.  Tento odkaz otevře pracovní prostor na webu Azure Portal.

1. Vykreslení zaznamenané hodnoty, které se zobrazí automaticky vytvořené v pracovním prostoru. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram.

   ![Zobrazení historie](./media/quickstart-get-started/web-results.png)

Protože kód přibližné PI použije náhodné hodnoty, se zobrazí vaše vykreslení různé hodnoty.  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranění jednoho pracovního prostoru. Zobrazení vlastností pracovního prostoru a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

Vytvořili jste prostředky potřebné k experimentování s modely a jejich nasazení. Také jste spustili kód v poznámkovém bloku. Kromě toho jste prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
