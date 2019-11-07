---
title: Azure Machine Learning vs. ML Studio (Classic)
description: Jak se Azure Machine Learning liší od ML Studio (Classic)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 721f730aa78be39b91f7d8368655dbbd5878903b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716692"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Způsob, jakým se Azure Machine Learning liší od ML Studio (klasické)

Tento článek porovnává funkce, možnosti a rozhraní Azure Machine Learning ML Studio (Classic). 

## <a name="about-ml-studio-classic"></a>O ML Studio (Classic)
[Ml Studio (Classic)](/studio/what-is-ml-studio.md) je prostředí pro spolupráci, přetahování myší, kde můžete vytvářet, testovat a nasazovat řešení strojového učení, aniž byste museli psát kód. Používá předem sestavené a předem nakonfigurované algoritmy strojového učení a moduly pro zpracování dat a také speciální výpočetní platformu.

## <a name="about-azure-machine-learning"></a>Informace o službě Azure Machine Learning

Mezitím [Azure Machine Learning](/service/overview-what-is-azure-ml.md) poskytuje jak webové rozhraní s názvem Návrhář (Preview) **,** tak několik sad SDK a CLI pro rychlé přípravu dat, výuku a nasazení modelů strojového učení. Díky Azure Machine Learning získáte škálování, podporu více platforem, pokročilé možnosti ML, jako je automatické strojové učení a podpora kanálů.

Azure Machine Learning Designer nabízí podobné prostředí pro přetahování do studia (Classic). Na rozdíl od proprietární výpočetní platformy studia (Classic) ale Návrhář používá vaše vlastní výpočetní prostředky, je škálovatelný a plně integrovaný do Azure Machine Learning.  

> [!TIP]
> Zákazníci, kteří aktuálně používají nebo vyhodnocují Machine Learning Studio (Classic), jsou doporučováni k vyzkoušení [Azure Machine Learningho návrháře](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (Preview), který __poskytuje moduly pro přetahování a škálovatelnost__ , správu verzí a zabezpečení podniku.

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>Porovnání: Azure Machine Learning vs. ML Studio (Classic)

Tady je rychlé porovnání.

||  Návrhář Azure Machine Learning|Studio (Classic) |
|---| --- | --- |
||Návrhář je ve verzi Preview, Azure Machine Learning je GA.|Všeobecně dostupná (GA) | 
|Rozhraní přetažení| Ano | Ano|
|Začátku| Škálování s cílem výpočetního prostředí|Škálování (limit pro školicí data pro 10GB) | 
|Moduly pro rozhraní| [Mnoho oblíbených modulů](algorithm-module-reference/module-reference.md) | Počtu |
|Školení výpočetních cílů| AML COMPUTE (GPU/CPU)|Proprietární výpočetní cíl, jenom procesor|
|Inferencing výpočetní cíle| Služba Azure Kubernetes pro odvození v reálném čase <br/>AML COMPUTE pro odvození dávky|Speciální formát webové služby, není přizpůsobitelný | 
|Kanál ML| Vytváření kanálů <br/> publikovaný kanál <br/> Koncový bod kanálu <br/> [Další informace o kanálu ML](service/concept-ml-pipelines.md)|Nepodporováno | 
|Operace ML| Konfigurovatelné nasazení, model a správa verzí kanálu|Základní Správa modelů a nasazení | 
|Model| Standardní formát, různé závisí na úloze školení.|Speciální, nepřenosový formát.| 
|Automatizované školení modelu|Ještě není v návrháři, ale je to možné prostřednictvím rozhraní a sad SDK.| Ne | 

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Následující zdroje vám pomůžou začít s Azure Machine Learning

- Přečtěte si [přehled Azure Machine Learning](service/tutorial-first-experiment-automated-ml.md) 

- [Vytvořte svůj první kanál návrháře](service/tutorial-designer-automobile-price-train-score.md) pro předpověď automatických cen.

![Příklad návrháře Azure Machine Learning](service/media/concept-ml-pipelines/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Další kroky

Kromě možností přetahování v Návrháři Azure Machine Learning k dispozici další nástroje:  
  + [Použití poznámkových bloků Pythonu k učení & nasazení modelů ML](./service/tutorial-1st-experiment-sdk-setup.md)
  + [Použití R Markdown ke školení & nasazení modelů ML](./service/tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení ke studiu & nasazení modelů ML](./service/tutorial-designer-automobile-price-train-score.md) 
  + [Použití rozhraní příkazového učení pro strojové učení ke školení a nasazení modelu](./service/tutorial-train-deploy-model-cli.md)

