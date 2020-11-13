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
ms.openlocfilehash: 018a0e3a244d1f0e36ce638d646ab05861c10e38
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565904"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co je Azure Machine Learning Studio?

V tomto článku se dozvíte o Azure Machine Learning studiu, webovém portálu pro vývojáře dat v [Azure Machine Learning](overview-what-is-azure-ml.md). Studio kombinuje prostředí pro datové vědy bez kódu a prvního kódu.

V tomto článku se dozvíte, jak:
>[!div class="checklist"]
> - Jak [vytvářet projekty strojového učení](#author-machine-learning-projects) v studiu.
> - Jak [spravovat prostředky a prostředky](#manage-assets-and-resources) v nástroji Studio.
> - Rozdíly mezi [Azure Machine Learning Studio a ml Studio (Classic)](#ml-studio-classic-vs-azure-machine-learning-studio).


## <a name="author-machine-learning-projects"></a>Vytváření projektů strojového učení

Studio nabízí více prostředí pro tvorbu v závislosti na typu projektu a úrovni uživatelského prostředí.

+ **Poznámkové bloky**

  Pište a spouštějte vlastní kód na spravovaných [Jupyter notebook serverech](how-to-run-jupyter-notebooks.md) , které jsou přímo integrované v studiu. 

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
- Kanály 
- Koncové body kanálu

I v případě, že jste zkušený vývojář, může Studio zjednodušit správu prostředků pracovního prostoru.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (Classic) vs Azure Machine Learning Studio

Vydaná v 2015. první, **ml Studio (Classic)** , byla prvním přetahováním tvůrce machine learningu. Jedná se o samostatnou službu, která nabízí jenom vizuální prostředí. Studio (Classic) spolupracuje s Azure Machine Learning.

**Azure Machine Learning** je samostatná a moderní služba, která poskytuje kompletní datovou platformu pro datové vědy. Podporuje i prostředí s nízkým kódem.

**Azure Machine Learning Studio** je webový portál *v* Azure Machine Learning, který obsahuje možnosti s nízkým kódem a bez kódu pro vytváření projektů a správu prostředků. 

Pro nejnovější škálu nástrojů pro datové vědy doporučujeme, aby si pro poslední rozsah nástrojů pro datové vědy zvolili možnost **Azure Machine Learning** místo ml Studio (Classic).

### <a name="feature-comparison"></a>Porovnání funkcí

Následující tabulka shrnuje hlavní rozdíly mezi ML Studio (Classic) a Azure Machine Learning.

| Příznak | ML Studio (klasický) | Azure Machine Learning |
|---| --- | --- |
| Rozhraní přetažení | Klasické prostředí | Aktualizované prostředí – [návrhář Azure Machine Learning](concept-designer.md)| 
| Sady SDK kódu | Nepodporované | Plně integrovaná s [Azure Machine Learning Python](/python/api/overview/azure/ml/) a [R](tutorial-1st-r-experiment.md) SDK |
| Experiment | Škálovatelné (limit pro školicí data z 10 GB) | Škálování s cílem výpočetního prostředí |
| Školení výpočetních cílů | Proprietární výpočetní cíl, jenom podpora procesoru | Široké spektrum přizpůsobitelných [výpočetních cílů](concept-compute-target.md#train). Zahrnuje podporu GPU a procesoru. | 
| Cíle výpočtů nasazení | Speciální formát webové služby, není přizpůsobitelný | Široké škály přizpůsobitelných [výpočetních cílů nasazení](concept-compute-target.md#deploy). Zahrnuje podporu GPU a procesoru. |
| Kanál ML | Nepodporováno | Vytváření flexibilních modulárních [kanálů](concept-ml-pipelines.md) pro automatizaci pracovních postupů |
| MLOps | Základní Správa modelů a nasazení; Nasazení pouze procesoru | Správa verzí entit (model, data, pracovní postupy), automatizace pracovních postupů, integrace s nástroji pro CICD, nasazení procesoru a GPU [a další](concept-model-management-and-deployment.md) |
| Formát modelu | Speciální formát, jenom Studio (Classic) | Více podporovaných formátů v závislosti na typu úlohy školení |
| Automatizované školení modelů a ladění parametrů |  Nepodporováno | [Podporuje](concept-automated-ml.md)se. Možnosti Code-First a No-Code. | 
| Detekce posunu dat | Nepodporováno | [Podporováno](how-to-monitor-datasets.md) |
| Projekty označování dat | Nepodporováno | [Podporováno](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>Další kroky

Navštivte [Studio](https://ml.azure.com)nebo si prozkoumejte různé možnosti vytváření těchto kurzů:  

- + [Začněte ve vlastním vývojovém prostředí](tutorial-1st-experiment-sdk-setup-local.md)
  + [Použití poznámkových bloků Jupyter na výpočetní instanci ke školení & nasazení modelů](tutorial-1st-experiment-sdk-setup.md)
  + [Použití automatizovaného strojového učení k výuce & nasazení modelů](tutorial-first-experiment-automated-ml.md)  
  + [Použití návrháře ke školení & nasazení modelů](tutorial-designer-automobile-price-train-score.md)
  + [Použití studia v zabezpečené virtuální síti](how-to-enable-studio-virtual-network.md)