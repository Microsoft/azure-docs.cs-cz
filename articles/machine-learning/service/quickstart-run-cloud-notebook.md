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
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 68f24d4d019af873a0ca45792a3cbcc3dd3c3c3f
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476043"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí serveru založené na cloudu poznámkového bloku

Nemusíte nic instalovat.  Začínáme se službou Azure Machine Learning pomocí poznámkového bloku spravovaného serveru v cloudu. Pokud chcete místo toho do prostředí Pythonu nainstalujte sadu SDK, přečtěte si téma [rychlý start: Začínáme s Azure Machine Learning pomocí serveru Poznámkový blok](quickstart-run-local-notebook.md).

Tento rychlý start ukazuje, jak můžete použít [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md) k udržení přehledu o vašich se strojovým učením.  Vytvoříte [poznámkového bloku virtuálního počítače (Preview)](how-to-configure-environment.md#notebookvm), zabezpečené, založené na cloudu Azure pracovní stanice, které poskytuje server poznámkového bloku Jupyter, JupyterLab a plně připravený ML prostředí. Potom spusťte Poznámkový blok Python na tomto virtuálním počítači, který hodnoty do protokolu do pracovního prostoru.

V tomto rychlém startu jste pomocí následujících kroků:

* Vytvoření pracovního prostoru
* Vytvoření poznámkového bloku virtuálního počítače ve vašem pracovním prostoru.
* Spuštění webového rozhraní Jupyter.
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru. Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu.

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning service, pokračujte [další části](#create-notebook). V opačném případě vytvořte si ho teď.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Vytvoření poznámkového bloku virtuálního počítače

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

    > [!TIP]
    > Tato složka se nachází na [kontejner úložiště](concept-workspace.md#resources) ve vašem pracovním prostoru, nikoli v poznámkovém bloku Virtuálního počítače.  Můžete odstranit poznámkového bloku virtuálního počítače a tom zachovat veškerou práci.  Když vytvoříte nový poznámkový blok virtuální počítač později, načte stejné složky.

1. Ukázky foldername zahrnuje číslo verze, například **ukázky 1.0.33.1**.  Vyberte složku ukázky.

1. Vyberte **rychlý Start** složky.

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spuštění poznámkového bloku, který odhaduje pi a zaznamená chybu do pracovního prostoru.

1. Vyberte **01.run experiment.ipynb** otevřete Poznámkový blok.

1. Pokud se zobrazí upozornění "Jádra nebyl nalezen", vyberte jádra **Python 3.6 – AzureML** (přibližně střední směrem dolů v seznamu) a nastavte jádra.

1. Klikněte do první buňky kódu a vyberte **spustit**.

    > [!NOTE]
    > Buňky kódu mají hranaté závorky před sebou. Pokud jsou prázdné hranaté závorky ( __[] č.__ ), kód nebyl spuštěn. Když je kód spuštěn, se zobrazí hvězdičku ( __[*]__ ). Po dokončení kódu, číslo **[1]** se zobrazí.  Číslo vám říká pořadí, ve kterém byl spuštěn buňky.
    >
    > Použití **Shift + Enter** jako zástupce spustit buňky.

    ![Spuštění první buňky kódu](media/quickstart-run-cloud-notebook/cell1.png)

1. Spuštění druhého buňky kódu. Pokud se zobrazí pokyny k ověření, zkopírujte kód a přejděte na odkaz pro přihlášení. Po přihlášení bude váš prohlížeč nezapomeňte toto nastavení.  

    ![Ověření](media/quickstart-run-cloud-notebook/authenticate.png)

1. Po dokončení počet buněk __[2]__ se zobrazí.  Pokud jste měli k přihlášení, zobrazí se zpráva stav úspěšné ověření.   Pokud máte nepovedlo se přihlásit, nezobrazí žádný výstup pro tuto buňku, jenom číslo, zobrazí se úspěšně spustil buňku.

    ![Zpráva o úspěchu](media/quickstart-run-cloud-notebook/success.png)

1. Spusťte zbývající buňky kódu.  Protože každá buňka doběhnutí, zobrazí se jeho počet buněk se zobrazí. Zobrazuje pouze poslední buňku žádný výstup.  

    Největší buňku kódu vidíte `run.log` použít na více místech. Každý `run.log` přidá jeho hodnotu do pracovního prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

1. Výstup `run` buňka obsahuje odkaz zpět na webu Azure portal pro zobrazení výsledků testu ve vašem pracovním prostoru.

    ![Zobrazení experimentů](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klikněte na tlačítko **odkaz na webu Azure portal** zobrazíte informace o daném spuštění ve vašem pracovním prostoru.  Tento odkaz otevře pracovní prostor na webu Azure Portal.

1. Vykreslení zaznamenané hodnoty, které se zobrazí automaticky vytvořené v pracovním prostoru. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram. Zde naleznete příklad:

   ![Zobrazení historie](./media/quickstart-run-cloud-notebook/web-results.png)

Protože kód přibližné PI použije náhodné hodnoty, může vypadat jinak vaše vykreslení.  

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

* Vytvoření pracovního prostoru
* Vytvoření poznámkového bloku virtuálního počítače.
* Spuštění webového rozhraní Jupyter.
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.  Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

Na webové stránce aplikace Jupyter Notebook v **rychlý Start** složky otevřen a spusťte **02.deploy-web-service.ipynb** Poznámkový blok jupyter zjistěte, jak nasadit webovou službu.

Také na webové stránce aplikace Jupyter Notebook, projděte si další poznámkových bloků ve složce samples získat další informace o službě Azure Machine Learning.

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
