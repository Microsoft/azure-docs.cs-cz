---
title: Co je pracovní prostor a projekt? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje rozdíly mezi pracovním prostorem a projektem a také kategorie a popisky projektu pro vlastní službu Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41586b2ec96d30fd583cbc9a746493ed9206548b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510738"
---
# <a name="what-is-a-custom-translator-workspace"></a>Co je vlastní pracovní prostor překladatele?

Pracovní prostor je pracovní oblast pro vytváření a vytváření vlastního systému překladu. Pracovní prostor může obsahovat více projektů, modelů a dokumentů. Veškerá práce, kterou vlastní překladatel, je uvnitř konkrétního pracovního prostoru.

Pracovní prostor je pro vás soukromý a osoby, které do svého pracovního prostoru zvete. Pozvaní uživatelé nemají přístup k obsahu vašeho pracovního prostoru. Do svého pracovního prostoru můžete pozvat tolik lidí, kolik chcete, a kdykoli ho upravit nebo odebrat. Můžete také vytvořit nový pracovní prostor. Ve výchozím nastavení nebude pracovní prostor obsahovat žádné projekty ani dokumenty, které se nacházejí v jiných pracovních prostorech.

## <a name="what-is-a-custom-translator-project"></a>Co je vlastní projekt překladatele?

Projekt je obálka pro model, dokumenty a testy. Každý projekt automaticky obsahuje všechny dokumenty, které jsou odeslány do daného pracovního prostoru, které mají správnou dvojici jazyků. Například pokud máte projekt z angličtiny do španělštiny i pro španělštinu do projektu v angličtině, budou stejné dokumenty zahrnuty v obou projektech. Ke každému projektu je přidruženo KódKategorie, které se používá při dotazování [rozhraní V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) na překlady. KódKategorie je parametr, který se používá k získání překladu z přizpůsobeného systému vytvořeného pomocí vlastního překladatele.

## <a name="project-categories"></a>Kategorie projektu

Kategorie identifikuje doménu – oblast terminologie a stylu, kterou chcete použít – pro váš projekt. Vyberte kategorii, která bude pro vaše dokumenty nejrelevantnější. V některých případech vaše volba kategorie přímo ovlivňuje chování vlastního překladatele.

Máme dvě sady základních modelů. Jsou to obecné a technologie. Pokud je vybrána **technologie** kategorie, budou použity modely standardních hodnot technologie. Pro jakýkoli výběr kategorie se použijí obecné základní modely. Model standardních hodnot technologie je dobře v technologické doméně, ale zobrazuje nižší kvalitu, pokud věty použité pro překlad nespadají do domény technologie. Doporučujeme zákazníkům vybrat technologii kategorií pouze v případě, že věty spadají výhradně do domény technologie.

Ve stejném pracovním prostoru můžete vytvořit projekty pro stejnou dvojici jazyků v různých kategoriích. Vlastní Překladatel zabraňuje vytvoření duplicitního projektu se stejnou dvojicí a kategorií jazyka. Použití popisku pro váš projekt vám umožní vyhnout se tomuto omezení. Nepoužívejte popisky, pokud sestavíte systémy překladu pro více klientů, protože přidání jedinečného popisku do projektu se projeví v KódKategorie v projektech.

## <a name="project-labels"></a>Štítky projektu

Vlastní Překladatel umožňuje přiřadit k projektu popisek projektu. Popisek projektu rozlišuje mezi několika projekty se stejnou dvojicí a kategorií jazyka. Jako osvědčený postup nepoužívejte popisky projektu, pokud je to nutné.

Popisek projektu je použit jako součást KódKategorie. Pokud je popisek projektu ponechán Nenastaveno na stejné hodnotě v rámci projektů, projekty se stejnou kategorií a *různými* jazykovými páry budou sdílet stejnou hodnotu KódKategorie. Tato metoda je výhodná, protože vám nebo vašemu zákazníkovi umožňuje přepínat mezi jazyky při použití rozhraní API pro překladatele textu, aniž byste se museli starat o KódKategorie, který je jedinečný pro každý projekt.

Pokud se například chtělo povolit překlady v produkční doméně z angličtiny do francouzštiny a ze francouzštiny do angličtiny, vytvoří jsem dva projekty: jeden pro angličtinu – \> francouzština a druhý pro francouzštinu – \> angličtina. Zadám stejnou kategorii (technologie) pro obojí a ponechání popisku projektu prázdné. CategoryID pro oba projekty by odpovídaly, takže se můžu dotázat na rozhraní API pro angličtinu i francouzštinu, aniž byste museli upravovat své KódKategorie.

Pokud jste poskytovatel jazykové služby a chcete sloužit více zákazníkům s různými modely, které si zachovávají stejnou dvojici kategorií a jazyků, pak se pomocí popisku projektu rozliší mezi zákazníky.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [školení a modelu](training-and-model.md) , abyste věděli, jak efektivně sestavit model překladu.
