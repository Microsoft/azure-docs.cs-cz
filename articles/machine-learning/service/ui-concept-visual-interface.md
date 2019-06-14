---
title: Vizuální rozhraní
titleSuffix: Azure Machine Learning service
description: Další informace o podmínkách, koncepty a pracovní postup, který tvoří rozhraní visual (preview) pro službu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: be07e0f3438ea93312d4eb440e7e63b8f98e11b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077379"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Co je vizuální rozhraní pro službu Azure Machine Learning? 

Vizuální rozhraní (preview) pro službu Azure Machine Learning umožňuje pro přípravu dat, trénování, testování, nasazení, správě a sledování modelů strojového učení bez psaní kódu.

Neexistuje žádné programování, vizuálně připojíte [datových sad](#dataset) a [moduly](#module) k vytvoření vašeho modelu.

Vizuální rozhraní používá služby Azure Machine Learning [pracovní prostor](concept-workspace.md) na:

+ Zápis artefakty [experimentovat](#experiment) i v pracovním prostoru.
+ Přístup [datových sad](#dataset).
+ Použití [výpočetní prostředky](#compute) v pracovním prostoru pro spuštění testu. 
+ Zaregistrujte [modely](concept-azure-machine-learning-architecture.md#models).
+ [Nasazení](#deployment) modely jako webové služby na výpočetní prostředky v pracovním prostoru.

![Přehled vizuální rozhraní](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Pracovní postup

Vizuální rozhraní umožňuje interaktivní vizuální plátno rychle vytvářet, testovat a iterovat model. 

+ Můžete přetažením myší [moduly](#module) na plátno.
+ Propojit moduly do formuláře [experimentovat](#experiment).
+ Spusťte experiment pomocí výpočetního prostředku pracovního prostoru služby Machine Learning.
+ Iterace návrhu modelu upravovat experiment a znovu spustit.
+ Jakmile budete připraveni, převést vaše **výukového experimentu** k **prediktivní experiment**.
+ [Nasazení](#deployment) prediktivní experiment jako webové služby tak, aby váš model je přístupný ostatním.

## <a name="experiment"></a>Experiment

Vytvoření experimentu úplně od začátku, nebo pomocí existujícího ukázkového experimentu jako šablony.  Pokaždé, když spuštění experimentu, artefakty jsou uložené ve vašem pracovním prostoru.

Experiment sestává z datových sad a analytickým modulům. ty lze vzájemně propojit a vytvořit model. Platný experiment má konkrétně tyto charakteristiky:

* Datové sady mohou připojit jen k modulům.
* Moduly je možné připojovat k datovým sadám, ostatní moduly.
* Všechny vstupní porty modulů musí mít několik připojení ke datový tok.
* Všechny požadované parametry pro každý modul musí být nastavena.

Příklad jednoduchého experimentu najdete v tématu [rychlý start: Příprava a vizualizace dat, aniž byste museli psát kód ve službě Azure Machine Learning](ui-quickstart-run-experiment.md).

Obsáhlejší návod řešení prediktivní analýzy, najdete v části [kurzu: Předvídání cen automobilů vizuální rozhraní](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Datová sada

Datové sady jsou data, která je nahraná vizuální rozhraní pro použití v procesu modelování. Jsou zahrnuty experimentovat s celou řadou ukázkových datových sad a můžete nahrávat další datové sady, kdy ho potřebujete.

## <a name="module"></a>Modul

Modul je algoritmus, který je možné provést na datech. Vizuální rozhraní má několik modulů od funkcí příchozího přenosu dat do školení, vyhodnocení a procesy ověřování.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když vyberete modul na plátně, parametry modulu se zobrazí v podokně vlastností napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

![Vlastnosti modulu](media/ui-concept-visual-interface/properties.png)

Nějaké Nápověda procházení knihovny dostupných algoritmů strojového učení, naleznete v tématu [přehled odkazů modulům a algoritmům](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Výpočetní prostředky

Použití výpočetních prostředků z pracovního prostoru ke spouštění experimentů nebo hostitele nasazených modelů jako webové služby. Cílových podporovaných výpočetních prostředí jsou:


| Cílové výpočetní prostředí | Školení | Nasazení |
| ---- |:----:|:----:|
| Azure Machine Learning compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Cíle jsou připojeny k Machine Learning COMPUTE [pracovní prostor](concept-workspace.md). Správa vašich cílových výpočetních prostředí ve vašem pracovním prostoru v [webu Azure portal](https://portal.azure.com).

## <a name="deployment"></a>Nasazení

Jakmile je váš model prediktivní analýzy připraven, nasadit jako webovou službu přímo z vizuální rozhraní.

Webové služby poskytují rozhraní mezi aplikací a vyhodnocování modelu. Externí aplikace komunikovat s modelem hodnocení v reálném čase. Volání webové služby vrací do externí aplikace predikované výsledky. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba je založen na REST, možnost Oblíbené architektuře programátorských projektů na webu.

Zjistěte, jak model nasadit, najdete v článku [kurzu: Nasadit model strojového učení vizuální rozhraní](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Další postup

* Seznamte se se základy prediktivní analýzy a strojové učení s [rychlý start: Příprava a vizualizace dat, aniž byste museli psát kód ve službě Azure Machine Learning](ui-quickstart-run-experiment.md).
* Použijte jednu z ukázek a upravte Suite vašim potřebám:
    * [Ukázka 1 - regrese: Předpovědět cenu](ui-sample-regression-predict-automobile-price-basic.md)
    * [Ukázka 2 - regrese: Předpovědět cenu a porovnat algoritmy](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Ukázka 3 – klasifikace: Předpovědět úvěrové riziko](ui-sample-classification-predict-credit-risk-basic.md)
    * [Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Ukázka 5 – klasifikace: Předpověď změn, appetency a prodej nahoru](ui-sample-classification-predict-churn.md)
