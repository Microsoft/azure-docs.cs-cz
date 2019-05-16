---
title: 'Rychlý start: Spuštění poznámkového bloku v cloudu'
titleSuffix: Azure Machine Learning service
description: Začínáme se službou Azure Machine Learning. Použijte server spravované poznámkového bloku v cloudu k vyzkoušení vašeho pracovního prostoru.  Váš pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely machine learningu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 4c15b9aa0bde704ad3665375d1c1a5bb8a1d32cf
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604127"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí serveru založené na cloudu poznámkového bloku

Vytvoření serveru založené na cloudu Poznámkový blok a pak ho použít.  V tomto rychlém startu spustíte kódu v Pythonu, který se přihlásí hodnoty [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md). Pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely strojového učení pomocí Machine Learning. 

Tento rychlý start ukazuje, jak vytvořit prostředek v cloudu ve vašem pracovním prostoru Azure Machine Learning nakonfigurovanou prostředí Pythonu potřebné ke spuštění Azure Machine Learning. Místo toho použít vlastní prostředí, najdete v článku [rychlý start: Začínáme s Azure Machine Learning pomocí serveru Poznámkový blok](quickstart-run-local-notebook.md).  

V tomto rychlém startu jste pomocí následujících kroků:

* Vytvoření nového poznámkového bloku založené na cloudu serveru ve vašem pracovním prostoru.
* Spuštění webového rozhraní Jupyter.
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru. Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu.

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning service, pokračujte [další části](#create-a-cloud-based-notebook-server). V opačném případě vytvořte si ho teď.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-a-cloud-based-notebook-server"></a>Vytvoření serveru založené na cloudu poznámkového bloku

 V pracovním prostoru vytvořit prostředek v cloudu a začněte využívat poznámkové bloky Jupyter. Tento prostředek poskytuje cloudové platformy předem nakonfigurovaným programem všechno, co potřebujete ke spuštění služby Azure Machine Learning.

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  Pokud si nejste jisti, jak vyhledat pracovního prostoru na portálu, přečtěte si postup [najít váš pracovní prostor](how-to-manage-workspace.md#view).

1. Na stránce pracovního prostoru na webu Azure Portal, vyberte **virtuálních počítačů Poznámkový blok** na levé straně.

1. Vyberte **+ nová** vytvoření poznámkového bloku virtuálního počítače.

     ![Vyberte nový virtuální počítač](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Zadejte název pro váš virtuální počítač. Potom vyberte **Vytvořit**.

    > [!NOTE]
    > Název vašeho virtuálního počítače poznámkového bloku musí být dlouhý 2 až 16 znaků. Platné znaky jsou písmena, číslice a znak /.  Název musí být také jedinečný ve vašem předplatném Azure.

    ![Vytvoření nového virtuálního počítače](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Počkejte přibližně 4 až 5 minut, dokud se stav změní na **systémem**.

## <a name="launch-jupyter-web-interface"></a>Spuštění webové rozhraní Jupyter

Po spuštění virtuálního počítače použijte **virtuálních počítačů Poznámkový blok** oddílu pro spuštění webové rozhraní Jupyter.

1. Vyberte **Jupyter** v **URI** sloupec pro váš virtuální počítač.  

    ![Spusťte server poznámkového bloku Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Odkaz spustí váš server poznámkového bloku a otevře se webová stránka poznámkového bloku Jupyter na nové kartě prohlížeče.  Tento odkaz bude fungovat jenom pro osobu, která vytvoří virtuální počítač.

1. Na webové stránce poznámkového bloku Jupyter hlavní název složky je vaše uživatelské jméno.  Vyberte tuto složku.

1. Ukázky foldername zahrnuje číslo verze, například **ukázky 1.0.33.1**.  Vyberte složku ukázky.

1. Vyberte **rychlý Start** poznámkového bloku.

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spuštění poznámkového bloku, který odhaduje pi a zaznamená chybu do pracovního prostoru.

1. Vyberte **01.run experiment.ipynb** otevřete Poznámkový blok.

1. Klikněte do první buňky kódu a vyberte **spustit**.

    > [!NOTE]
    > Buňky kódu mají hranaté závorky před sebou. Pokud jsou prázdné hranaté závorky (__[] č.__), kód nebyl spuštěn. Když je kód spuštěn, se zobrazí hvězdičku (__[*]__). Po dokončení kódu, číslo **[1]** se zobrazí.  Číslo vám říká pořadí, ve kterém byl spuštěn buňky.
    >
    > Použití **Shift + Enter** jako zástupce spustit buňky.

    ![Spuštění první buňky kódu](media/quickstart-run-cloud-notebook/cell1.png)

1. Spuštění druhého buňky kódu. Pokud se zobrazí pokyny k ověření, zkopírujte kód a přejděte na odkaz pro přihlášení. Po přihlášení bude váš prohlížeč nezapomeňte toto nastavení.  

    ![Ověřit](media/quickstart-run-cloud-notebook/authenticate.png)

1. Po dokončení počet buněk __[2]__ se zobrazí.  Pokud jste měli k přihlášení, zobrazí se zpráva stav úspěšné ověření.   Pokud máte nepovedlo se přihlásit, nezobrazí žádný výstup pro tuto buňku, jenom číslo, zobrazí se úspěšně spustil buňku.

    ![Zpráva o úspěchu](media/quickstart-run-cloud-notebook/success.png)

1. Spusťte zbývající buňky kódu.  Protože každá buňka doběhnutí, zobrazí se jeho počet buněk se zobrazí. Zobrazuje pouze poslední buňku žádný výstup.  

    Největší buňku kódu vidíte `run.log` použít na více místech. Každý `run.log` přidá jeho hodnotu do pracovního prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

1. Výstup `run` buňka obsahuje odkaz zpět na webu Azure portal pro zobrazení výsledků testu ve vašem pracovním prostoru.

    ![Zobrazení experimentů](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klikněte na tlačítko **odkaz na webu Azure portal** zobrazíte informace o daném spuštění ve vašem pracovním prostoru.  Tento odkaz otevře pracovní prostor na webu Azure Portal.

1. Vykreslení zaznamenané hodnoty, které se zobrazí automaticky vytvořené v pracovním prostoru. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram.

   ![Zobrazení historie](./media/quickstart-run-cloud-notebook/web-results.png)

Protože kód přibližné PI použije náhodné hodnoty, se zobrazí vaše vykreslení různé hodnoty.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="stop-the-notebook-vm"></a>Zastavit virtuální počítač poznámkového bloku

Zastavte virtuální počítač Poznámkový blok, když ji nepoužíváte ke snížení nákladů.  

1. V pracovním prostoru vyberte **virtuálních počítačů Poznámkový blok**.

   ![Zastavení virtuálního počítače serveru](./media/quickstart-run-cloud-notebook/stop-server.png)

1. V seznamu vyberte virtuální počítač.

1. Vyberte **Zastavit**.

1. Až budete chtít zase použít server, vyberte **Start**.

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranění jednoho pracovního prostoru. Zobrazení vlastností pracovního prostoru a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste dokončili tyto úlohy:

* Vytvoření poznámkového bloku virtuálního počítače.
* Spuštění webového rozhraní Jupyter.
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.  Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

Na webové stránce aplikace Jupyter Notebook projděte si další poznámkových bloků ve složce samples získat další informace o službě Azure Machine Learning.

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
