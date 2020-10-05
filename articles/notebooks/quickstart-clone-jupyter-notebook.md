---
title: Klonování Jupyter poznámkového bloku z GitHubu s využitím Azure Notebooks Preview
description: Z úložiště GitHubu si rychle naklonujte Jupyter Poznámkový blok a spusťte ho v účtu Azure Notebooks.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424473"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Rychlý Start: naklonování poznámkového bloku v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

V tomto rychlém startu zkopírujete Poznámkový blok Jupyter uložený v GitHubu do účtu Azure Notebooks. 

Úložiště GitHub poskytují úložiště a správu verzí pro poznámkové bloky Jupyter. Spolupracovníci udržují místní kopie úložišť a z těchto kopií spouštějí poznámkové bloky. Klonování Jupyter poznámkového bloku z GitHubu do účtu Azure Notebooks vytvoří nezávislou kopii poznámkového bloku. Změny se ukládají jenom v účtu Azure Notebooks a neovlivňují původní úložiště GitHub. 

Vzhledem k tomu, že je váš Azure Notebooks klonem v cloudu, můžete ho sdílet s spolupracovníky, kteří na svém počítači nemusejí dělat žádné místní kopie nebo mít Jupyter nainstalované. Poznámkový blok můžete také klonovat jednoduše jako výchozí bod pro projekt, který vlastníte, nebo pro získání datových souborů. 

## <a name="prerequisites"></a>Předpoklady
Žádné

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonovat notebooky Azure Cognitive Services

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte tlačítko se šipkou nahoru (Klávesová zkratka: U; tlačítko se zobrazí jako **nahrát úložiště GitHub** , když je okno prohlížeče dostatečně velké):

    ![Nahrání příkazu pro úložiště GitHub na stránce Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. V části **nahrát úložiště GitHub** , které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **importovat**:

   - **Úložiště GitHub**: Microsoft/vnímání-Services – poznámkové bloky (Tento název klonuje Jupyter poznámkové bloky pro Azure Cognitive Services v [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) ).
   - **Rekurzivní klonování**: (nezaškrtnuto)
   - **Název projektu**: Cognitive Services klonování
   - **ID projektu**: vnímání – služby – klon
   - **Veřejné**: (nezaškrtnuto)

     ![Místní nabídka nahrávání úložiště GitHubu pro shromáždění informací o úložišti](media/quickstarts/upload-github-repo-popup.png)

1. Být pacient, zatímco se proces dokončí; klonování úložiště může trvat několik minut.

1. Po dokončení klonování Azure Notebooks přejdete k novému projektu, kde vidíte kopie všech souborů.

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="Zobrazení dokončeného klonu." lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>Sdílení poznámkového bloku

1. Chcete-li sdílet kopii klonovaného projektu, použijte ovládací prvek **share** nebo získejte odkaz, Získejte kód HTML nebo Markdownu, který obsahuje odkaz, nebo vytvořte e-mailovou zprávu s odkazem:

    ![Sdílení projektu – příkaz](media/quickstarts/share-project-command.png)

1. Vzhledem k tomu, že jste zrušili možnost **Public** při klonování projektu, klon je soukromý. Pokud chcete kopii zveřejnit, vyberte **nastavení projektu**, v místní nabídce nastavte možnost **veřejný projekt** a pak vyberte **Uložit**.

1. Vyberte Poznámkový blok v projektu, který chcete spustit. Každý Poznámkový blok v úložišti Azure Cognitive Services je například svým vlastním samoobslužným rychlým startem. Následující obrázek ukazuje výsledek používání poznámkového bloku BingImageSearchAPI po přidání klíče předplatného rozhraní API Cognitive Services a změně hledaného termínu "Puppies" na "Bunnies":

    ![Spuštění poznámkového bloku Jupyter naklonované z GitHubu](media/quickstarts/clone-notebook-result.png)

1. Až budete Poznámkový blok hotovi, vyberte **soubor**  >  **Zavřít a zastavit** a zavřete Poznámkový blok a okno prohlížeče.

1. Pokud chcete v projektu sdílet jednotlivý Poznámkový blok, klikněte na něj pravým tlačítkem a vyberte **Kopírovat odkaz** (Klávesová zkratka: y):

    ![Příkaz místní nabídky pro zkopírování odkazu do jednotlivého poznámkového bloku](media/quickstarts/copy-link-to-individual-notebook.png)

1. Chcete-li upravit jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte možnost **Upravit soubor** (Klávesová zkratka: i). Výchozí akce, **Run** (Klávesová zkratka: r), zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
