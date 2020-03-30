---
title: Vytvoření projektu náhledu poznámkových bloků Azure s vlastním prostředím
description: Vytvořte nový projekt v náhledu poznámkových bloků Azure, který je nakonfigurovaný s konkrétní sadou nainstalovaných balíčků a spouštěcích skriptů.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196837"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Úvodní příručka: Vytvoření projektu s vlastním prostředím ve verzi Azure Notebooks Preview

Projekt v poznámkových blocích Azure je kolekce souborů, jako jsou poznámkové bloky, datové soubory, dokumentace, image a tak dále, spolu s prostředím, které lze nakonfigurovat pomocí konkrétních příkazů nastavení. Definováním prostředí s projektem, každý, kdo klony projektu do svého vlastního účtu Azure poznámkových bloků má všechny informace, které potřebují k vytvoření potřebné prostředí.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Vytvoření projektu

1. Přejděte na [Poznámkové bloky Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete [v tématu Úvodní příručka – přihlášení k poznámkovým blokům Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na stránce veřejného profilu vyberte **moje projekty** v horní části stránky:

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte + Nový projekt (klávesová **zkratka:** n); tlačítko se může **+** zobrazit pouze tak, jako by bylo okno prohlížeče úzké:

    ![Příkaz Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. Ve vyskakovacím okně **Vytvořit nový projekt,** které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **Vytvořit**:

    - **Název projektu**: Projekt s vlastním prostředím
    - **ID projektu**: vlastní prostředí projektu
    - **Veřejný projekt**: (schváleno)
    - **Vytvoření README.md**: (vymazáno)

1. Po chvíli vás poznámkové bloky Azure přenese k novému projektu. Přidejte poznámkový blok do projektu tak, že vyberete rozevírací rozevírací soubor **+ Nový** (který se může zobrazit pouze **+**) a pak vyberte **Poznámkový blok**.

1. Pojmenujte poznámkový blok jako *Vlastní prostředí.ipynb*, vyberte **python 3.6** pro jazyk a vyberte **Nový**.

## <a name="add-a-custom-setup-step"></a>Přidání vlastního kroku nastavení

1. Na stránce projektu vyberte **Nastavení projektu**.

    ![Příkaz Nastavení projektu](media/quickstarts/project-settings-command.png)

1. V místní části **Nastavení projektu** vyberte kartu **Životní prostředí** a v části Kroky nastavení **prostředí**vyberte **+ Přidat**:

    ![Příkaz pro přidání nového kroku nastavení prostředí](media/quickstarts/environment-add-command.png)

1. Příkaz **+ Add** vytvoří krok, který je definován operací a cílový soubor, který je vybrán ze souborů v projektu. Podporovány jsou následující operace:

   | Operace | Popis |
   | --- | --- |
   | Requirements.txt | Projekty Pythonu definují své závislosti v souboru requirements.txt. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu a také vyberte verzi Pythonu v dalším rozevíracím seznamu, který se zobrazí. V případě potřeby se vyberte **Storno,** chcete-li se vrátit do projektu, nahrát nebo vytvořit soubor, pak se vrátit na kartu**Prostředí** **nastavení** > projektu a vytvořit nový krok. S tímto krokem na místě se automaticky spustí spuštění poznámkového bloku v projektu`pip install -r <file>` |
   | Skript prostředí | Slouží k označení skriptu prostředí bash (obvykle soubor s příponou *.sh),* který obsahuje všechny příkazy, které chcete spustit pro inicializaci prostředí. |
   | Prostředí.yml | Projekt Pythonu, který používá conda pro správu prostředí používá soubor *environments.yml* k popisu závislostí. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu. |

   > [!WARNING]
   > Vzhledem k tomu, že se jedná o službu `Environment.yml` preview ve vývoji, je aktuálně známý problém, kde se nastavení nepoužije na váš projekt podle očekávání. Projekt a poznámkové bloky Jupyter v rámci nenačítají zadaný soubor prostředí v současné době.

1. Chcete-li odebrat jakýkoli krok nastavení, vyberte **x** vpravo od kroku.

1. Po dokončení všech kroků nastavení vyberte **uložit**. (Chcete-li zrušit změny, vyberte možnost **Storno).**

1. Chcete-li otestovat prostředí, vytvořte a spusťte nový poznámkový blok a pak vytvořte `import` buňku kódu s příkazy, které závisí na balíčku v prostředí, například pomocí příkazu Python. Pokud je příkaz úspěšný, byl potřebný balíček úspěšně nainstalován v prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa a konfigurace projektů v poznámkových blocích Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření spuštění notebooku Jupyter dělat lineární regrese](tutorial-create-run-jupyter-notebook.md)
