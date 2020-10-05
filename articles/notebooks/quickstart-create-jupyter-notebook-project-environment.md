---
title: Vytvoření projektu Azure Notebooks ve verzi Preview s vlastním prostředím
description: Vytvoří nový projekt v Azure Notebooks Preview, který je nakonfigurovaný pomocí konkrétní sady nainstalovaných balíčků a spouštěcích skriptů.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 655c016b55abdcf4b6f546a1fe16348ec4c83724
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87853360"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Rychlý Start: vytvoření projektu s vlastním prostředím ve službě Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Projekt v Azure Notebooks je kolekce souborů, například poznámkových bloků, datových souborů, dokumentace, obrázků a tak dále, spolu s prostředím, které lze konfigurovat s konkrétními příkazy instalace. Když definujete prostředí s projektem, všichni uživatelé, kteří naklonují projekt na svůj vlastní Azure Notebooks účet, mají všechny informace, které potřebují k opětovnému vytvoření potřebného prostředí.

## <a name="create-a-project"></a>Vytvoření projektu

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit jenom v **+** případě, že je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

    - **Název projektu**: projekt s vlastním prostředím
    - **ID projektu**: projekt – vlastní – prostředí
    - **Veřejný projekt**: (nezaškrtnuto)
    - **Vytvořit Readme.MD**: (nezaškrtnuto)

1. Po chvíli Azure Notebooks přejít k novému projektu. Přidejte do projektu Poznámkový blok tak, že vyberete rozevírací seznam **+ Nový** (může se zobrazit jenom jako **+** ) a pak vybrat **Poznámkový blok**.

1. Poznámkový blok pojmenujte jako *vlastní prostředí. ipynb*, vyberte **Python 3,6** pro jazyk a vyberte **Nový**.

## <a name="add-a-custom-setup-step"></a>Přidat vlastní krok instalace

1. Na stránce projekt vyberte **nastavení projektu**.

    ![Nastavení projektu – příkaz](media/quickstarts/project-settings-command.png)

1. V místní nabídce **nastavení projektu** vyberte kartu **prostředí** a potom v části **nastavení prostředí**vyberte **+ Přidat**:

    ![Příkaz pro přidání nového kroku nastavení prostředí](media/quickstarts/environment-add-command.png)

1. Příkaz **+ Add** vytvoří krok, který je definován operací a cílovým souborem, který je vybrán ze souborů v projektu. Podporují se tyto operace:

   | Operace | Popis |
   | --- | --- |
   | Requirements.txt | Projekty Pythonu definují své závislosti v souboru requirements.txt. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu a také v rozevíracím seznamu, který se zobrazí, vyberte verzi Pythonu. V případě potřeby vyberte **Zrušit** a vraťte se do projektu, nahrajte nebo vytvořte soubor a pak se vraťte na kartu prostředí **nastavení projektu**  >  **Environment** a vytvořte nový krok. V tomto kroku je spuštěný Poznámkový blok v projektu se automaticky spustí. `pip install -r <file>` |
   | Skript prostředí | Použijte k označení skriptu prostředí bash (obvykle souboru s příponou *. sh* ), který obsahuje příkazy, které chcete spustit k inicializaci prostředí. |
   | Environment. yml | Projekt Pythonu, který používá conda ke správě prostředí, používá k popisu závislostí soubor *Environment. yml* . Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu. |

   > [!WARNING]
   > Vzhledem k tomu, že se jedná o službu ve verzi Preview, se v současnosti jedná o známý problém, kdy se `Environment.yml` nastavení pro váš projekt nepoužije podle očekávání. Projekt a poznámkové bloky Jupyter v v současné době nenačte zadaný soubor prostředí.

1. Pokud chcete odebrat nějaký krok nastavení, vyberte **X** napravo od kroku.

1. Až budou všechny kroky instalace na místě, vyberte **Uložit**. (Vyberte **Zrušit** pro zahození změn).

1. Chcete-li otestovat prostředí, vytvořte a spusťte nový Poznámkový blok a pak vytvořte buňku kódu s příkazy, které jsou závislé na balíčku v prostředí, jako je například použití `import` příkazu Python. Pokud se příkaz úspěšně dokončí, potřebný balíček se úspěšně nainstaloval do prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa a konfigurace projektů v Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
