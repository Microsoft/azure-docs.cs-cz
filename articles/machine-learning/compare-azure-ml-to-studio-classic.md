---
title: Azure Machine Learning vs. Machine Learning Studio (Classic)
description: Jak se Azure Machine Learning liší od Machine Learning Studio (Classic)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a808387c891b4078092c5cac1bf95df8472ff0f6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529541"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Způsob, jakým se Azure Machine Learning liší od Machine Learning Studio (klasické)

Tento článek porovnává funkce, možnosti a rozhraní Azure Machine Learning Machine Learning Studio (Classic). 

## <a name="about-machine-learning-studio-classic"></a>O Machine Learning Studio (Classic)
[Machine Learning Studio (Classic)](studio/what-is-ml-studio.md) je prostředí pro spolupráci, přetahování myší, kde můžete vytvářet, testovat a nasazovat řešení strojového učení, aniž byste museli psát kód. Používá předem sestavené a předem nakonfigurované algoritmy strojového učení a moduly pro zpracování dat a také speciální výpočetní platformu.

## <a name="about-azure-machine-learning"></a>Informace o službě Azure Machine Learning

Mezitím [Azure Machine Learning](overview-what-is-azure-ml.md) poskytuje jak webové rozhraní s názvem Návrhář (Preview) **,** tak několik sad SDK a CLI pro rychlé přípravu dat, výuku a nasazení modelů strojového učení. Díky Azure Machine Learning získáte škálování, podporu více platforem, pokročilé možnosti ML, jako je automatické strojové učení a podpora kanálů.

Azure Machine Learning Designer nabízí podobné prostředí pro přetahování do studia (Classic). Na rozdíl od proprietární výpočetní platformy studia (Classic) ale Návrhář používá vaše vlastní výpočetní prostředky, je škálovatelný a plně integrovaný do Azure Machine Learning.  

> [!TIP]
> Zákazníci, kteří aktuálně používají nebo vyhodnocují Machine Learning Studio (Classic), jsou doporučováni k vyzkoušení [Azure Machine Learningho návrháře](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (Preview), který __poskytuje moduly pro přetahování a škálovatelnost__ , správu verzí a zabezpečení podniku.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Porovnání: Azure Machine Learning vs. Machine Learning Studio (Classic)

Tady je rychlé porovnání.

||  Návrhář Azure Machine Learning|Studio (Classic) |
|---| --- | --- |
||Návrhář je ve verzi Preview, Azure Machine Learning je GA.|Všeobecně dostupná (GA) | 
|Rozhraní podporující přetahování| Ano | Ano|
|Experiment| Škálování s cílem výpočetního prostředí|Škálování (limit pro školicí data pro 10GB) | 
|Moduly pro rozhraní| [Mnoho oblíbených modulů](algorithm-module-reference/module-reference.md) | Počtu |
|Školení výpočetních cílů| AML COMPUTE (GPU/CPU)|Proprietární výpočetní cíl, jenom procesor|
|Inferencing výpočetní cíle| Služba Azure Kubernetes pro odvození v reálném čase <br/>AML COMPUTE pro odvození dávky|Speciální formát webové služby, není přizpůsobitelný | 
|Kanál ML| Vytváření kanálů <br/> Publikovaný kanál <br/> Koncový bod kanálu <br/> [Další informace o kanálu ML](concept-ml-pipelines.md)|Nepodporováno | 
|Operace ML| Konfigurovatelné nasazení, model a správa verzí kanálu|Základní Správa modelů a nasazení | 
|Model| Standardní formát, různé závisí na úloze školení.|Speciální, nepřenosový formát.| 
|Automatizované školení modelu|Ještě není v návrháři, ale je to možné prostřednictvím rozhraní a sad SDK.| Ne | 

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Následující zdroje vám pomůžou začít s Azure Machine Learning

- Přečtěte si [přehled Azure Machine Learning](tutorial-first-experiment-automated-ml.md) 

- [Vytvořte svůj první kanál návrháře](tutorial-designer-automobile-price-train-score.md) pro předpověď automatických cen.

![Příklad návrháře Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Další kroky

Kromě možností přetahování v Návrháři Azure Machine Learning k dispozici další nástroje:  
  + [Použití poznámkových bloků Pythonu k učení & nasazení modelů ML](tutorial-1st-experiment-sdk-setup.md)
  + [Použití R Markdown ke školení & nasazení modelů ML](tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení ke studiu & nasazení modelů ML](tutorial-designer-automobile-price-train-score.md) 
  + [Použití rozhraní příkazového učení pro strojové učení ke školení a nasazení modelu](tutorial-train-deploy-model-cli.md)

