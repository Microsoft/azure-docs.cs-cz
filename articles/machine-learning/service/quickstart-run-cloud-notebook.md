---
title: Rychlý start spuštění poznámkového bloku v cloudu
titleSuffix: Azure Machine Learning service
description: Začínáme se službou Azure Machine Learning. Použijte server spravované poznámkového bloku v cloudu k vyzkoušení vašeho pracovního prostoru.  Váš pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely machine learningu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd643185fb4647b19082980edfd333c507aab8a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266252"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí serveru založené na cloudu poznámkového bloku

Vytvoření serveru založené na cloudu Poznámkový blok a pak použít ke spuštění kódu, který zaznamenává hodnoty ve službě Azure Machine Learning [pracovní prostor](concept-azure-machine-learning-architecture.md). Váš pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely strojového učení pomocí Machine Learning. 

Tento rychlý start ukazuje, jak vytvořit prostředek v cloudu ve vašem pracovním prostoru Azure Machine Learning nakonfigurovanou prostředí Pythonu potřebné ke spuštění Azure Machine Learning. Místo toho použít vlastní prostředí, najdete v článku [rychlý start: Začínáme s Azure Machine Learning pomocí serveru Poznámkový blok](quickstart-run-local-notebook.md).  
 
V tomto rychlém startu jste pomocí následujících kroků:

* Vytvořit pracovní stanice
* Spusťte Poznámkový blok Jupyter serveru na pracovní stanici
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.  Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření pracovního prostoru Azure Machine Learning](setup-create-workspace.md#portal) pokud ho nemáte.

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  Pokud si nejste jisti, jak vyhledat pracovního prostoru na portálu, přečtěte si postup [najít váš pracovní prostor](how-to-manage-workspace.md#view).

## <a name="create-a-workstation"></a>Vytvořit pracovní stanice 

Pracovní stanice Poznámkový blok poskytuje cloudové platformy pro aplikace Jupyter notebook, který byl předem nakonfigurován s všechno, co potřebujete ke spuštění služby Azure Machine Learning. Z pracovního prostoru můžete vytvořit této platformy, abyste mohli začít používat poznámkových bloků Jupyter.

1. Na stránce pracovního prostoru na webu Azure portal, vyberte **pracovní stanice Poznámkový blok** na levé straně.

1. Vyberte **vytvoření poznámkových bloků v pracovní stanici k Azure Machine Learning (Preview)**

   ![Prozkoumejte pracovního prostoru](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. **Pracovních stanic Poznámkový blok** část zobrazuje seznam všech serverů založené na cloudu poznámkového bloku k dispozici ve vašem pracovním prostoru.  Odsud můžete také spravovat tyto prostředky a je odstranit, pokud už nepotřebujete. 

1. Vyberte **přidání pracovní stanice** k vytvoření pracovní stanice poznámkového bloku.

     ![Vyberte Přidat pracovní stanice](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. V části Přidání pracovní stanice Poznámkový blok poskytnout pracovní stanice **název Compute** a vyberte **typ výpočtu**. Potom vyberte **Vytvořit**.

    ![Vytvoření nové pracovní stanici](media/quickstart-run-cloud-notebook/create-new-workstation.png)

    > [!NOTE]
    > Pracovní stanice trvá přibližně dvě minuty vytvořit. Po dokončení aktualizace stavu "Spuštěno" a odkazy na Jupyter a JupyterLab zobrazí.

## <a name="launch-jupyter-web-interface"></a>Spuštění webové rozhraní Jupyter

Po vytvoření pracovní stanice pomocí části pracovních stanic Poznámkový blok spustíte Jupyter webové rozhraní.

* Vyberte **Jupyter** nebo **Jupyter Lab** v **spuštění** sloupec pro pracovní stanice.

    ![Spusťte server poznámkového bloku Jupyter](./media/quickstart-run-cloud-notebook/start-notebook-server.png)

    To spustí Poznámkový blok serveru a na domovské stránce serveru na nové kartě prohlížeče.  Váš server ukazuje ukázkové poznámkové bloky můžete použít, abyste mohli začít se službou Azure Machine Learning.

### <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spuštění poznámkového bloku, který odhaduje pi a zaznamená chybu do pracovního prostoru.

1. Vyberte **01.run experiment.ipynb** otevřete Poznámkový blok.

1. Oblasti stav zjistíte Počkejte, až jádro byla spuštěna.  Zpráva zmizí, jakmile je jádro připraveno.

    ![Vyčkat, než jádra spuštění](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Po spuštění jádra spuštění jednotlivé buňky v čase pomocí **Shift + Enter**. Nebo vyberte **buňky** > **spustit všechny** celý poznámkový blok spustit. Když se zobrazí hvězdičku (__*__) vedle buňky, je stále spuštěna buňku. Po dokončení kódu v této buňce se zobrazí číslo.  

Po dokončení spuštění všechny buňky v poznámkovém bloku, zaznamenané hodnoty lze zobrazit v pracovním prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

1. Výstup `run` buňka obsahuje odkaz zpět na webu Azure portal pro zobrazení výsledků testu ve vašem pracovním prostoru. 

    ![Zobrazení experimentů](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klikněte na tlačítko **odkaz na webu Azure portal** zobrazíte informace o daném spuštění ve vašem pracovním prostoru.  Tento odkaz otevře pracovní prostor na webu Azure Portal.

1. Vykreslení zaznamenané hodnoty, které se zobrazí automaticky vytvořené v pracovním prostoru. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram.

   ![Zobrazení historie](./media/quickstart-run-cloud-notebook/web-results.png)

Protože kód přibližné PI použije náhodné hodnoty, se zobrazí vaše vykreslení různé hodnoty.  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranění jednoho pracovního prostoru. Zobrazení vlastností pracovního prostoru a vyberte **odstranit**.


## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste dokončili:

* Vytvořit pracovní stanice
* Spusťte Poznámkový blok Jupyter serveru na pracovní stanici
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.  Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
