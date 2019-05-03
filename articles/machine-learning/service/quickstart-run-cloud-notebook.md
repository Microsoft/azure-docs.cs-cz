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
ms.openlocfilehash: 3d4127226037bf28ba677a49f6444ca987118cb9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023896"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí serveru založené na cloudu poznámkového bloku

Vytvoření serveru založené na cloudu Poznámkový blok a pak ho použít.  V tomto rychlém startu spustíte kódu v Pythonu, který se přihlásí hodnoty [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md). Pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely strojového učení pomocí Machine Learning. 

Tento rychlý start ukazuje, jak vytvořit prostředek v cloudu ve vašem pracovním prostoru Azure Machine Learning nakonfigurovanou prostředí Pythonu potřebné ke spuštění Azure Machine Learning. Místo toho použít vlastní prostředí, najdete v článku [rychlý start: Začínáme s Azure Machine Learning pomocí serveru Poznámkový blok](quickstart-run-local-notebook.md).  
 
V tomto rychlém startu jste pomocí následujících kroků:

* Vytvoření nového poznámkového bloku založené na cloudu serveru ve vašem pracovním prostoru
* Spuštění webového rozhraní Jupyter
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.  Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning.  [Vytvoření pracovního prostoru](setup-create-workspace.md#portal) nyní, pokud ho nemáte.

## <a name="create-a-cloud-based-notebook-server"></a>Vytvoření serveru založené na cloudu poznámkového bloku

 V pracovním prostoru vytvořit prostředek v cloudu a začněte využívat poznámkové bloky Jupyter. Tento prostředek poskytuje cloudové platformy předem nakonfigurovaným programem všechno, co potřebujete ke spuštění služby Azure Machine Learning.

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  Pokud si nejste jisti, jak vyhledat pracovního prostoru na portálu, přečtěte si postup [najít váš pracovní prostor](how-to-manage-workspace.md#view).

1. Na stránce pracovního prostoru na webu Azure Portal, vyberte **virtuálních počítačů Poznámkový blok** na levé straně.

1. Vyberte **+ nová** vytvoření poznámkového bloku virtuálního počítače.

     ![Vyberte nový virtuální počítač](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Zadejte název pro váš virtuální počítač. Potom vyberte **Vytvořit**. 

    ![Vytvoření nového virtuálního počítače](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Počkejte přibližně 4 až 5 minut a pak vyberte **aktualizovat**.  Zkuste aktualizovat každých 30 sekund, nebo dokud je stav **systémem**.

    ![Obnovení](media/quickstart-run-cloud-notebook/refresh.png)

## <a name="launch-jupyter-web-interface"></a>Spuštění webové rozhraní Jupyter

Po spuštění virtuálního počítače použijte **virtuálních počítačů Poznámkový blok** oddílu pro spuštění webové rozhraní Jupyter.

1. Vyberte **Jupyter** v **URI** sloupec pro váš virtuální počítač.  

    ![Spusťte server poznámkového bloku Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Odkaz spustí váš server poznámkového bloku a otevře se webová stránka poznámkového bloku Jupyter na nové kartě prohlížeče.  Tento odkaz bude fungovat jenom pro osobu, která vytvoří virtuální počítač.

1. Na webové stránce poznámkového bloku Jupyter, vyberte **samples/quickstart** složky najdete v rychlém startu poznámkového bloku.

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spuštění poznámkového bloku, který odhaduje pi a zaznamená chybu do pracovního prostoru.

1. Vyberte **01.run experiment.ipynb** otevřete Poznámkový blok.

1. Může se zobrazit zpráva, která nebyla nastavena jádra.  Vyberte **Python 3.6 – AzureML**a pak vyberte **nastavit jádra**.

   ![Nastavte jádra](./media/quickstart-run-cloud-notebook/set-kernel.png)

1. Oblasti stav zjistíte Počkejte, až jádro byla spuštěna. Zpráva zmizí, jakmile je jádro připraveno.

    ![Vyčkat, než jádra spuštění](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1.  Klikněte do první buňky kódu a vyberte **spustit**.

    > [!NOTE]
    > Buňky kódu mají hranaté závorky před sebou. Pokud jsou prázdné hranaté závorky (__[] č.__), kód nebyl spuštěn. Když je kód spuštěn, se zobrazí hvězdičku (__[*]__). Po dokončení kódu, číslo **[1]** se zobrazí.  Číslo vám říká pořadí, ve kterém byl spuštěn buňky.
    >
    > Použití **Shift + Enter** jako zástupce spustit buňky.

    ![Spuštění první buňky kódu](media/quickstart-run-cloud-notebook/cell1.png)

1. Spuštění druhého buňky kódu. Pokud se zobrazí pokyny k ověření, zkopírujte kód a přejděte na odkaz pro přihlášení. Po přihlášení bude váš prohlížeč nezapomeňte toto nastavení.  

    > [!TIP]
    > Nezapomeňte zkopírovat místo za kód.  

    ![Ověření](media/quickstart-run-cloud-notebook/authenticate.png)

1. Až budete hotovi, počet buněk __[2]__ se zobrazí.  Pokud jste měli k přihlášení, zobrazí se zpráva stav úspěšné ověření.   Pokud máte nepovedlo se přihlásit, nezobrazí žádný výstup pro tuto buňku, jenom číslo, zobrazí se úspěšně spustil buňku.

    ![Zpráva o úspěchu](media/quickstart-run-cloud-notebook/success.png)

1. Spusťte zbývající buňky kódu.  Protože každá buňka doběhnutí, zobrazí se jeho počet buněk se zobrazí. Zobrazuje pouze poslední buňku žádný výstup.  Největší buňku kódu vidíte `run.log` použít na více místech. Každý `run.log` přidá jeho hodnotu do pracovního prostoru.


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

1. Jakmile budete připraveni na uživatele na serveru znovu, vyberte **Start**.

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranění jednoho pracovního prostoru. Zobrazení vlastností pracovního prostoru a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste dokončili tyto úlohy:

* Vytvoření poznámkového bloku virtuálního počítače
* Spuštění aplikace Jupyter Notebook server na virtuální počítač v poznámkovém bloku
* Otevřete Poznámkový blok, který obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích.
* Spuštění poznámkového bloku.
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.  Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)