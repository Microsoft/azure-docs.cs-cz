---
title: Prediktivní řešení pro úvěrové riziko - Azure Machine Learning Studio | Dokumentace Microsoftu
description: Podrobný průvodce postupem vytvoření řešení prediktivní analýzy pro posuzování úvěrového rizika v nástroji Azure Machine Learning Studio
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: edbcd1ae128969bb10436afb1dc800c42e69f8c9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089815"
---
# <a name="walkthrough-develop-predictive-solution-for-credit-risk-assessment-in-azure-machine-learning-studio"></a>Názorný postup: Vývoj prediktivního řešení pro posuzování úvěrového rizika v nástroji Azure Machine Learning Studio

V tomto názorném průvodci se zaměříme na proces vývoje řešení prediktivní analýzy v nástroji Machine Learning Studio. Vytvoříme si jednoduchý model v nástroji Machine Learning Studio a pak ho nasadíme jako webovou službu Azure Machine Learning, ve které může model vytvářet predikce na základě nových dat. 

Tento názorný průvodce předpokládá, že jste nejméně jednou předtím použili Machine Learning Studio a že rozumíte konceptům strojového učení. Bere ale v úvahu, že nejste odborníkem ani na jedno.

Pokud jste nástroj **Azure Machine Learning Studio** ještě nikdy nepoužívali, možná budete chtít začít kurzem [Vytvoření prvního experimentu datové vědy v nástroji Azure Machine Learning Studio](create-experiment.md). Tento kurz vás poprvé provede nástrojem Machine Learning Studio. Ukáže vám základy toho, jak pomocí myši přetáhnout moduly do experimentu, vzájemně je propojit, spustit experiment a prohlédnout si výsledky. Dalším nástrojem, který vám může pomoci začít, je diagram s přehledem možností nástroje Machine Learning Studio. Stáhnout a vytisknout si ho můžete odtud: [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).
 
Pokud se strojovým učením obecně teprve začínáte, může vám pomoci série videí. Má název [Datová věda pro začátečníky](data-science-for-beginners-the-5-questions-data-science-answers.md) a poskytuje vynikající úvod do strojového učení s využitím jazyka a konceptů každodenního života.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>Problém

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je komplexní problém, ale pro účely tohoto názorného průvodce jej můžeme zjednodušit. Použijeme jej jako příklad způsobu, jakým můžete vytvořit řešení prediktivní analýzy pomocí služby Microsoft Azure Machine Learning. Využijeme k tomu nástroj Azure Machine Learning Studio a webovou službu Machine Learning.  

## <a name="the-solution"></a>Řešení

V tomto podrobném průvodci začneme s veřejně dostupnými daty o úvěrovém riziku, na jejichž základě vyvineme a natrénujeme prediktivní model. Potom model nasadíme jako webovou službu, aby ji pro posuzování úvěrového rizika mohli využívat další uživatelé.

Při vytváření řešení pro posuzování úvěrového rizika budeme postupovat po těchto krocích:  

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. [Vytvoření experimentu](walkthrough-3-create-new-experiment.md)
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. [Nastavení přístupu k webové službě](walkthrough-6-access-web-service.md)

> [!TIP] 
> Pracovní kopii experimentu, který vyvíjíme v tomto názorném průvodci, najdete v [galerii Azure AI](https://gallery.cortanaintelligence.com). Přejděte k části **[Názorný průvodce – Predikce úvěrového rizika](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)**, klikněte na **Open in Studio** (Otevřít v sadě Studio) a stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio.
> 
> Tento názorný postup je založen na zjednodušené verzi ukázkového experimentu [Binární klasifikace: Predikce úvěrového rizika](https://go.microsoft.com/fwlink/?LinkID=525270), který je také k dispozici v [Galerii](http://gallery.cortanaintelligence.com/).
