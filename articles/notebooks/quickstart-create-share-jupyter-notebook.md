---
title: Vytvářejte a sdílejte aplikace Jupyter notebook v Azure
description: Rychle vytvořit a spustit Poznámkový blok Jupyter v poznámkových blocích Azure a potom sdílet s ostatními tento poznámkový blok.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 71220fa5aa0367d1cb1694582b4f96459a3016e7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277507"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Rychlý start: Vytvoření a sdílení poznámkového bloku

1. Přejděte na [poznámkových bloků Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete v tématu [rychlý start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce svého veřejného profilu vyberte **projekty** v horní části stránky:

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na **projekty** stránce **+ nový projekt** (Klávesová zkratka: n); na tlačítko se může objevit pouze jako **+** li úzké okno prohlížeče:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V **vytvořit nový projekt** automaticky otevírané okno, které se zobrazí, zadejte nebo nastavte tyto údaje a pak vyberte **vytvořit**:

   - **Název projektu**: Hello World v Pythonu
   - **ID projektu**: hello-world – python
   - **Veřejné projektu**: (zaškrtnuto)
   - **Vytvoření README.md**: (zaškrtnuto)

     ![Nový projekt automaticky otevírané okno s podrobnostmi mají údaj vyplněný](media/quickstarts/new-project-popup.png)

1. Po chvíli se poznámkových bloků Azure přejde do nového projektu. Poznámkového bloku přidejte do projektu tak, že vyberete **+ nový** rozevíracího seznamu (který může zobrazit jako pouze **+** ), pak vyberete **Poznámkový blok**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. V **vytvoření nového poznámkového bloku** automaticky otevírané okno, které se zobrazí, zadejte název souboru pro poznámkový blok, jako například *HelloWorldInPython.ipynb* ( *.ipynb* znamená, že poznámkový blok IronPython (Jupyter) ) a vyberte **Python 3.6** pro jazyk (také nazývané *jádra*):

    ![Vytvoření nového poznámkového bloku automaticky otevíraného okna](media/quickstarts/new-notebook-popup.png)

1. Vyberte **nový** dokončete vytváření Poznámkový blok, který se pak objeví v seznamu souboru projektu:

    ![Nový poznámkový blok, v seznamu souborů v projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

1. Vyberte nový poznámkový blok spustit v editoru; jádra vyberete (v tomto příkladu Python 3.6) se automaticky aktivuje pro tento poznámkový blok:

    ![Zobrazení nového poznámkového bloku v poznámkových bloků Azure](media/quickstarts/create-notebook-first-open.png)

1. Poznámkový blok má ve výchozím nastavení jednu buňku prázdný kód. Chcete-li změnit typ buňky do **Markdownu**, pomocí buňky typ rozevírací nabídky vyberte **Markdownu**:

    ![Změna typu buňky v nového poznámkového bloku](media/quickstarts/create-notebook-cell-type.png)

1. Zadejte nebo vložte do buňky následující text nadpisu:

    ```markdown
    # Hello World in Python
    ```

1. Protože upravujete Markdownu, text se zobrazí jako záhlaví znakem "#". Chcete-li zobrazit Markdown do kódu HTML, vyberte **spustit** tlačítko. Poznámkových bloků Azure automaticky vytvoří novou buňku kódu později:

    ![Na tlačítko pro spuštění pro buňku a vykreslený Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Do buňky kódu zadejte následující kód Pythonu:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Vyberte **spustit** (Klávesová zkratka: Shift + Enter) ke spuštění kódu. Pod buňku byste měli vidět úspěšné výstup podobný následujícímu textu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Uložení vyberte ikonu a práci uložte:

    ![Uložit ikonu na panelu nástrojů poznámkového bloku Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Vyberte **soubor** > **Zavřít a zastavit** příkaz nabídky k zastavení serveru a zavření okna prohlížeče.

## <a name="share-the-notebook"></a>Sdílet poznámkového bloku

Pokud chcete sdílet v poznámkovém bloku, v případě potřeby přepněte zpět na stránku projektu, klikněte pravým tlačítkem na soubor poznámkového bloku, vyberte **Kopírovat odkaz** (Klávesová zkratka: y) a vložte odkaz do odpovídající zprávu (e-mailu, zasílání rychlých zpráv atd.).

Na stránce projektu, můžete použít také **sdílené složky** kód pro vložení nabídky získat odkaz, vytvořte e-mailovou zprávu s odkazem nebo získat HTML a Markdown:

![Sdílet projekt – příkaz](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
