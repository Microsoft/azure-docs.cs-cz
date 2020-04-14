---
title: 'Kurz: Vytvoření prvního experimentu ML'
titleSuffix: Azure Machine Learning
description: V tomto kurzu můžete začít s sadou Python SDK Azure Machine Learning, která běží v poznámkových blocích Jupyteru.  V části 1 vytvoříte pracovní prostor, ve kterém budete spravovat experimenty a modely ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 820332b0692c0c863ed23912fe9913c419769155
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272997"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Kurz: Začínáme vytvářet první experiment ML pomocí sady Python SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu dokončíte komplexní kroky, abyste mohli začít s sadou Python SDK Azure Machine Learning, která běží v poznámkových blocích Jupyter. Tento kurz je **součástí první série dvou částí kurzu**a zahrnuje nastavení a konfiguraci prostředí Pythonu a také vytváří pracovní prostor pro správu experimentů a modelů strojového učení. [**Část dvě**](tutorial-1st-experiment-sdk-train.md) staví na tomto trénovat více modelů strojového učení a zavést proces správy modelu pomocí Azure Machine Learning studio a SDK.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte [pracovní prostor Azure Machine Learning,](concept-workspace.md) který se použije v dalším kurzu.
> * Naklonujte poznámkový blok kurzů do složky v pracovním prostoru.
> * Vytvořte cloudovou výpočetní instanci s nainstalovanou a předkonfigurovanou sadou Python SDK azure machine learningu.


Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning je základní prostředek v cloudu, který používáte k experimentování, trénování a nasazování modelů strojového učení. Vazby předplatného Azure a skupiny prostředků na snadno spotřebované objektve službě. 

Pracovní prostor vytvoříte prostřednictvím portálu Azure, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si pracovní **prostor** a **předplatné**. Budete je potřebovat, abyste zajistili vytvoření experimentu na správném místě. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Spuštění poznámkového bloku v pracovním prostoru

Tento kurz používá cloudový notebook ový server ve vašem pracovním prostoru pro prostředí bez instalace a předkonfigurované. Pokud dáváte přednost kontrole nad prostředím, balíčky a závislostmi, použijte [vlastní prostředí.](how-to-configure-environment.md#local)

Postupujte podle tohoto videa nebo použijte níže uvedené podrobné kroky ke klonování a spuštění kurzu z pracovního prostoru. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Klonování složky poznámkového bloku

Můžete dokončit následující experiment set-up a spustit kroky v Azure Machine Learning studio, konsolidované rozhraní, které zahrnuje nástroje strojového učení k provádění scénářů datové vědy pro odborníky datové vědy všech úrovní dovedností.

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com/).

1. Vyberte předplatné a pracovní prostor, který jste vytvořili.

1. Vlevo vyberte **Poznámkové bloky.**

1. Otevřete složku **Ukázky.**

1. Otevřete složku **Pythonu.**

1. Otevřete složku s číslem verze.  Toto číslo představuje aktuální verzi sady Python SDK.

1. Vyberte **"..."** vpravo od složky **výukové** programy a pak vyberte **Klonovat**.

    ![Složka Klonování](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Zobrazí se seznam složek zobrazující každého uživatele, který přistupuje k pracovnímu prostoru.  Vyberte složku a naklonujte složku **výukových programů.**

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">Otevření klonovaného poznámkového bloku

1. V části **Uživatelské soubory** otevřete složku a potom otevřete složku klonovaných **výukových programů.**

    ![Otevřít složku výukových programů](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Poznámkové bloky můžete zobrazit ve složce **ukázky,** ale nelze spustit poznámkový blok odtud.  Chcete-li spustit poznámkový blok, otevřete klonoci verze poznámkového bloku v části **Uživatelské soubory.**
    
1. Vyberte soubor **tutorial-1st-experiment-sdk-train.ipynb** ve složce **tutorials/create-first-ml-experiment.**

1. Na horním panelu vyberte výpočetní instanci, která se má použít ke spuštění poznámkového bloku. Tyto virtuální počítače jsou předem nakonfigurované se [vším, co potřebujete ke spuštění Azure Machine Learning](concept-compute-instance.md#contents). 

1. Pokud se nenajdou žádné virtuální počítače, vyberte **+ Přidat** k vytvoření virtuálního počítače výpočetní instance. 

    1. Při vytváření virtuálního virtuálního_  Název musí být mezi 2 až 16 znaky. Platné znaky jsou písmena, číslice a znak - a musí být také jedinečné v rámci předplatného Azure.

    1.  Z dostupných voleb vyberte velikost virtuálního počítače.

    1. Pak vyberte **Vytvořit**. Nastavení virtuálního počítače může trvat přibližně 5 minut.

1. Jakmile je virtuální virtuální ms k dispozici, zobrazí se v horním panelu nástrojů.  Nyní můžete poznámkový blok spustit buď pomocí **spuštění vše** v panelu nástrojů, nebo pomocí **Shift+Enter** v buňkách kódu poznámkového bloku.

Pokud máte vlastní widgety nebo dáváte přednost použití Jupyter / JupyterLab vyberte **Jupyter** drop down na pravé straně, pak vyberte **Jupyter** nebo **JupyterLab**. Otevře se nové okno prohlížeče.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto úkoly:

* Vytvořil pracovní prostor Azure Machine Learning.
* Vytvoření a konfigurace cloudového notebookového serveru v pracovním prostoru.

V **druhé části** kurzu spustíte `tutorial-1st-experiment-sdk-train.ipynb` kód v trénovat model strojového učení. 

> [!div class="nextstepaction"]
> [Výuka: Trénujte svůj první model](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Pokud nemáte v plánu na následující části 2 tohoto kurzu nebo jiné kurzy, měli byste [zastavit cloud notebook server VM,](tutorial-1st-experiment-sdk-train.md#clean-up-resources) když nejste pomocí ke snížení nákladů.


