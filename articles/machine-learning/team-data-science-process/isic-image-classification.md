---
title: Obrázek klasifikaci s Azure Machine Learning balíček pro počítač vize a tým datové vědy procesu (TDSP) | Microsoft Docs
description: Popisuje použití tým datové vědy procesu (TDSP) a balíček Azure Machine Learning pro počítač výhled pro klasifikaci bitové kopie.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300786"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Vzhledu rakoviny klasifikaci bitové kopie s Azure Machine Learning balíček pro počítač vize a proces vědecké účely Team dat

V tomto článku se dozvíte, jak používat [balíček Azure Machine Learning pro počítač vize](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) ke cvičení, testování a nasazení *bitové kopie klasifikace* modelu. Příklad používá struktura tým datové vědy procesu (TDSP) a šablon v nástroji [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Tento názorný postup obsahuje je kompletní ukázka. Použije [kognitivní nástrojů Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) jako přímý učení framework a cvičení se provádí na [vědecké zpracování dat virtuálního počítače](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) grafický procesor počítače. Nasazení používá Azure Machine Learning operationalization rozhraní příkazového řádku.

Mnoho aplikací v doméně vize počítače může být ohraničeny jako obrázek klasifikaci problémy. Mezi ně patří vytváření modelů, zodpovědět jednoduché otázky typu "je přítomen v bitové kopii objektu?" kde objekt může být PSA, auta nebo příjemce. Také obsahuje odpovědi na otázky složitější, jako například "jaké třída závažnosti nákazy oko je evinced retinal kontrolou tento pacienta?" Balíček Azure Machine Learning pro počítač vize zjednodušuje zpracování dat klasifikace bitové kopie a modelování kanálu. 

## <a name="link-to-the-github-repository"></a>Propojit s úložišti GitHub
Tento článek je souhrnný dokument o vzorku. Rozsáhlejší dokumentaci můžete najít na [Githubu lokality](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Návod proces vědecké účely dat Team

Tento návod používá [proces vědecké účely dat Team](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) životního cyklu. Průvodce zahrnuje následující kroky životního cyklu.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Získávání dat](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Datová sada mezinárodní vzhledu Imaging spolupráce (ISIC) se používá pro úlohu klasifikace bitové kopie. ISIC je partnerství mezi vysokými školami a průmyslem k usnadnění provádění digitální vzhledu imaging prozkoumat a snížit nemelanomové úmrtnosti. [ISIC archivu](https://isic-archive.com/#images) obsahuje více než 13 000 bitových kopií vzhledu změn, které jsou označeny jako buď či nezhoubné. Stáhněte si ukázku bitové kopie z ISIC archivu.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelování](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
V kroku modelování jsou prováděny následující dílčích kroků.

#### <a name="dataset-creation"></a>Vytvoření datové sady

Generovat objekt datovou sadu v Azure Machine Learning balíčku pro počítač vize tím, že poskytuje kořenový adresář bitových kopií na místním disku. 

#### <a name="image-visualization-and-annotation"></a>Vizualizace bitové kopie a poznámky

Vizualizace bitové kopie v objektu datové sady a opravte popisky podle potřeby.

#### <a name="image-augmentation"></a>Rozšíření bitové kopie

Použití transformací, které jsou popsány v posílení objekt datové sady [imgaug](https://github.com/aleju/imgaug) knihovny.

#### <a name="dnn-model-definition"></a>Definice DNN modelu

Definujte Architektura modelu, která se používá v kroku školení. Šest předem trénované modely hluboké neuronové sítě jsou podporovány v Azure Machine Learning balíčku pro počítač vizi: AlexNet, Resnet 18, Resnet 34, Resnet – 50, Resnet 101 a Resnet 152.

#### <a name="classifier-training"></a>Školení třídění

Cvičení neuronové sítě s výchozí nebo vlastní parametry.

#### <a name="evaluation-and-visualization"></a>Vyhodnocení a vizualizaci

Poskytují funkce k vyhodnocení výkonu pro cvičný model v datové sadě služby nezávislé testu. Vyhodnocení metriky zahrnují přesnost, přesnost, odvolání a křivka ROC.

Dílčích kroků jsou podrobně vysvětleny v odpovídající poznámkového bloku Jupyter. Poznámkového bloku také obsahuje pokyny pro vypnutí parametry, jako je například rychlost učení, velikost dávky mini a rychlost Odpadlík dále zlepšit výkon modelu.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. nasazení](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Tento krok operationalizes modelu, která je vytvořena z kroku modelování. Zavádí požadavky a požadované instalace. Také vysvětluje spotřeby webovou službu. V tomto kurzu zjistíte vytvářet modely hloubkové learning s Azure Machine Learning balíčkem pro počítač vize a zprovoznit model v Azure.

## <a name="next-steps"></a>Další postup
- Přečtěte si další dokumentaci o [balíček Azure Machine Learning pro počítač vize](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Pro čtení [proces vědecké účely dat Team](https://aka.ms/tdsp) dokumentaci, abyste mohli začít.


## <a name="references"></a>Odkazy

* [Balíček Azure Machine Learning pro vize počítače](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Datové vědy virtuálního počítače](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

