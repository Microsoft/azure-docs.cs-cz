---
title: Co je Azure Machine Learning Studio?
description: Studio je webový portál pro Azure Machine Learning pracovní prostory. Studio kombinuje prostředí pro datové vědy bez kódu a prvního kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
adobe-target: true
ms.openlocfilehash: 48c4b2a73628ab2105e23054d747e28acc105d01
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563180"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co je Azure Machine Learning Studio?

V tomto článku se dozvíte o Azure Machine Learning studiu, webovém portálu pro vývojáře dat v [Azure Machine Learning](overview-what-is-azure-ml.md). Studio kombinuje prostředí pro datové vědy bez kódu a prvního kódu.

V tomto článku se dozvíte, jak:
>[!div class="checklist"]
> - Jak [vytvářet projekty strojového učení](#author-machine-learning-projects) v studiu.
> - Jak [spravovat prostředky a prostředky](#manage-assets-and-resources) v nástroji Studio.
> - Rozdíly mezi [Azure Machine Learning Studio a ml Studio (Classic)](#ml-studio-classic-vs-azure-machine-learning-studio).

Doporučujeme používat nejnovější prohlížeč, který je kompatibilní s vaším operačním systémem. Podporovány jsou následující prohlížeče:
  * Microsoft Edge (nová Microsoft Edge, nejnovější verze Ne Microsoft Edge starší verze)
  * Safari (nejnovější verze, jen Mac)
  * Chrome (nejnovější verze)
  * Firefox (nejnovější verze)

## <a name="author-machine-learning-projects"></a>Vytváření projektů strojového učení

Studio nabízí více prostředí pro tvorbu v závislosti na typu projektu a úrovni uživatelského prostředí.

+ **Poznámkové bloky**

  Pište a spouštějte vlastní kód na spravovaných [Jupyter notebook serverech](how-to-run-jupyter-notebooks.md) , které jsou přímo integrované v studiu. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Snímek obrazovky: zápis a spuštění kódu v poznámkovém bloku":::

+ **Návrhář služby Azure Machine Learning**

  Použijte návrháře ke školení a nasazení modelů strojového učení bez psaní kódu. Přetáhnutím datových sad a modulů vytvořte kanály ML. Vyzkoušejte si [kurz pro návrháře](tutorial-designer-automobile-price-train-score.md).

    ![Příklad návrháře Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Uživatelské rozhraní automatizovaného strojového učení**

  Naučte se vytvářet [automatizované experimenty ml](tutorial-first-experiment-automated-ml.md) pomocí snadno použitelného rozhraní. 

  [![Navigační podokno Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Popisky dat**

    Použijte [Azure Machine Learning popisky dat](how-to-create-labeling-projects.md) , abyste efektivně koordinovali popisky dat v projektech.

## <a name="manage-assets-and-resources"></a>Správa prostředků a prostředků

Spravujte své prostředky strojového učení přímo v prohlížeči. Prostředky jsou sdíleny ve stejném pracovním prostoru mezi sadou SDK a sadou Studio, aby bylo zajištěno bezproblémové prostředí. Použijte Studio ke správě:

- Modely
- Datové sady
- Úložiště dat
- Výpočetní prostředky
- Notebooks
- Experimenty
- Spustit protokoly
- Pipelines 
- Koncové body kanálu

I v případě, že jste zkušený vývojář, může Studio zjednodušit správu prostředků pracovního prostoru.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (Classic) vs Azure Machine Learning Studio

Vydaná v 2015. první, **ml Studio (Classic)** , byla prvním přetahováním tvůrce machine learningu. 

**Ml Studio (Classic)** je samostatná služba, která nabízí jenom vizuální prostředí. Studio (Classic) spolupracuje s Azure Machine Learning.

**Azure Machine Learning** je samostatná a moderní služba, která poskytuje kompletní datovou platformu pro datové vědy. Podporuje i prostředí s nízkým kódem.

**Azure Machine Learning Studio** je webový portál *v* Azure Machine Learning, který obsahuje možnosti s nízkým kódem a bez kódu pro vytváření projektů a správu prostředků. 

Pro nejnovější škálu nástrojů pro datové vědy doporučujeme, aby si pro poslední rozsah nástrojů pro datové vědy zvolili možnost **Azure Machine Learning** místo ml Studio (Classic). Pokud jste stávající uživatel ML Studio (klasický), zvažte možnost [migrace na Azure Machine Learning](classic/migrate-overview.md).

Zde jsou některé výhody přepínání na Azure Machine Learning:

- Škálovatelné výpočetní clustery pro velké školení.
- Podnikové zabezpečení a zásady správného řízení.
- Vzájemná spolupráce s oblíbenými open source nástroji.
- Koncová MLOps.

### <a name="feature-comparison"></a>Porovnání funkcí

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Řešení potíží

* **Chybějící položky uživatelského rozhraní v studiu** Řízení přístupu na základě role v Azure je možné použít k omezení akcí, které můžete provádět s Azure Machine Learning. Tato omezení můžou zabránit tomu, aby se položky uživatelského rozhraní zobrazovaly v Azure Machine Learning Studiu. Například pokud máte přiřazenou roli, která nemůže vytvořit výpočetní instanci, možnost vytvoření instance COMPUTE se v studiu nezobrazí. Další informace najdete v tématu [Správa uživatelů a rolí](how-to-assign-roles.md).

## <a name="next-steps"></a>Další kroky

Navštivte [Studio](https://ml.azure.com)nebo si prozkoumejte různé možnosti vytváření těchto kurzů:  

- + [Začněte ve vlastním vývojovém prostředí](tutorial-1st-experiment-sdk-setup-local.md)
  + [Použití poznámkových bloků Jupyter na výpočetní instanci ke školení & nasazení modelů](tutorial-1st-experiment-sdk-setup.md)
  + [Použití automatizovaného strojového učení k výuce & nasazení modelů](tutorial-first-experiment-automated-ml.md)  
  + [Použití návrháře ke školení & nasazení modelů](tutorial-designer-automobile-price-train-score.md)
  + [Použití studia v zabezpečené virtuální síti](how-to-enable-studio-virtual-network.md)