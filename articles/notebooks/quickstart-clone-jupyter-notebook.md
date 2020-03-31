---
title: Klonování poznámkového bloku Jupyter z GitHubu pomocí náhledu poznámkových bloků Azure
description: Rychle naklonujte poznámkový blok Jupyter z úložiště GitHub a spusťte ho ve svém účtu Poznámkových bloků Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064593"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Úvodní příručka: Klonování poznámkového bloku v náhledu poznámkových bloků Azure

V tomto rychlém startu zkopírujete poznámkový blok Jupyter uložený na GitHubu do účtu poznámkových bloků Azure. 

Úložiště GitHub poskytují úložiště a správu verzí pro notebooky Jupyter. Spolupracovníci udržují místní kopie úložišť a spouštějí poznámkové bloky z těchto kopií. Klonování poznámkového bloku Jupyter z GitHubu do účtu Azure Notebooks vytvoří nezávislou kopii poznámkového bloku. Změny se ukládají jenom ve vašem účtu Poznámkových bloků Azure a nemají vliv na původní úložiště GitHub. 

Vzhledem k tomu, že klonování poznámkových bloků Azure je v cloudu, můžete je sdílet se spolupracovníky, kteří nemusí vytvářet žádné místní kopie nebo mají na svých počítačích nainstalovaný Jupyter. Můžete také klonovat poznámkový blok jednoduše jako výchozí bod pro vlastní projekt nebo získat datové soubory. 

## <a name="prerequisites"></a>Požadavky
Žádné.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonování poznámkových bloků služby Azure Cognitive Services

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Přejděte na [Poznámkové bloky Azure](https://notebooks.azure.com) a přihlaste se. Podrobnosti najdete [v tématu Úvodní příručka – Přihlášení k poznámkovým blokům Azure](quickstart-sign-in-azure-notebooks.md).

1. Na stránce veřejného profilu vyberte **moje projekty** v horní části stránky:

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte tlačítko šipky nahoru (klávesová zkratka: U; tlačítko se zobrazí jako **Upload GitHub Repo,** když je dostatečně široké okno prohlížeče):

    ![Příkaz Odeslat úložiště GitHub na stránce Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. V **uploadu úložiště GitHub,** které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **Importovat**:

   - **Úložiště GitHub**: Microsoft/cognitive-services-notebooks (tento název klonuje poznámkové bloky [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)Jupyter pro Služby Azure Cognitive Services na).
   - **Klonrekurzálně**: (vymazáno)
   - **Název projektu**: Cognitive Services Clone
   - **ID projektu**: kognitivní služby-klon
   - **Veřejné**: (vymazáno)

     ![Nahrát GitHub Repo popup shromažďovat informace o úložišti](media/quickstarts/upload-github-repo-popup.png)

1. Buďte trpěliví, zatímco proces dokončí; klonování úložiště může trvat několik minut.

1. Po dokončení klonování, Poznámkové bloky Azure přejdete do nového projektu, kde můžete zobrazit kopie všech souborů.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Sdílení poznámkového bloku

1. Chcete-li sdílet kopii klonovaného projektu, použijte ovládací prvek **Sdílet** nebo získat odkaz, získat kód HTML nebo Markdown, který obsahuje odkaz, nebo vytvořit e-mailovou zprávu s odkazem:

    ![Sdílení projektu, příkaz](media/quickstarts/share-project-command.png)

1. Protože jste při klonování projektu zrušili možnost **Veřejné,** klon je soukromý. Chcete-li, aby byla vaše kopie veřejná, vyberte **Nastavení projektu**, nastavte možnost **Veřejný projekt** ve vyskakovacím okně a pak vyberte **Uložit**.

1. Vyberte poznámkový blok v projektu, který chcete spustit. Každý poznámkový blok v úložišti Azure Cognitive Services, například je vlastní samostatný rychlý start. Obrázek níže ukazuje výsledek použití notebooku BingImageSearchAPI, po přidání klíče předplatného rozhraní API cognitive services a změně vyhledávacího výrazu "štěňata" na "zajíčci":

    ![Spuštění notebooku Jupyter naklonovaného z GitHubu](media/quickstarts/clone-notebook-result.png)

1. Po dokončení spuštění poznámkového bloku vyberte **Zavřít soubor** > **a zavřete** ho a zavřete okno prohlížeče.

1. Pokud chcete sdílet jednotlivé poznámkové bloky v projektu, klikněte pravým tlačítkem myši na poznámkový blok a vyberte **Kopírovat odkaz** (klávesová zkratka: y):

    ![Příkaz kontextové nabídky pro kopírování odkazu na jednotlivý poznámkový blok](media/quickstarts/copy-link-to-individual-notebook.png)

1. Pokud chcete upravovat jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte **Upravit soubor** (klávesová zkratka: i). Výchozí akce **Spustit** (klávesová zkratka: r) zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
