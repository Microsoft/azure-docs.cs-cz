---
title: Klonovat poznámkového bloku Jupyter z Githubu s poznámkovými bloky Azure
description: Rychle klonovat poznámkového bloku Jupyter z úložiště GitHub a spusťte jej ve svém účtu poznámkových bloků Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 500bd3f85409bb2f5e7b73be0478694695cc9c00
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277528"
---
# <a name="quickstart-clone-a-notebook"></a>Rychlý start: Klonování poznámkového bloku

Mnoho datových vědců a vývojářů ukládat v jejich poznámkové bloky [úložišť GitHub](https://github.com)je bezplatná služba, která poskytuje úložiště a správou verzí pro mnoho různých typech projektů. GitHub se často používá jako způsob spolupráce v poznámkových blocích Jupyter, které běží místně. V takovém případě každý spolupracovníka udržuje místní kopie úložiště a spustí poznámkových bloků v kopii.

Klonování vytvoří kopii Poznámkový blok Githubu ve vašem účtu poznámkových bloků Azure místo. Tenhle klon je nezávislá z jeho původní úložiště; změny se ukládají v účtu Azure poznámkových bloků a nemají vliv na původní. Protože vaše klonování je v cloudu, můžete sdílet projekt s další spolupracovníci, kteří nemusí provést žádné místní kopie nebo dokonce mít Jupyter ve svých počítačích nainstalován. Jednoduše jako výchozí bod pro projekt vlastní nebo k získání datových souborů může také klonovat poznámkového bloku.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonovat poznámkových bloků Azure Cognitive Services

1. Přejděte na [poznámkových bloků Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete v tématu [rychlý start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce svého veřejného profilu vyberte **projekty** v horní části stránky:

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte tlačítko se šipkou nahoru (Klávesová zkratka: U; tlačítko se zobrazí jako **nahrát úložiště GitHub** , když je okno prohlížeče dostatečně velké):

    ![Nahrání příkazu pro úložiště GitHub na stránce Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. V části **nahrát úložiště GitHub** , které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **importovat**:

   - **Úložiště GitHub**: Microsoft/vnímání-Services-poznámkové bloky (Tento název klonuje Jupyter poznámkové bloky pro Azure Cognitive Services v [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klonovat rekurzivně**: (zaškrtnuto)
   - **Název projektu**: klonování služby Cognitive Services
   - **ID projektu**: cognitive services klonování
   - **Veřejné**: (zaškrtnuto)

     ![Místní nabídka nahrávání úložiště GitHubu pro shromáždění informací o úložišti](media/quickstarts/upload-github-repo-popup.png)

1. Při dokončení procesu; buďte prosím trpěliví. klonování úložiště může trvat několik minut.

1. Po dokončení klonování poznámkových bloků Azure přejdete do nového projektu ve kterém uvidíte zkopíruje všechny soubory.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Sdílení poznámkového bloku

1. Chcete-li sdílet vaše kopie klonovaný projektu, použijte **sdílet** řídit získat odkaz, získat kód HTML nebo Markdown, který obsahuje odkaz nebo vytvořit e-mailovou zprávu s odkazem:

    ![Sdílet projekt – příkaz](media/quickstarts/share-project-command.png)

1. Protože jste zrušili **veřejné** možnost při klonování projektu, klonování je privátní. Chcete-li zveřejnit vaše kopie, vyberte **nastavení projektu**, nastavte **veřejného projektu** a potom vyberte možnost v místní nabídce **Uložit**.

1. Vyberte poznámkového bloku v projektu a spustíme ji. Každý poznámkového bloku v úložišti Azure Cognitive Services je například svůj vlastní samostatný rychlý start. Následující obrázek ukazuje výsledek použití poznámkového bloku BingImageSearchAPI, po přidání předplatného klíče rozhraní API služeb Cognitive Services a změnu hledaný termín "Štěňata" na "bunnies":

    ![Spuštění poznámkového bloku Jupyter naklonovali z Githubu](media/quickstarts/clone-notebook-result.png)

1. Po dokončení spuštění poznámkového bloku vyberte **souboru** > **zavřít a zastavit** zavřete poznámkový blok a jeho okno prohlížeče.

1. Ke sdílení jednotlivých poznámkového bloku v projektu, klikněte pravým tlačítkem na Poznámkový blok a vyberte **Kopírovat odkaz** (Klávesová zkratka: y):

    ![Příkaz místní nabídky zkopírovat odkaz do jednotlivých poznámkového bloku](media/quickstarts/copy-link-to-individual-notebook.png)

1. Chcete-li upravit soubory jiné než poznámkových bloků, klikněte pravým tlačítkem na soubor v projektu a vyberte **upravit soubor** (Klávesová zkratka: Mohu). Výchozí akce **spustit** (Klávesová zkratka: r), pouze zobrazuje obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
