---
title: Vytvoření a sdílení poznámkového bloku Jupyter v Azure Notebooks Preview
description: Rychle vytvořte a spusťte Poznámkový blok Jupyter Azure Notebooks ve verzi Preview a potom tento poznámkový blok sdílejte s ostatními.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064440"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Rychlý Start: vytvoření a sdílení poznámkového bloku v Azure Notebooks Preview

V tomto rychlém startu vytvoříte a spustíte na Azure Notebooks Poznámkový blok Jupyter a potom tento poznámkový blok nasdílíte s ostatními. Jupyter umožňuje snadno zkombinovat Markdownu text, spustitelný kód, trvalá data, grafiku a vizualizace na jednom plátně, poznámkovém bloku. Azure Notebooks je bezplatná hostovaná služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu, která nevyžaduje instalaci.

## <a name="prerequisites"></a>Požadavky
Žádné

## <a name="create-a-new-project-and-notebook"></a>Vytvořit nový projekt a Poznámkový blok

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Přejít na [web Azure Notebooks (https://notebooks.azure.com)](https://notebooks.azure.com) a přihlásit se. Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n). Tlačítko se může zobrazit jenom jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

   - **Název projektu**: Hello World v Pythonu
   - **ID projektu**: Hello-World – Python
   - **Veřejný projekt**: (nezaškrtnuto)
   - **Vytvořit Readme.MD**: (nezaškrtnuto)

     ![Nový projekt automaticky otevírané okno s podrobnostmi mají údaj vyplněný](media/quickstarts/new-project-popup.png)

1. Po chvíli se poznámkových bloků Azure přejde do nového projektu. Přidejte do projektu Poznámkový blok tak, že vyberete rozevírací seznam **+ Nový** (může se zobrazit jenom **+** ) a pak vyberete **Poznámkový blok**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. V automaticky otevřeném okně **vytvořit nový Poznámkový blok** zadejte název souboru pro svůj Poznámkový blok, například *HelloWorldInPython. ipynb* ( *. ipynb* označuje notebook ironpythonu (Jupyter)) a vyberte **Python 3,6** pro jazyk (také označovaný jako *jádro*):

    ![Vytvoření nového poznámkového bloku automaticky otevíraného okna](media/quickstarts/new-notebook-popup.png)

1. Když vyberete **Nový** , dokončí se vytváření poznámkového bloku, který se pak zobrazí v seznamu souborů vašeho projektu:

    ![Nový poznámkový blok, v seznamu souborů v projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

1. Vyberte nový poznámkový blok spustit v editoru; jádra vyberete (v tomto příkladu Python 3.6) se automaticky aktivuje pro tento poznámkový blok:

    ![Zobrazení nového poznámkového bloku v poznámkových bloků Azure](media/quickstarts/create-notebook-first-open.png)

1. Poznámkový blok má ve výchozím nastavení jednu buňku prázdný kód. Chcete-li změnit typ buňky na **Markdownu**, použijte rozevírací seznam typ buňky k výběru **Markdownu**:

    ![Změna typu buňky v nového poznámkového bloku](media/quickstarts/create-notebook-cell-type.png)

1. Zadejte nebo vložte do buňky následující text nadpisu:

    ```markdown
    # Hello World in Python
    ```

1. Protože upravujete Markdownu, text se zobrazí jako záhlaví znakem "#". Pokud chcete Markdownu vykreslit do HTML, vyberte tlačítko **Spustit** . Poznámkových bloků Azure automaticky vytvoří novou buňku kódu později:

    ![Na tlačítko pro spuštění pro buňku a vykreslený Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Do buňky kódu zadejte následující kód Pythonu:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Pro spuštění kódu vyberte **Spustit** (Klávesová zkratka: SHIFT + ENTER). Pod buňku byste měli vidět úspěšné výstup podobný následujícímu textu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Uložení vyberte ikonu a práci uložte:

    ![Uložit ikonu na panelu nástrojů poznámkového bloku Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Vyberte **soubor** > **Zavřít a zastavit** příkaz nabídky k zastavení serveru a zavření okna prohlížeče.

## <a name="share-the-notebook"></a>Sdílet poznámkového bloku

Chcete-li sdílet svůj Poznámkový blok, v případě potřeby přepněte zpět na stránku projektu, klikněte pravým tlačítkem na soubor poznámkového bloku, vyberte **Kopírovat odkaz** (Klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy (E-mail, im atd.).

Na stránce projekt můžete také pomocí nabídky **sdílet** získat odkaz, vytvořit e-mailovou zprávu s odkazem nebo získat kód pro vložení HTML a Markdownu:

![Sdílet projekt – příkaz](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
