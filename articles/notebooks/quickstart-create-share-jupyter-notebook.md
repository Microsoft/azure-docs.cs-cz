---
title: Vytvoření a sdílení poznámkového bloku Jupyter v náhledu poznámkových bloků Azure
description: Rychle vytvořte a spusťte poznámkový blok Jupyter v náhledu poznámkových bloků Azure a pak ho sdílejte s ostatními.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064440"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Úvodní příručka: Vytvoření a sdílení poznámkového bloku v náhledu poznámkových bloků Azure

V tomto rychlém startu vytvoříte a spustíte poznámkový blok Jupyter v poznámkových blocích Azure a pak tento poznámkový blok sdílíte s ostatními. Jupyter umožňuje snadno kombinovat Markdown text, spustitelný kód, trvalá data, grafiku a vizualizace na jednom sdíleném plátně, notebooku. Azure Notebooks je bezplatná hostovaná služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu, která nevyžaduje instalaci.

## <a name="prerequisites"></a>Požadavky
Žádné.

## <a name="create-a-new-project-and-notebook"></a>Vytvoření nového projektu a poznámkového bloku

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Přejděte na [web Poznámkových bloků Azure (https://notebooks.azure.com) ](https://notebooks.azure.com) a přihlaste se. Podrobnosti najdete [v tématu Úvodní příručka – Přihlášení k poznámkovým blokům Azure](quickstart-sign-in-azure-notebooks.md).

1. Na stránce veřejného profilu vyberte **moje projekty** v horní části stránky:

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte + Nový projekt (klávesová **zkratka:** n). Tlačítko se může **+** zobrazit pouze tak, jako by bylo okno prohlížeče úzké:

    ![Příkaz Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. Ve vyskakovacím okně **Vytvořit nový projekt,** které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **Vytvořit**:

   - **Název projektu**: Hello World v Pythonu
   - **ID projektu**: hello-world-python
   - **Veřejný projekt**: (schváleno)
   - **Vytvoření README.md**: (vymazáno)

     ![Nový projekt vyskakovací okno s vyplněnými detaily](media/quickstarts/new-project-popup.png)

1. Po chvíli vás poznámkové bloky Azure přenese k novému projektu. Přidejte poznámkový blok do projektu tak, že vyberete rozevírací rozevírací tlačítko **+ Nový** (který se může zobrazit pouze **+**) a pak vyberete **Poznámkový blok**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. V vyskakovacím panelu **Vytvořit nový poznámkový blok,** který se zobrazí, zadejte název souboru pro poznámkový blok, například *HelloWorldInPython.ipynb* (*.ipynb* znamená Notebook IronPython (Jupyter) a vyberte **Python 3.6** pro jazyk (označovaný také jako *jádro*):

    ![Místní okno Vytvořit nový poznámkový blok](media/quickstarts/new-notebook-popup.png)

1. Chcete-li dokončit vytvoření poznámkového bloku, který se pak zobrazí v seznamu souborů projektu, vyberte **nový:**

    ![Nový poznámkový blok zobrazený v seznamu souborů projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

1. Vyberte nový poznámkový blok, který chcete spustit v editoru; jádro, které jste vybrali (Python 3.6 v tomto příkladu) se automaticky aktivuje pro tento notebook:

    ![Zobrazení nového poznámkového bloku v poznámkových blocích Azure](media/quickstarts/create-notebook-first-open.png)

1. Ve výchozím nastavení má poznámkový blok jednu prázdnou buňku kódu. Chcete-li změnit typ buňky na **Markdown**, vyberte pomocí rozevíracího pole Typ buňky položku **Markdown**:

    ![Změna typu buňky v novém poznámkovém bloku](media/quickstarts/create-notebook-cell-type.png)

1. Zadejte nebo vložte do buňky následující text nadpisu:

    ```markdown
    # Hello World in Python
    ```

1. Protože upravujete Markdown, text se zobrazí jako záhlaví s "#". Chcete-li markdown vykreslit do html, vyberte tlačítko **Spustit.** Poznámkové bloky Azure pak automaticky vytvoří novou buňku kódu později:

    ![Tlačítko spustit pro buňku a vykreslené Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Do buňky kódu zadejte následující kód Pythonu:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Chcete-li spustit kód, vyberte **spustit** (klávesová zkratka: Shift+Enter). Pod buňkou byste měli vidět úspěšný výstup podobný následujícímu textu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Vyberte ikonu uložit, kterou chcete uložit svou práci:

    ![Ikona Uložit na panelu nástrojů poznámkového bloku Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Výběrem příkazu nabídka**Zavřít a zastavit** **soubor** > zastavíte server a zavřete okno prohlížeče.

## <a name="share-the-notebook"></a>Sdílení poznámkového bloku

Pokud chcete poznámkový blok sdílet, přepněte v případě potřeby zpět na stránku projektu, klikněte pravým tlačítkem myši na soubor poznámkového bloku, vyberte **Kopírovat odkaz** (klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy (e-mail, zasílání zpráv atd.).

Na stránce projektu můžete také použít nabídku **Sdílet** k získání odkazu, vytvoření e-mailové zprávy s odkazem nebo získání kódu pro vložení HTML a Markdownu:

![Sdílení projektu, příkaz](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
