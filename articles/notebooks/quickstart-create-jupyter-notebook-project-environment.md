---
title: Vytvoření projektu poznámkových bloků Azure s vlastním prostředím
description: Vytvoření nového projektu v poznámkových bloků Azure, který je nakonfigurovaný s konkrétní sadou balíčků nainstalovaných a spouštění skriptů.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: f167d1bf6064cde535d17fa4a336ee13424baba7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277485"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Rychlý start: Vytvoření projektu s vlastním prostředím

Projekt v poznámkových bloků Azure je kolekce souborů, jako jsou poznámkové bloky, datové soubory, dokumentace, obrázky a tak dále, spolu s prostředí, ve kterém můžete nakonfigurovat specifické nastavení příkazy. Definováním prostředí s projektem každý, kdo duplicity projektu do účtu poznámkových bloků Azure má všechny informace, které je nutné znovu vytvořit potřebná prostředí.

## <a name="create-a-project"></a>Vytvoření projektu

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit jenom jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

    - **Název projektu**: projekt s vlastním prostředím
    - **ID projektu**: projekt – vlastní – prostředí
    - **Veřejný projekt**: (nezaškrtnuto)
    - **Vytvořit Readme.MD**: (nezaškrtnuto)

1. Po chvíli se poznámkových bloků Azure přejde do nového projektu. Přidejte do projektu Poznámkový blok tak, že vyberete rozevírací seznam **+ Nový** (může se zobrazit jenom **+** ) a pak vyberete **Poznámkový blok**.

1. Poznámkový blok pojmenujte jako *vlastní prostředí. ipynb*, vyberte **Python 3,6** pro jazyk a vyberte **Nový**.

## <a name="add-a-custom-setup-step"></a>Přidání kroku s vlastním nastavením

1. Na stránce projekt vyberte **nastavení projektu**.

    ![Nastavení projekt – příkaz](media/quickstarts/project-settings-command.png)

1. V místní nabídce **nastavení projektu** vyberte kartu **prostředí** a potom v části **nastavení prostředí**vyberte **+ Přidat**:

    ![Příkaz pro přidání nového kroku nastavení prostředí](media/quickstarts/environment-add-command.png)

1. Příkaz **+ Add** vytvoří krok, který je definován operací a cílovým souborem, který je vybrán ze souborů v projektu. Podporovány jsou následující operace:

    | Operace | Popis |
    | --- | --- |
    | Soubor Requirements.txt | Projekty Pythonu definovat jejich závislosti v souboru requirements.txt. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu a v dalších rozevíracího seznamu, který se zobrazí také vyberte verzi Pythonu. V případě potřeby vyberte **Zrušit** a vraťte se do projektu, nahrajte nebo vytvořte soubor a pak se vraťte na kartu **nastavení projektu** > **prostředí** a vytvořte nový krok. V tomto kroku se automaticky spustí Poznámkový blok v projektu `pip install -r <file>` |
    | Skript prostředí | Použijte k označení skriptu prostředí bash (obvykle souboru s příponou *. sh* ), který obsahuje příkazy, které chcete spustit k inicializaci prostředí. |
    | Environment.yml | Projekt Pythonu, který používá conda ke správě prostředí, používá k popisu závislostí soubor *Environment. yml* . Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů v projektu. |

1. Pokud chcete odebrat nějaký krok nastavení, vyberte **X** napravo od kroku.

1. Až budou všechny kroky instalace na místě, vyberte **Uložit**. (Vyberte **Zrušit** pro zahození změn).

1. Chcete-li otestovat prostředí, vytvořte a spusťte nový Poznámkový blok a pak vytvořte buňku kódu s příkazy, které jsou závislé na balíčku v prostředí, například pomocí příkazu Python `import`. Pokud příkaz bude úspěšné, pak potřebný balíček byl úspěšně nainstalován v prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa a konfigurace projektů v Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
