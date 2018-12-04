---
title: Vytvoření projektu poznámkových bloků Azure s vlastním prostředím | Dokumentace Microsoftu
description: Vytvoření nového projektu v poznámkových bloků Azure, který je nakonfigurovaný s konkrétní sadou balíčků nainstalovaných a spouštění skriptů.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 48579f797d0ff94a86fdbb25fa1fc814f63d7788
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855892"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Rychlý start: Vytvoření projektu s vlastním prostředím

Projekt v poznámkových bloků Azure je kolekce souborů, jako jsou poznámkové bloky, datové soubory, dokumentace, obrázky a tak dále, spolu s prostředí, ve kterém můžete nakonfigurovat specifické nastavení příkazy. Definováním prostředí s projektem každý, kdo duplicity projektu do účtu poznámkových bloků Azure má všechny informace, které je nutné znovu vytvořit potřebná prostředí.

## <a name="create-a-project"></a>Vytvoření projektu

1. Přejděte na [poznámkových bloků Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete v tématu [rychlý start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce svého veřejného profilu vyberte **projekty** v horní části stránky:

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na **projekty** stránce **+ nový projekt** (Klávesová zkratka: n); na tlačítko se může objevit pouze jako **+** li úzké okno prohlížeče:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V **vytvořit nový projekt** automaticky otevírané okno, které se zobrazí, zadejte nebo nastavte tyto údaje a pak vyberte **vytvořit**:

    - **Název projektu**: projekt s vlastním prostředím
    - **ID projektu**: prostředí vlastní projektu
    - **Veřejné projektu**: (zaškrtnuto)
    - **Vytvoření README.md**: (zaškrtnuto)

1. Po chvíli se poznámkových bloků Azure přejde do nového projektu. Poznámkového bloku přidejte do projektu tak, že vyberete **+ nový** rozevíracího seznamu (který může zobrazit jako pouze **+**), pak vyberete **Poznámkový blok**.

1. Zadejte název jako je Poznámkový blok *environment.ipynb vlastní*vyberte **Python 3.6** pro jazyk a vyberte **nový**.

## <a name="add-a-custom-setup-step"></a>Přidání kroku s vlastním nastavením

1. Na stránce projektu vyberte **nastavení projektu**.

    ![Nastavení projekt – příkaz](media/quickstarts/project-settings-command.png)

1. V **nastavení projektu** automaticky otevírané okno, vyberte **prostředí** kartu, pak v oblasti **pokynů k instalaci prostředí**vyberte **+ přidat**:

    ![Příkaz pro přidání nového kroku nastavení prostředí](media/quickstarts/environment-add-command.png)

1. **+ Přidat** příkaz vytvoří krok, který je definován tak, že operace a cílový soubor, který je vybrán ze souborů ve vašem projektu. Podporovány jsou následující operace:

    | Operace | Popis |
    | --- | --- |
    | Soubor Requirements.txt | Projekty Pythonu definovat jejich závislosti v souboru requirements.txt. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu a v dalších rozevíracího seznamu, který se zobrazí také vyberte verzi Pythonu. V případě potřeby vyberte **zrušit** vraťte se do projektu, nahrání nebo vytvoření souboru a potom vrátit **nastavení projektu** > **prostředí** kartu a vytvořit nový krok. Spuštění poznámkového bloku v projektu automaticky spustí s tímto krokem na místě `pip install -r <file>` |
    | Skript prostředí | Používá se k označení skript prostředí bash (obvykle soubor s *.sh* rozšíření), která obsahuje všechny příkazy, které chcete spustit inicializace prostředí. |
    | Environment.yml | Projekt Python, který používá systém conda pro správu prostředí používá *environments.yml* souboru k popsání závislostí. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů v projektu. |

1. Chcete-li odebrat libovolný krok instalace, vyberte **X** napravo od kroku.

1. Až všechny kroky v místě, vyberte **Uložit**. (Vyberte **zrušit** pro zrušení změn).

1. Pro testovací prostředí, vytvořte a spusťte nový poznámkový blok a pak vytvořte buňku kódu s příkazy, které závisí na balíčku v prostředí, například pomocí Python `import` příkazu. Pokud příkaz bude úspěšné, pak potřebný balíček byl úspěšně nainstalován v prostředí.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa a konfigurace projektů v poznámkových bloků Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
