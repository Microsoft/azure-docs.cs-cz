---
title: Naklonujte Poznámkový blok Jupyter z GitHubu pomocí Azure Notebooks
description: Z úložiště GitHubu si rychle naklonujte Jupyter Poznámkový blok a spusťte ho v účtu Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed239c470a49fc045bd436668f83dff32eac3a66
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970122"
---
# <a name="quickstart-clone-a-notebook"></a>Rychlý Start: klonování poznámkového bloku

Mnoho vědeckých pracovníků a vývojářů ukládá své poznámkové bloky do úložišť [GitHub](https://github.com), bezplatnou službu, která poskytuje úložiště a správu verzí pro mnoho různých typů projektů. GitHub se často používá jako prostředek pro spolupráci na poznámkových blocích Jupyter, které se spouštějí místně. V takových případech každý spolupracovníka udržuje místní kopii úložiště a spouští poznámkové bloky z této kopie.

Klonování vytvoří místo toho kopii poznámkového bloku GitHubu v účtu Azure Notebooks. Tento klon je nezávislý na svém původním úložišti; změny se ukládají jenom v účtu Azure Notebooks a neovlivňují původní. Vzhledem k tomu, že je váš klon v cloudu, můžete projekt sdílet s ostatními spolupracovníky, kteří nepotřebují žádné místní kopie nebo dokonce můžou mít Jupyter nainstalované na svých počítačích. Poznámkový blok můžete také klonovat jednoduše jako výchozí bod pro projekt, který vlastníte, nebo k získání datových souborů.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonovat notebooky Azure Cognitive Services

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte tlačítko se šipkou nahoru (Klávesová zkratka: U; tlačítko se zobrazí jako **nahrát úložiště GitHub** , když je okno prohlížeče dostatečně velké):

    ![Nahrání příkazu pro úložiště GitHub na stránce Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. V části **nahrát úložiště GitHub** , které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **importovat**:

   - **Úložiště GitHub**: Microsoft/vnímání-Services-poznámkové bloky (Tento název klonuje Jupyter poznámkové bloky pro Azure Cognitive Services v [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Rekurzivní klonování**: (nezaškrtnuto)
   - **Název projektu**: Cognitive Services klonování
   - **ID projektu**: vnímání – služby – klon
   - **Veřejné**: (nezaškrtnuto)

     ![Místní nabídka nahrávání úložiště GitHubu pro shromáždění informací o úložišti](media/quickstarts/upload-github-repo-popup.png)

1. Být pacient, zatímco se proces dokončí; klonování úložiště může trvat několik minut.

1. Po dokončení klonování Azure Notebooks přejdete k novému projektu, kde vidíte kopie všech souborů.

    [![](media/quickstarts/completed-clone.png#lightbox)(media/quickstarts/completed-clone.png "Zobrazení dokončeného klonu")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Sdílení poznámkového bloku

1. Chcete-li sdílet kopii klonovaného projektu, použijte ovládací prvek **share** nebo získejte odkaz, Získejte kód HTML nebo Markdownu, který obsahuje odkaz, nebo vytvořte e-mailovou zprávu s odkazem:

    ![Sdílení projektu – příkaz](media/quickstarts/share-project-command.png)

1. Vzhledem k tomu, že jste zrušili možnost **Public** při klonování projektu, klon je soukromý. Pokud chcete kopii zveřejnit, vyberte **nastavení projektu**, v místní nabídce nastavte možnost **veřejný projekt** a pak vyberte **Uložit**.

1. Vyberte Poznámkový blok v projektu, který chcete spustit. Každý Poznámkový blok v úložišti Azure Cognitive Services je například svým vlastním samoobslužným rychlým startem. Následující obrázek ukazuje výsledek používání poznámkového bloku BingImageSearchAPI po přidání klíče předplatného rozhraní API Cognitive Services a změně hledaného termínu "Puppies" na "Bunnies":

    ![Spuštění poznámkového bloku Jupyter naklonované z GitHubu](media/quickstarts/clone-notebook-result.png)

1. Po spuštění poznámkového bloku vyberte **soubor** > **Zavřít a zastavit** , aby se Poznámkový blok a jeho okno v prohlížeči zavřelo.

1. Pokud chcete v projektu sdílet jednotlivý Poznámkový blok, klikněte na něj pravým tlačítkem a vyberte **Kopírovat odkaz** (Klávesová zkratka: y):

    ![Příkaz místní nabídky pro zkopírování odkazu do jednotlivého poznámkového bloku](media/quickstarts/copy-link-to-individual-notebook.png)

1. Chcete-li upravit jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte možnost **Upravit soubor** (Klávesová zkratka: i). Výchozí akce, **Run** (Klávesová zkratka: r), zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
