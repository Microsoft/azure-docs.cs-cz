---
title: Vytvoření a sdílení Jupyter Notebook v Azure Notebooks Preview
description: V Azure Notebooks Preview můžete rychle vytvořit a spustit Jupyter Notebook a pak tento poznámkový blok sdílet s ostatními.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88589329"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Rychlý Start: vytvoření a sdílení poznámkového bloku v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

V tomto rychlém startu vytvoříte a spustíte Jupyter Notebook v Azure Notebooks a pak tento poznámkový blok nasdílíte s ostatními. Jupyter umožňuje snadno zkombinovat Markdownu text, spustitelný kód, trvalá data, grafiku a vizualizace na jednom plátně, poznámkovém bloku. Azure Notebooks je bezplatná hostované služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu bez instalace.

## <a name="prerequisites"></a>Předpoklady
Žádné

## <a name="create-a-new-project-and-notebook"></a>Vytvořit nový projekt a Poznámkový blok

1. Přejít na [web Azure Notebooks ( https://notebooks.azure.com) ](https://notebooks.azure.com) a přihlaste se. Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n). Tlačítko se může zobrazit jenom v **+** případě, že je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

   - **Název projektu**: Hello World v Pythonu
   - **ID projektu**: Hello-World – Python
   - **Veřejný projekt**: (nezaškrtnuto)
   - **Vytvořit Readme.MD**: (nezaškrtnuto)

     ![Místní nabídka nový projekt s vyplněnými podrobnostmi](media/quickstarts/new-project-popup.png)

1. Po chvíli Azure Notebooks přejít k novému projektu. Přidejte do projektu Poznámkový blok tak, že vyberete rozevírací seznam **+ Nový** (může se objevit jenom jako **+** ) a pak vybrat **Poznámkový blok**:

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Nový, prázdný projekt a příkaz Přidat Poznámkový blok." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. V automaticky otevřeném okně **vytvořit nový Poznámkový blok** zadejte název souboru pro svůj Poznámkový blok, například *HelloWorldInPython. ipynb* (*. ipynb* označuje notebook ironpythonu (Jupyter)) a vyberte **Python 3,6** pro jazyk (také označovaný jako *jádro*):

    ![Místní nabídka pro vytvoření nového poznámkového bloku](media/quickstarts/new-notebook-popup.png)

1. Když vyberete **Nový** , dokončí se vytváření poznámkového bloku, který se pak zobrazí v seznamu souborů vašeho projektu:

    ![Nový Poznámkový blok se zobrazuje v seznamu souborů projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

1. Vyberte nový Poznámkový blok, který chcete spustit v editoru. vybrané jádro (v tomto příkladu Python 3,6) se automaticky aktivuje pro tento poznámkový blok:

    ![Zobrazení nového poznámkového bloku v Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Ve výchozím nastavení má Poznámkový blok jednu prázdnou buňku s kódem. Chcete-li změnit typ buňky na **Markdownu**, použijte rozevírací seznam typ buňky k výběru **Markdownu**:

    ![Změna typu buňky v novém poznámkovém bloku](media/quickstarts/create-notebook-cell-type.png)

1. Do buňky zadejte nebo vložte následující text nadpisu:

    ```markdown
    # Hello World in Python
    ```

1. Vzhledem k tomu, že upravujete Markdownu, text se zobrazí jako záhlaví s znakem "#". Pokud chcete Markdownu vykreslit do HTML, vyberte tlačítko **Spustit** . Azure Notebooks pak automaticky vytvoří novou buňku kódu následně:

    ![Tlačítko spustit pro buňku a vykreslený Markdownu](media/quickstarts/run-cell-markdown-render.png)

1. V buňce Code (kód) zadejte následující kód Pythonu:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Pro spuštění kódu vyberte **Spustit** (Klávesová zkratka: SHIFT + ENTER). Pod buňkou byste měli vidět úspěšný výstup podobný následujícímu textu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Kliknutím na ikonu Uložit uložte svou práci:

    ![Ikona uložit na panelu nástrojů Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Výběrem **File**  >  příkazu nabídky**Zavřít a zastavit** soubor zastavte Server a zavřete okno prohlížeče.

## <a name="share-the-notebook"></a>Sdílet Poznámkový blok

Chcete-li sdílet svůj Poznámkový blok, v případě potřeby přepněte zpět na stránku projektu, klikněte pravým tlačítkem na soubor poznámkového bloku, vyberte **Kopírovat odkaz** (Klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy (E-mail, im atd.).

Na stránce projekt můžete také pomocí nabídky **sdílet** získat odkaz, vytvořit e-mailovou zprávu s odkazem nebo získat kód pro vložení HTML a Markdownu:

![Sdílení projektu – příkaz](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření a spuštění Jupyter Notebook k provedení lineární regrese](tutorial-create-run-jupyter-notebook.md)
