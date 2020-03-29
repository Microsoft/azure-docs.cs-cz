---
title: Co je pracovní prostor a projekt? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek bude vysvětlovat rozdíly mezi pracovním prostorem a projektem, stejně jako kategorie projektu a popisky pro službu Vlastní překladač.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219431"
---
# <a name="what-is-a-custom-translator-workspace"></a>Co je vlastní překladač pracovního prostoru?

Pracovní prostor je pracovní plocha pro vytváření a vytváření vlastního překladatelského systému. Pracovní prostor může obsahovat více projektů, modelů a dokumentů. Veškerá práce, kterou provádíte v custom translatoru, je uvnitř určitého pracovního prostoru.

Pracovní prostor je soukromý pro vás i pro lidi, které pozvete do pracovního prostoru. Nepozvaní uživatelé nemají přístup k obsahu vašeho pracovního prostoru. Do pracovního prostoru můžete pozvat libovolný počet lidí a kdykoli upravit nebo odebrat jejich přístup. Můžete také vytvořit nový pracovní prostor. Ve výchozím nastavení nebude pracovní prostor obsahovat žádné projekty ani dokumenty, které jsou v ostatních pracovních prostorech.

## <a name="what-is-a-custom-translator-project"></a>Co je vlastní překladač projektu?

Projekt je obálka pro model, dokumenty a testy. Každý projekt automaticky zahrnuje všechny dokumenty, které jsou odeslány do tohoto pracovního prostoru, které mají správný pár jazyků. Například pokud máte projekt z angličtiny do španělštiny a projekt ze španělštiny do angličtiny, budou do obou projektů zahrnuty stejné dokumenty. Každý projekt má CategoryID s ním spojené, který se používá při dotazování [V3 ROZHRANÍ API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) pro překlady. CategoryID je parametr používaný k získání překladů z přizpůsobeného systému vytvořeného pomocí vlastního překladače.

## <a name="project-categories"></a>Kategorie projektu

V kategorii je uvedena doména – oblast terminologie a stylu, kterou chcete použít – pro váš projekt. Vyberte kategorii, která je pro vaše dokumenty nejrelevantnější. V některých případech vaše volba kategorie přímo ovlivňuje chování vlastního překladače.

Máme dvě sady základních modelů. Jsou generál a technologie. Pokud je vybrána kategorie **Technologie,** budou použity základní modely technologie. Pro jakýkoli výběr ostatních kategorií se používají obecné základní modely. Základní model technologie si v oblasti technologie dobře provází, ale ukazuje nižší kvalitu, pokud věty použité pro překlad nespadají do oblasti technologie. Zákazníkům doporučujeme vybrat kategorii Technologie pouze v případě, že věty spadají striktně do oblasti technologie.

Ve stejném pracovním prostoru můžete vytvořit projekty pro stejný pár jazyků v různých kategoriích. Vlastní překladač zabraňuje vytvoření duplicitního projektu se stejným jazykovým párem a kategorií. Použití popisku na projekt umožňuje vyhnout se tomuto omezení. Nepoužívejte popisky, pokud nevytváříte překladové systémy pro více klientů, protože přidání jedinečného štítku do projektu se projeví ve vašich projektech CategoryID.

## <a name="project-labels"></a>Popisky projektů

Vlastní překladač umožňuje přiřadit popisek projektu k projektu. Popisek projektu rozlišuje mezi více projekty se stejným jazykovým párem a kategorií. Jako osvědčený postup, vyhněte se použití popisky projektu, pokud to není nutné.

Popisek projektu se používá jako součást CategoryID. Pokud je popisek projektu ponechán odstavený nebo je nastaven stejně mezi projekty, pak projekty se stejnou kategorií a *různými* jazykovými páry budou sdílet stejné ID kategorie. Tento přístup je výhodný, protože umožňuje vám nebo vašemu zákazníkovi přepínat mezi jazyky při použití rozhraní API překladače textu bez obav o CategoryID, který je jedinečný pro každý projekt.

Pokud bych například chtěl povolit překlady do oblasti Technologie z angličtiny do francouzštiny a z\> francouzštiny do angličtiny, vytvořil bych dva projekty: jeden pro angličtinu - francouzštinu a jeden pro francouzštinu -\> angličtinu. Chtěl bych určit stejnou kategorii (Technologie) pro oba a ponechat popisek projektu prázdné. CategoryID pro oba projekty by odpovídaly, takže jsem mohl dotaz rozhraní API pro anglické a francouzské překlady bez nutnosti měnit my CategoryID.

Pokud jste poskytovatel jazykových služeb a chcete poskytovat služby více zákazníkům s různými modely, které si zachovávají stejnou kategorii a dvojici jazyků, bylo by moudré rozhodnutí použít popisek projektu k rozlišení mezi zákazníky.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [školení a model](training-and-model.md) vědět, jak efektivně vytvořit model překladu.
