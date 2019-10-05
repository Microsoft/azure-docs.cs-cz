---
title: Vytvoření a sdílení poznámkového bloku Jupyter v Azure
description: Rychle vytvořte a spusťte Poznámkový blok Jupyter v Azure Notebooks a potom tento poznámkový blok sdílejte s ostatními.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 19e8217bf88427c353fdd4c90c9f421445b7c81a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970178"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Rychlý Start: vytvoření a sdílení poznámkového bloku

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit pouze jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

   - **Název projektu**: Hello World v Pythonu
   - **ID projektu**: Hello-World – Python
   - **Veřejný projekt**: (nezaškrtnuto)
   - **Vytvořit Readme.MD**: (nezaškrtnuto)

     ![Místní nabídka nový projekt s vyplněnými podrobnostmi](media/quickstarts/new-project-popup.png)

1. Po chvíli Azure Notebooks přejít k novému projektu. Přidejte do projektu Poznámkový blok tak, že vyberete rozevírací seznam **+ Nový** (může se zobrazit jenom **+** ) a pak vybrat **Poznámkový blok**:

    [![](media/quickstarts/empty-project-new-notebook-button.png#lightbox)(media/quickstarts/empty-project-new-notebook-button.png "Nový, prázdný projekt a příkaz Přidat Poznámkový blok")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. V automaticky otevřeném okně **vytvořit nový Poznámkový blok** zadejte název souboru pro svůj Poznámkový blok, například *HelloWorldInPython. ipynb* ( *. ipynb* označuje notebook ironpythonu (Jupyter)) a vyberte **Python 3,6** pro jazyk (také se mu říká *jádro*):

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

    ![Ikona uložit na panelu nástrojů Jupyter Poznámkový blok](media/quickstarts/hello-results-save-icon.png)

1. Vyberte **soubor** > **Zavřít a zastavit** příkaz nabídky k zastavení serveru a zavření okna prohlížeče.

## <a name="share-the-notebook"></a>Sdílet Poznámkový blok

Chcete-li sdílet svůj Poznámkový blok, v případě potřeby přepněte zpět na stránku projektu, klikněte pravým tlačítkem na soubor poznámkového bloku, vyberte **Kopírovat odkaz** (Klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy (E-mail, im atd.).

Na stránce projekt můžete také pomocí nabídky **sdílet** získat odkaz, vytvořit e-mailovou zprávu s odkazem nebo získat kód pro vložení HTML a Markdownu:

![Sdílení projektu – příkaz](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
