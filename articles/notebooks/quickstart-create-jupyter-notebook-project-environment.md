---
title: Vytvoření projektu Azure Notebooks ve verzi Preview s vlastním prostředím
description: Vytvoří nový projekt v Azure Notebooks Preview, který je nakonfigurovaný pomocí konkrétní sady nainstalovaných balíčků a spouštěcích skriptů.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 999133dd7d9d792956f9a2c93ec218e458c921e8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647063"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Rychlý Start: vytvoření projektu s vlastním prostředím ve službě Azure Notebooks Preview

Projekt v poznámkových bloků Azure je kolekce souborů, jako jsou poznámkové bloky, datové soubory, dokumentace, obrázky a tak dále, spolu s prostředí, ve kterém můžete nakonfigurovat specifické nastavení příkazy. Definováním prostředí s projektem každý, kdo duplicity projektu do účtu poznámkových bloků Azure má všechny informace, které je nutné znovu vytvořit potřebná prostředí.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

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

1. Po chvíli se poznámkových bloků Azure přejde do nového projektu. Poznámkového bloku přidejte do projektu tak, že vyberete **+ nový** rozevíracího seznamu (který může zobrazit jako pouze **+** ), pak vyberete **Poznámkový blok**.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa a konfigurace projektů v poznámkových bloků Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
