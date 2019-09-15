---
title: Vizuální rozhraní
titleSuffix: Azure Machine Learning
description: Přečtěte si o pojmech, konceptech a pracovním postupu, které tvoří vizuální rozhraní (Preview) pro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 65daf1468d27825d9904a14e42e43796d3985321
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996524"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Jaké je vizuální rozhraní pro Azure Machine Learning? 

Rozhraní Visual (Preview) pro Azure Machine Learning umožňuje přípravu dat, výuku, testování, nasazení, správu a sledování modelů strojového učení bez psaní kódu.

Není nutné žádné programování, vizuálně propojit [datové sady](#dataset) a [moduly](#module) a vytvořit model.

Toto vizuální rozhraní používá [pracovní prostor](concept-workspace.md) Azure Machine Learning k těmto akcím:

+ Do pracovního prostoru se napíší artefakty [experimentu](#experiment) .
+ Přístup k [datovým sadám](#dataset).
+ Ke spuštění experimentu použijte [výpočetní prostředky](#compute) v pracovním prostoru. 
+ Registrovat [modely](concept-azure-machine-learning-architecture.md#models).
+ [Nasaďte](#deployment) modely jako webové služby na výpočetních prostředcích v pracovním prostoru.

![Přehled vizuálního rozhraní](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Pracovní postup

Vizuální rozhraní poskytuje interaktivní a vizuální plátno pro rychlé sestavování, testování a iteraci modelu. 

+ [Moduly](#module) přetahujete na plátno.
+ Propojit moduly dohromady a vytvořit [experiment](#experiment).
+ Spusťte experiment pomocí výpočetního prostředku pracovního prostoru služby Machine Learning.
+ Iterujte na návrh modelu úpravou experimentu a jeho opětovným spuštěním.
+ Až budete připraveni, převeďte svůj **školicí experiment** na **prediktivní experiment**.
+ [Nasaďte](#deployment) prediktivní experiment jako webovou službu, aby k vašemu modelu měli přístup jiní uživatelé.

## <a name="experiment"></a>Experiment

Vytvořte experiment od nuly nebo použijte existující vzorový experiment jako šablonu.  Pokaždé, když spustíte experiment, se artefakty ukládají do vašeho pracovního prostoru.

Experiment se skládá z datových sad a analytických modulů, které spojíte dohromady za účelem vytvoření modelu. Platný experiment má konkrétně tyto charakteristiky:

* Datové sady mohou být připojeny pouze k modulům.
* Moduly se můžou připojit k datovým sadám nebo jiným modulům.
* Všechny vstupní porty pro moduly musí mít připojení k toku dat.
* Musí být nastavené všechny požadované parametry pro každý modul.


Informace o tom, jak začít s vizuálním rozhraním, [najdete v tématu Kurz: Předpovídat cenu automobilu pomocí vizuálního rozhraní](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Datová sada

Datová sada je data, která byla nahrána do vizuálního rozhraní pro použití v procesu modelování. Je k dispozici několik ukázkových datových sad, které můžete experimentovat s a můžete nahrávat více datových sad, jak je potřebujete.

## <a name="module"></a>Modul

Modul je algoritmus, který je možné provést na datech. Rozhraní vizuálu má řadu modulů od vstupních funkcí dat až po procesy školení, bodování a ověřování.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když vyberete modul na plátně, parametry modulu se zobrazí v podokně vlastnosti napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

![Vlastnosti modulu](media/ui-concept-visual-interface/properties.png)

Další nápovědu k dispozici v knihovně strojového učení, najdete v tématu [Přehled modulu & algoritmu](../algorithm-module-reference/module-reference.md) .

## <a name="compute"></a>Výpočetní prostředky

Pomocí výpočetních prostředků z pracovního prostoru můžete spustit experiment nebo hostovat nasazené modely jako webové služby. Cílových podporovaných výpočetních prostředí jsou:


| Cílové výpočetní prostředí | Školení | Nasazení |
| ---- |:----:|:----:|
| Azure Machine Learning compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Cíle výpočetní služby jsou připojeny k vašemu [pracovnímu prostoru](concept-workspace.md)Machine Learning. Výpočetní cíle můžete spravovat ve svém pracovním prostoru v [Azure Portal](https://portal.azure.com) nebo na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

## <a name="deployment"></a>Nasazení

Jakmile je model prediktivní analýzy připravený, nasadíte ho jako webovou službu přímo z vizuálního rozhraní.

Webové služby poskytují rozhraní mezi aplikací a modelem bodování. Externí aplikace může komunikovat s modelem bodování v reálném čase. Volání webové služby vrátí výsledky předpovědi do externí aplikace. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba je založena na REST, na základě oblíbené architektury pro projekty webového programování.

Informace o tom, jak model nasadit, najdete [v tématu Kurz: Nasaďte model strojového učení pomocí vizuálního](ui-tutorial-automobile-price-deploy.md)rozhraní.

## <a name="next-steps"></a>Další postup

* Seznamte se se základy prediktivní analýzy a strojového učení [s využitím kurzu: Předpověď ceny automobilu pomocí vizuálního rozhraní](ui-tutorial-automobile-price-train-score.md)
* Použijte jednu z ukázek a upravte ji tak, aby vyhovovala vašim potřebám:
    * [Ukázka 1 – regrese: Předpověď ceny](ui-sample-regression-predict-automobile-price-basic.md)
    * [Ukázka 2 – regrese: Předpověď cen a porovnávání algoritmů](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Ukázka 3 – klasifikace: Předpověď úvěrového rizika](ui-sample-classification-predict-credit-risk-basic.md)
    * [Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Ukázka 5 – klasifikace: Předpověď změn, appetencyí a prodejů](ui-sample-classification-predict-churn.md)
