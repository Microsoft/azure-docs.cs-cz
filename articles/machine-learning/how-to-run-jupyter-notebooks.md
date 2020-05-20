---
title: Spouštění poznámkových bloků Jupyter ve vlastním pracovním prostoru
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spustit Jupyter Notebook, aniž byste opustili pracovní prostor v Azure Machine Learning Studiu.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 96e37afd8bf7d59eef4a4c0c831f535faa36d34d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681439"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Spouštění poznámkových bloků Jupyter ve vlastním pracovním prostoru
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Přečtěte si, jak spustit poznámkové bloky Jupyter přímo ve vašem pracovním prostoru v Azure Machine Learning Studiu. I když můžete spustit [Jupyter](https://jupyter.org/) nebo [JupyterLab](https://jupyterlab.readthedocs.io), můžete také upravit a spustit poznámkové bloky, aniž byste museli opustit pracovní prostor.

Podívejte se, jak můžete:

* Vytvoření poznámkových bloků Jupyter v pracovním prostoru
* Spuštění experimentu z poznámkového bloku
* Změna prostředí poznámkového bloku
* Vyhledání podrobností o výpočetních instancích použitých ke spouštění vašich poznámkových bloků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://aka.ms/AMLFree), ještě než začnete.
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Vytvoření poznámkových bloků

V pracovním prostoru Azure Machine Learning vytvořte nový Poznámkový blok Jupyter a začněte pracovat. Nově vytvořený Poznámkový blok je uložený ve výchozím úložišti pracovního prostoru. Tento Poznámkový blok můžete sdílet s kýmkoli, kdo má přístup k pracovnímu prostoru. 

Vytvoření nového poznámkového bloku: 

1. Otevřete pracovní prostor v [Azure Machine Learning Studiu](https://ml.azure.com).
1. Na levé straně vyberte **poznámkové bloky**. 
1. Vyberte ikonu **vytvořit nový soubor** nad seznamem **uživatelských souborů** v části **Moje soubory** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file":::

1. Pojmenujte soubor. 
1. Pro Jupyter Notebook soubory vyberte jako typ souboru **Poznámkový blok Python** .
1. Vyberte adresář souborů.
1. Vyberte **Vytvořit**.

> [!TIP]
> Můžete také vytvořit textové soubory.  Jako typ souboru vyberte **text** a přidejte rozšíření k názvu (například MyFile.py nebo MyFile. txt).  

Pomocí nástrojů v horní části stránky poznámkových bloků můžete také nahrávat složky a soubory, včetně poznámkových bloků.  Poznámkové bloky a většina typů textových souborů se zobrazí v sekci Preview.  Pro většinu ostatních typů souborů není dostupná žádná verze Preview.

### <a name="clone-samples"></a>Klonovat ukázky

Váš pracovní prostor obsahuje složku **ukázek** s poznámkami, které jsou navržené tak, aby vám pomohly prozkoumat sadu SDK a sloužit jako příklady pro vlastní projekty machine learningu.  Tyto poznámkové bloky můžete klonovat do vlastní složky v kontejneru úložiště pracovního prostoru.  

Příklad najdete v tématu [kurz: vytvoření prvního experimentu ml](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a>Použít soubory z Gitu a verze moje soubory

Ke všem operacím Git můžete přistupovat pomocí okna terminálu. Všechny soubory a složky Git budou uložené v systému souborů pracovního prostoru.

> [!NOTE]
> Přidejte své soubory a složky kamkoli do složky **~/CloudFiles/Code/Users** , aby se zobrazily ve všech prostředích Jupyter.

Přístup k terminálu:

1. Otevřete pracovní prostor v [Azure Machine Learning Studiu](https://ml.azure.com).
1. Na levé straně vyberte **poznámkové bloky**.
1. Vyberte libovolný Poznámkový blok umístěný v části **soubory uživatelů** na levé straně.  Pokud tam nemáte žádné poznámkové bloky, [vytvořte nejdřív Poznámkový blok](#create) .
1. Vyberte **výpočetní** cíl nebo vytvořte nový a počkejte, než se spustí.
1. Vyberte ikonu **otevřeného terminálu** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Otevření terminálu":::

1. Pokud ikonu nevidíte, vyberte **...** napravo od cíle výpočtů a pak vyberte **otevřít terminál** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Otevřít terminál z...":::


Přečtěte si další informace o [klonování úložišť Git do systému souborů pracovního prostoru](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Sdílení poznámkových bloků a dalších souborů

Zkopírujte a vložte adresu URL pro sdílení poznámkového bloku nebo souboru.  K této adrese URL budou mít přístup jenom jiní uživatelé pracovního prostoru.  Přečtěte si další informace o tom, jak [udělit přístup k pracovnímu prostoru](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Úprava poznámkového bloku

Pokud chcete upravit Poznámkový blok, otevřete libovolný Poznámkový blok umístěný v části **uživatelské soubory** v pracovním prostoru. Klikněte na buňku, kterou chcete upravit. 

Když je spuštěná výpočetní instance, můžete také použít dokončování kódu využívající [technologii IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)v jakémkoli poznámkovém bloku Pythonu.

Na panelu nástrojů poznámkového bloku můžete také spustit Jupyter nebo JupyterLab.  Azure Machine Learning neposkytuje aktualizace a opravují chyby z Jupyter nebo JupyterLab, protože se jedná o open source produkty mimo hranici podpora Microsoftu.

### <a name="useful-keyboard-shortcuts"></a>Užitečné klávesové zkratky

|Klávesnice  |Akce  |
|---------|---------|
|Shift+Enter     |  Spuštění buňky       |
|CTRL + M (Windows)     |  Povolí nebo zakáže soutisk karty v poznámkovém bloku.       |
|CTRL + SHIFT + M (Mac & Linux)     |    Povolí nebo zakáže soutisk karty v poznámkovém bloku.     |
|TAB (při zapnutém depeši tabulátorem) | Přidat znak \t (odsazení)
|TAB (když je zakázané přesahy tabulátoru) | Změní fokus na další položku s fokusem (Odstranit tlačítko buňky, tlačítko spustit atd.).

## <a name="delete-a-notebook"></a>Odstranění poznámkového bloku

*Nemůžete* odstranit poznámkové bloky **ukázek** .  Tyto poznámkové bloky jsou součástí studia a aktualizují se při každém publikování nové sady SDK.  

Poznámkové bloky **uživatelských souborů** *můžete* odstranit některým z těchto způsobů:

* V nástroji Studio vyberte **...** na konci složky nebo souboru.  Ujistěte se, že používáte podporovaný prohlížeč (Microsoft Edge, Chrome nebo Firefox).
* Z libovolného panelu nástrojů poznámkového bloku vyberte [**otevřít terminál**](#terminal) pro přístup k oknu terminálu pro instanci Compute.
* V Jupyter nebo JupyterLab s jejich nástroji.

## <a name="run-an-experiment"></a>Spuštění experimentu

Chcete-li spustit experiment z poznámkového bloku, nejprve se připojíte ke spuštěné [výpočetní instanci](concept-compute-instance.md). Pokud nemáte výpočetní instanci, vytvořte ji pomocí těchto kroků: 

1. Vyberte **+** na panelu nástrojů Poznámkový blok. 
2. Pojmenujte výpočetní prostředky a vyberte **Velikost virtuálního počítače**. 
3. Vyberte **Vytvořit**.
4. Instance služby COMPUTE se automaticky připojí k poznámkovému bloku a vy teď můžete spouštět své buňky.

Pouze můžete zobrazit a použít výpočetní instance, které vytvoříte.  Vaše **uživatelské soubory** jsou uložené odděleně od virtuálního počítače a sdílí se mezi všemi výpočetními instancemi v pracovním prostoru.

### <a name="view-logs-and-output"></a>Zobrazit protokoly a výstup

Pomocí [widgetů poznámkového bloku](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) můžete zobrazit průběh běhu a protokolů. Pomůcka je asynchronní a poskytuje aktualizace až do dokončení školení. Pomůcky Azure Machine Learning jsou také podporovány v Jupyter a JupterLab.

## <a name="change-the-notebook-environment"></a>Změna prostředí poznámkového bloku

Panel nástrojů Poznámkový blok umožňuje změnit prostředí, na kterém Poznámkový blok běží.  

Tyto akce nezmění stav poznámkového bloku ani hodnoty jakýchkoli proměnných v poznámkovém bloku:

|Akce  |Výsledek  |
|---------|---------| --------|
|Zastavit jádro     |  Zastaví všechny spuštěné buňky. Při spuštění buňky se jádro automaticky restartuje. |
|Přejít do jiného oddílu pracovního prostoru     |     Běžící buňky jsou zastaveny. |

Tyto akce resetují stav poznámkového bloku a obnoví všechny proměnné v poznámkovém bloku.

|Akce  |Výsledek  |
|---------|---------| --------|
| Změna jádra | Poznámkový blok používá nové jádro |
| Přepnout výpočetní prostředky    |     Poznámkový blok automaticky používá nové výpočetní prostředky. |
| Resetovat výpočetní výkon | Spustí se znovu při pokusu o spuštění buňky. |
| Zastavit výpočetní výkon     |    Nespustí se žádné buňky.  |
| Otevření poznámkového bloku v Jupyter nebo JupyterLab     |    Poznámkový blok se otevřel na nové kartě.  |

### <a name="add-new-kernels"></a>Přidat nové jádra

Poznámkový blok automaticky vyhledá všechny jádra Jupyter nainstalované na připojené výpočetní instanci.  Přidání jádra do výpočetní instance:

1. Na panelu nástrojů poznámkového bloku vyberte [**otevřít terminál**](#terminal) .
1. Pomocí okna terminálu vytvořte nové prostředí.
1. Aktivujte prostředí.  Například po vytvoření `newenv` :

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Můžete nainstalovat kterýkoli z [dostupných jader Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

### <a name="status-indicators"></a>Indikátory stavu

Indikátor vedle rozevíracího seznamu **COMPUTE** zobrazuje jeho stav.  Stav je zobrazen také v rozevíracím seznamu.  

|Barva |Stav výpočtů |
|---------|---------| 
| Green | Spuštěná výpočetní prostředí |
| Červený |Výpočet se nezdařil | 
| Black | Služba COMPUTE byla zastavena |
|  Světle modrá |Výpočetní prostředí pro vytváření, spouštění, restartování a nastavování |
|  Les |Výpočetní odstranění, zastavení |

Indikátor vedle rozevíracího seznamu **jádro** zobrazuje jeho stav.

|Barva |Stav jádra |
|---------|---------|
|  Green |Připojení jádra, nečinné, zaneprázdněné|
|  Les |Jádro není připojeno. |

## <a name="find-compute-details"></a>Najít podrobnosti o COMPUTE 

Podrobnosti o vašich výpočetních instancích najdete na stránce **COMPUTE** v [studiu](https://ml.azure.com).

## <a name="next-steps"></a>Další kroky

* [Spuštění prvního experimentu](tutorial-1st-experiment-sdk-train.md)
* [Zálohování úložiště souborů pomocí snímků](../storage/files/storage-snapshots-files.md)
