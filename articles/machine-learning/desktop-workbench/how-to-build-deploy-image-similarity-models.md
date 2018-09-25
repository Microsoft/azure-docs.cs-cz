---
title: Sestavení a nasazení modelu podobnosti image pomocí balíčku Azure Machine Learning pro počítačové zpracování obrazu.
description: Zjistěte, jak sestavit, trénování, testování a nasazování modelem počítačového zpracování obrazu image podobnosti pomocí balíčku Azure Machine Learning pro počítačové zpracování obrazu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 371903e1ee080d2b98fd46ac4d6d9838416e1335
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973418"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Vytváření a nasazování modelů podobnosti image pomocí služby Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


V tomto článku najdete další informace o použití Azure Machine Learning balíčku pro zpracování obrazu počítače (AMLPCV) pro trénování, testování a nasazení modelu podobnosti bitové kopie. Přehled o tento balíček a jeho podrobnou referenční dokumentaci, naleznete v tématu https://aka.ms/aml-packages/vision.

Velký počet problémů v doméně počítače pro zpracování obrazu můžete vyřešit pomocí bitové kopie podobnosti. Tyto problémy patří vytváření modelů, které odpovídají otázky jako:
+ _OBJEKT je k dispozici v bitové kopii? Například "pes", "auto", "dodání" a tak dále_
+ _Jaké třídy závažnost stádiu oka je evinced podle této pacienta retinal kontrolu?_

Při vytváření a nasazování tento model s AMLPCV, můžete přejít pomocí následujících kroků:
1. Vytvoření datové sady
2. Obrázek vizualizace a poznámky
3. Image rozšíření
4. Definice modelu hluboké Neuronové sítě (DNN)
5. Třídění školení
6. Vyhodnocení a vizualizace
7. Nasazení webové služby
8. Webové služby zátěžové testování

[CNTK](https://www.microsoft.com/cognitive-toolkit/) slouží jako rozhraní hloubkového učení školení se provádí místně na počítači s využitím GPU, jako ([hloubkového učení virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), a nasazení pomocí rozhraní příkazového řádku Azure ML Operacionalizace.

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Následující účty a aplikace musí mít nastavení a instalaci:
   - Účet Experimentování ve službě Azure Machine Learning 
   - Účet správy modelů Azure Machine Learning
   - Nainstalovanou aplikaci Azure Machine Learning Workbench

   Pokud tyto tři jsou ještě vytvořen nebo nainstalován, postupujte [Quickstart pro Azure Machine Learning a Workbench instalace](../desktop-workbench/quickstart-installation.md) článku. 

1. Musíte nainstalovat balíček Azure Machine Learning pro počítačové zpracování obrazu. Zjistěte, jak nainstalovat tento balíček, jak je popsáno v https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Ukázková data a poznámkového bloku

### <a name="get-the-jupyter-notebook"></a>Získání poznámkového bloku Jupyter

Stáhnout ho na spuštění ukázky popsané sami.

> [!div class="nextstepaction"]
> [Získání poznámkového bloku Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Načtení ukázkových dat.

Následující příklad používá datovou sadu skládající se z 63 stolní imagí. Každé image je označen jako patřící do jednoho z čtyři různé třídy (bowl, cup, příbory, talíře). Počet obrázků v tomto příkladu je malá tak, aby tato ukázka je možné provést rychle. V praxi musí být zadána nejméně 100 obrázků na třídy. Všechny bitové kopie jsou umístěny na *".. /sample_data/imgs_recycling / "*, v podadresářích nazývá"bowl","cupu","příbory"a"štítku".

![Datová sada Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>Další postup

Další informace o balíčku pro Azure Machine Learning pro počítačové zpracování obrazu v těchto článcích:

+ Zjistěte, jak [zlepšit přesnost tohoto modelu](how-to-improve-accuracy-for-computer-vision-models.md).

+ Přečtěte si [balíček přehled](https://aka.ms/aml-packages/vision).

+ Prozkoumejte [referenční dokumentaci](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) pro tento balíček.

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md).
