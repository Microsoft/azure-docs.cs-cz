---
title: Vytvoření projektu Azure Notebooks s vlastním prostředím
description: Vytvoří nový projekt v Azure Notebooks, který je nakonfigurovaný pomocí konkrétní sady nainstalovaných balíčků a spouštěcích skriptů.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6ec0d2fae285889a731e9b881ea90f15b96c85f2
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973074"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Rychlý Start: vytvoření projektu s vlastním prostředím

Projekt v Azure Notebooks je kolekce souborů, například poznámkových bloků, datových souborů, dokumentace, obrázků a tak dále, spolu s prostředím, které lze konfigurovat s konkrétními příkazy instalace. Když definujete prostředí s projektem, všichni uživatelé, kteří naklonují projekt na svůj vlastní Azure Notebooks účet, mají všechny informace, které potřebují k opětovnému vytvoření potřebného prostředí.

## <a name="create-a-project"></a>Vytvoření projektu

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit pouze jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

    - **Název projektu**: projekt s vlastním prostředím
    - **ID projektu**: projekt – vlastní – prostředí
    - **Veřejný projekt**: (nezaškrtnuto)
    - **Vytvořit Readme.MD**: (nezaškrtnuto)

1. Po chvíli Azure Notebooks přejít k novému projektu. Přidejte do projektu Poznámkový blok tak, že vyberete rozevírací seznam **+ Nový** (může se zobrazit jenom **+** ) a pak vybrat **Poznámkový blok**.

1. Poznámkový blok pojmenujte jako *vlastní prostředí. ipynb*, vyberte **Python 3,6** pro jazyk a vyberte **Nový**.

## <a name="add-a-custom-setup-step"></a>Přidat vlastní krok instalace

1. Na stránce projekt vyberte **nastavení projektu**.

    ![Nastavení projektu – příkaz](media/quickstarts/project-settings-command.png)

1. V místní nabídce **nastavení projektu** vyberte kartu **prostředí** a potom v části **nastavení prostředí**vyberte **+ Přidat**:

    ![Příkaz pro přidání nového kroku nastavení prostředí](media/quickstarts/environment-add-command.png)

1. Příkaz **+ Add** vytvoří krok, který je definován operací a cílovým souborem, který je vybrán ze souborů v projektu. Podporují se tyto operace:

    | Operace | Popis |
    | --- | --- |
    | Požadavky. txt | Projekty Pythonu definují své závislosti v souboru. txt požadavků. Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu a také v rozevíracím seznamu, který se zobrazí, vyberte verzi Pythonu. V případě potřeby vyberte **Zrušit** a vraťte se do projektu, nahrajte nebo vytvořte soubor a pak se vraťte na kartu**prostředí** **nastavení projektu** >  a vytvořte nový krok. V tomto kroku je spuštěný Poznámkový blok v projektu se automaticky spustí `pip install -r <file>`. |
    | Skript prostředí | Použijte k označení skriptu prostředí bash (obvykle souboru s příponou *. sh* ), který obsahuje příkazy, které chcete spustit k inicializaci prostředí. |
    | Environment. yml | Projekt Pythonu, který používá conda ke správě prostředí, používá k popisu závislostí soubor *Environment. yml* . Pomocí této možnosti vyberte příslušný soubor ze seznamu souborů projektu. |

1. Pokud chcete odebrat nějaký krok nastavení, vyberte **X** napravo od kroku.

1. Až budou všechny kroky instalace na místě, vyberte **Uložit**. (Vyberte **Zrušit** pro zahození změn).

1. Chcete-li otestovat prostředí, vytvořte a spusťte nový Poznámkový blok a pak vytvořte buňku kódu s příkazy, které jsou závislé na balíčku v prostředí, například pomocí příkazu Python `import`. Pokud se příkaz úspěšně dokončí, potřebný balíček se úspěšně nainstaloval do prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa a konfigurace projektů v Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
