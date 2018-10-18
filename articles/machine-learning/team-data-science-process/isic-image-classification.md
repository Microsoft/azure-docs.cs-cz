---
title: Klasifikace obrázků s balíček Azure Machine Learning pro počítačové zpracování obrazu a vědecké zpracování týmových dat (TDSP) | Dokumentace Microsoftu
description: Popisuje použití vědecké zpracování týmových dat (TDSP) a balíček Azure Machine Learning pro počítačové zpracování obrazu pro klasifikace obrázků.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: ee2e797f3838b8b6b36174d14c73e97fe9790315
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392808"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Vzhledu rakoviny klasifikace obrázků s balíček Azure Machine Learning pro počítačové zpracování obrazu a vědecké zpracování týmových dat

V tomto článku se dozvíte, jak používat [balíček Azure Machine Learning pro počítačové zpracování obrazu](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) pro trénování, testování a nasazení *klasifikace obrázků* modelu. Ukázka používá strukturu vědecké zpracování týmových dat (TDSP) a šablon v [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation). Tento názorný postup obsahuje úplnou ukázku. Používá [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) jako hloubkového učení rozhraní framework a školení, které se provádí na [virtuálního počítače pro datové vědy](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) počítače GPU. Nasazení používá Azure Machine Learning operacionalizaci rozhraní příkazového řádku.

Mnoho aplikací v doméně počítače pro zpracování obrazu může uvedeny jako image klasifikace problémy. Mezi ně patří vytváření modelů, zodpovědět jednoduché otázky typu "je k dispozici v bitové kopii objektu?" Pokud objekt může být pes, Auto nebo příjemce. Zahrnuje také odpovědi na složitější otázky, jako například "jaké třída závažnosti stádiu oka je evinced podle této pacienta retinal kontroly?" Balíček Azure Machine Learning pro počítačové zpracování obrazu zjednodušuje zpracování dat klasifikace obrázků a modelování kanálu. 

## <a name="link-to-the-github-repository"></a>Propojit úložiště GitHub
Tento článek je souhrnný dokument o ukázku. Více si rozsáhlou dokumentaci k můžete najít na [webu GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Názorný postup týmu vědecké zpracování dat

Tento návod používá [vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) životního cyklu. Návod se zabývá následujícími kroky životního cyklu.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Získání dat](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Datová sada mezinárodní vzhledu Imaging spolupráce (ISIC) se používá pro úlohy klasifikace obrázků. Karty ISIC je partnerství mezi průmyslu pro usnadnění používání digitální vzhledu imaging studovat a pomoct snížit nemelanomové úmrtnosti a akademické sféře. [ISIC archivu](https://isic-archive.com/#images) obsahuje více než 13 000 bitových kopií vzhledu změn, které jsou označeny jako buď či nezhoubné. Stáhněte ukázku z imagí z karty ISIC archivu.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelování](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
V kroku modelování jsou prováděny následující dílčí kroky.

#### <a name="dataset-creation"></a>Vytvoření datové sady

Generovat objekt dataset v balíčku Azure Machine Learning pro počítačové zpracování obrazu poskytnutím kořenový adresář bitové kopie na místním disku. 

#### <a name="image-visualization-and-annotation"></a>Obrázek vizualizace a poznámky

Vizualizujte obrázky v objektu dataset a opravte popisků podle potřeby.

#### <a name="image-augmentation"></a>Image rozšíření

Rozšířit pomocí transformace, které jsou popsány v objektu dataset [imgaug](https://github.com/aleju/imgaug) knihovny.

#### <a name="dnn-model-definition"></a>Definice modelu DNN

Definujte Architektura modelu, který se používá v kroku školení. Šest předem natrénovaných modelů hluboké neuronové sítě jsou podporovány v balíčku Azure Machine Learning pro počítačové zpracování obrazu: AlexNet modelem Resnet-18, modelem Resnet-34, modelem Resnet-50, modelem Resnet-101 a modelem Resnet-152.

#### <a name="classifier-training"></a>Třídění školení

Trénování neuronové sítě pomocí výchozí nebo vlastní parametry.

#### <a name="evaluation-and-visualization"></a>Vyhodnocení a vizualizace

Poskytuje funkce pro vyhodnocení výkonu trénovaného modelu na datové sadě služby nezávislé testu. Vyhodnocení metriky zahrnují správností, přesností, odvolání a křivky roc s více TŘÍDAMI.

Dílčích kroků jednotlivých je podrobně popsaný v odpovídající poznámkového bloku Jupyter. Poznámkový blok také obsahuje pokyny pro zapnutí parametry, jako je například rychlost učení, velikost dávky mini a rychlost Odpadlík můžete ještě zlepšit výkon modelů.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Nasazení](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Tento krok operationalizes modelu, který je vytvořen z kroku modelování. Zavádí požadavky a požadované instalace. Je také jsou vysvětlené využití webové služby. V tomto kurzu se naučíte vytvářet modely obsáhlého learningu s balíček Azure Machine Learning pro počítačové zpracování obrazu a zprovoznění modelu v Azure.

## <a name="next-steps"></a>Další postup
- Přečtěte si další dokumentaci o [balíček Azure Machine Learning pro počítačové zpracování obrazu](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Přečtěte si [vědecké zpracování týmových dat](https://aka.ms/tdsp) dokumentaci, abyste mohli začít.


## <a name="references"></a>Odkazy

* [Balíček Azure Machine Learning pro počítačové zpracování obrazu](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation)
* [Virtuální počítač pro datové vědy](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

