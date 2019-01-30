---
title: Co je pracovní prostor a projektu? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Pracovní prostor je pracovní oblast pro sestavování a vytváření vlastních překladový systém. Pracovní prostor může obsahovat více projektů, modely a dokumenty. Projekt je obálka pro model, dokumenty a testy. Každý projekt automaticky obsahuje všechny dokumenty, které jsou odeslány do pracovního prostoru, které mají pár správný jazyk.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 1612c5ae6f6fd82386a4d4fba20f011621223a84
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214105"
---
# <a name="what-is-a-custom-translator-workspace"></a>Co je pracovní prostor Translator vlastní?

Pracovní prostor je pracovní oblast pro sestavování a vytváření vlastních překladový systém. Pracovní prostor může obsahovat více projektů, modely a dokumenty. Veškerou práci, kterou provedete v vlastní Translator je uvnitř určitý pracovní prostor.

Pracovní prostor je privátní a jste pozvaní do pracovního prostoru. Zrušena. uživatelé nebudou mít přístup k obsahu vašeho pracovního prostoru. Můžete pozvat tolik uživatelů, jako je do pracovního prostoru a změnit nebo odebrat přístup můžete kdykoli. Můžete také vytvořit nový pracovní prostor. Ve výchozím nastavení pracovní prostor nebude obsahovat žádné projekty nebo dokumenty, které jsou v rámci jiných pracovních prostorů.

## <a name="what-is-a-custom-translator-project"></a>Co je vlastní Translator projektu?

Projekt je obálka pro model, dokumenty a testy. Každý projekt automaticky obsahuje všechny dokumenty, které jsou odeslány do pracovního prostoru, které mají pár správný jazyk. Například pokud máte angličtina, španělština projektu a španělština anglické projektu, stejné dokumenty se zahrne v obou projektů. Každý projekt má CategoryID používaný při dotazování [rozhraní API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) pro překlady. ID kategorie je parametr použít k získání z přizpůsobené systému vytvořených pomocí překladače vlastní překlady.

## <a name="project-categories"></a>Kategorie projektů

Kategorie určuje doménu – oblast terminologie a stylu, které chcete použít – pro váš projekt. Zvolte kategorii/jí nejvíce hodilo dokumentů. V některých případech podle vašeho výběru kategorie přímo ovlivňuje chování vlastní překladač.

Máme dvě sady základní modely. Jsou obecné a technologie. Pokud kategorie **technologie** je zaškrtnuto, bude použita technologie základní modely. Pro další výběr kategorie se používají obecné základní modely. Model základní technologie nemá v doméně technologie, ale zobrazuje nižší kvality, pokud není věty používá pro překlad spadají do domény technologie. Doporučujeme zákazníkům vybrat kategorii technologii pouze v případě, že věty spadat výhradně v rámci domény technologie.

Ve stejném pracovním prostoru mohou vytvářet projekty pro stejného páru jazyk v různých kategoriích. Vlastní Translator brání vytvoření duplicitního projektu s stejného páru jazyka a kategorie. Použití popisku do projektu umožňuje se tomuto omezení vyhnout. Nepoužívejte popisky, pokud pro více klientů, jako přidávání že jedinečný popisek do projektu se projeví ve vašich projektech ID kategorie vytváříte překladových systémů.

## <a name="project-labels"></a>Popisky projektu

Vlastní Translator umožňuje přiřadit popisek projektu do projektu. Popisek projektu rozlišuje mezi více projekty pomocí stejného páru jazyka a kategorie. Jako osvědčený postup, vyhněte se použití projektu popisků není-li nezbytné.

Popisek projektu slouží jako součást ID kategorie. Pokud popisek projektu zůstane zrušit nastavení nebo je stejně jako ve všech projektech, pak projekty se stejnou kategorií a *různých* dvojice jazyků bude sdílet stejné ID kategorie. Tento přístup je výhodné, protože umožňuje vy nebo váš zákazník přepínat mezi jazyky, používáte Translator Text API bez starostí o ID kategorie, který je jedinečný pro každý projekt.

Například kdybych chtěl povolit překlady v technologii doméně z angličtiny z francouzština a Francouzština na angličtinu, můžu by vytvořit dva projekty: jeden pro angličtinu -\> francouzština a jeden pro Francouzština –\> angličtina. Můžu by zadejte stejnou category (technologie) pro obě a nezadáte, název projektu. ID kategorie pro oba projekty odpovídají, aniž byste museli změnit své ID kategorie mi může dotazování do rozhraní API pro angličtinu a francouzštinu překlady.

Pokud jste poskytovatelem služeb jazyka a chcete k poskytování více zákazníků s různými modely, které se zachovat stejné kategorie a dvojice jazyk, pak pomocí projektu popisek k rozlišení mezi zákazníky by z pohledu rozhodnutí.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [školení a model](training-and-model.md) vědět, jak efektivně vytvářet modelu překladu.
