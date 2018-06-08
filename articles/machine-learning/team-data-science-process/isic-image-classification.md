---
title: Klasifikace bitové kopie s balíčkem Azure Machine Learning (AML) pro počítač vize (AMLPCV) a proces vědecké účely dat Team (TDSP) | Microsoft Docs
description: Popisuje použití TDSP (Team datové vědy proces) a AMLPCV pro klasifikaci bitové kopie
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
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837214"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Vzhledu rakoviny Image klasifikaci s balíček Azure Machine Learning (AML) pro počítač vize (AMLPCV) a Team datové vědy procesu (TDSP)

## <a name="introduction"></a>Úvod

Tento článek ukazuje, jak používat [Azure Machine Learning balíčku pro vize počítače (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) ke cvičení, testování a nasazení **bitové kopie klasifikace** modelu. Příklad používá TDSP struktura a šablon v nástroji [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Tento názorný postup je součástí je kompletní ukázka. Používá [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) jako přímý učení framework a cvičení se provádí na [datové vědy VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) grafický procesor počítače. Nasazení používá rozhraní příkazového řádku Azure ML Operationalization.

Mnoho aplikací v doméně vize počítače může být ohraničeny jako obrázek klasifikaci problémy. Mezi ně patří vytváření modelů, že odpovědi na dotazy, jako "je přítomen v bitové kopii objektu?" (objekt může být *PSA*, *car*, nebo *dodávat*) a složitější otázky typu "jaký třída závažnosti nákazy oka je evinced retinal kontrolou tento pacienta?" AMLPCV zjednodušuje zpracování dat klasifikace bitové kopie a modelování kanálu. 

## <a name="link-to-github-repository"></a>Propojit s úložišti GitHub
Poskytujeme souhrnné dokumentaci o vzorku. Více rozsáhlou dokumentaci můžete najít na [Githubu lokality](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Tým datové vědy procesu (TDSP) návod s AMLPCV

Tento návod používá [tým datové vědy procesu (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) životního cyklu.

Průvodce obsahuje následující kroky životního cyklu:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Acquision dat](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
ISIC datová sada se používá pro úlohu klasifikace bitové kopie. ISIC (mezinárodní vzhledu vytvoření bitové kopie spolupráce) je partership mezi vysokými školami a průmyslem k usnadnění provádění digitální vzhledu imaging prozkoumat a snížit nemelanomové úmrtnosti. [ISIC archivu](https://isic-archive.com/#images) obsahuje více než 13 000 bitových kopií vzhledu změn s popisky buď či nezhoubné. Jsme ukázku bitové kopie stahovat z ISIC archivu.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelování](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
V kroku modelování jsou prováděny následující dílčích kroků. 

<b>2.1 Vytvoření datové sady</b><br>

Aby bylo možné generovat datovou sadu objektu v AMLPCV, poskytovat kořenový adresář bitových kopií na místním disku. 

<b>2.2 obrázku vizualizace a poznámky</b><br>

Vizualizace bitové kopie v objektu datové sady a opravte některé ze štítků, v případě potřeby.

<b>2.3 image rozšíření</b><br>

Posílení objekt datové sady pomocí transformace popsané v [imgaug](https://github.com/aleju/imgaug) knihovny.

<b>2.4 DNN modelu definice</b><br>

Definujte architekturu modelu použitým v kroku školení. Šesti různých za Trénink hluboké neuronové sítě modely jsou podporované v AMLPCV: AlexNet, Resnet 18, Resnet-34 a Resnet – 50, Resnet 101 a Resnet 152.

<b>2.5 třídění školení</b><br>

Cvičení neuronové sítě s výchozí nebo vlastní parametry.

<b>2.6 vyhodnocení a vizualizaci</b><br>

Dílčím kroku poskytuje funkce, k vyhodnocení výkonu pro cvičný model v datové sadě služby nezávislé testu. Vyhodnocení metriky zahrnují přesnost, přesnost a odvolání a křivka ROC.

Tyto dílčích kroků jsou podrobně vysvětleny v odpovídající poznámkového bloku Jupyter. Jsme k dispozici také pokyny pro vypnutí parametry, jako je například učení rychlost, velikost dávky mini a rychlost Odpadlík dále zlepšit výkon modelu.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. nasazení](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Tento krok operationalizes modelu vytvořeného z kroku modelování. Zavádí požadavky operationalization a instalační program. Nakonec se vysvětluje spotřeby webovou službu. Prostřednictvím tohoto kurzu můžete Naučte se vytvářet modely hloubkové learning s AMLPCV a zprovoznit model v Azure.

## <a name="next-steps"></a>Další kroky
Přečtěte si další dokumentaci na [Azure Machine Learning balíčku pro vize počítače (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) a [tým datové vědy procesu (TDSP)](https://aka.ms/tdsp) začít pracovat.


## <a name="references"></a>Odkazy

* [Azure Machine Learning balíčku pro počítač vize (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Vědecké zpracování dat virtuálních počítačů](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

