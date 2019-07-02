---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Další informace o použití modulu pro rozpoznávání formuláře analyzovat data formuláře a tabulek.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: a17e47fb059c23ab2e6eb69f3cfe6f2f8d0e51b2
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502895"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

Azure formuláře Rozlišovač není služby cognitive Services, který používá technologií pro strojové učení k identifikaci a extrahovat páry klíč/hodnota a data tabulky z formuláře dokumentů. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Vlastní model modulu pro rozpoznávání formuláře můžete volat pomocí jednoduchého rozhraní REST API a snižuje složitost a snadno ji integrovat do vašich pracovních postupů nebo aplikace. Abyste mohli začít, stačí pět formuláře vyplněné dokumenty nebo dva formuláře vyplněné plus prázdný formulář stejného typu jako vstupní materiál. Rychle získat přesné výsledky, které jsou přizpůsobené pro konkrétní obsah bez náročné ruční zásah nebo odbornými znalostmi o rozsáhlé datové vědy.

## <a name="custom-models"></a>Vlastní modely

Vlastní model modulu pro rozpoznávání formuláře trénovat na vaše vlastní data a potřebujete jenom pět ukázkové vstupní formuláře ke spuštění. Při odesílání vstupní data algoritmus clusterů formuláře podle typu, zjistí, jaké klíčů a tabulky jsou k dispozici a přiřazuje hodnoty ke klíčům a položky do tabulky. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Poté, co jste trénování modelu, můžete otestovat a přeučování ji a nakonec použijete spolehlivě extrahovat data z více formulářů podle vašich potřeb.

Učení bez supervize umožňuje tomuto modelu porozumět relacím mezi poli a položkami a jejich rozložení a nevyžaduje se přitom ruční popisování dat ani náročné kódování nebo údržba. Modely předem vytrénovaných strojového učení naopak vyžadují standardizované data. Jsou to méně přesné s vstupní materiálů, které se odchylují od tradiční formáty, jako jsou formuláře specifických pro dané odvětví.

## <a name="pre-built-receipt-model"></a>Předem připravené přijetí modelu

Rozpoznávání formuláře také model pro čtení prodejních výnosů. Tento model extrahuje informace o klíči jako je například transakce, obchodní informace, množství daň a součty a další data a času. Kromě toho předem připravené potvrzení model se trénuje nerozpozná a nenahraje a vrátí všechny textu na potvrzení.

## <a name="what-it-includes"></a>Co zahrnuje

Formulář Rozlišovač není k dispozici jako rozhraní REST API. Můžete vytvořit, trénování a určení skóre modelu vlastní nebo přístup k předem sestavených modelů vyvoláním tato rozhraní API. Pokud chcete, můžete trénování a spuštění vlastních modelů v místním kontejneru Dockeru.

## <a name="input-requirements-custom-model"></a>Vstupní požadavky (vlastní model)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Vyžádat si přístup

Formulář Rozlišovač není k dispozici ve verzi preview omezeným přístupem. Pokud chcete získat přístup k verzi preview, vyplňte a odešlete [žádost o přístup formulář Rozlišovač](https://aka.ms/FormRecognizerRequestAccess) formuláře. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat nástroj pro rozpoznávání formuláře. Pokud vaše žádost o schválení od týmu služeb Azure Cognitive Services, dostanete e-mail s pokyny k přístupu ke službě.

## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1:** Vytvořte prostředek Rozlišovač formuláře na webu Azure Portal.

**Krok 2:** Postup rychlého spuštění použití rozhraní REST API:
* [Rychlé zprovoznění: Trénování modelu Rozlišovač formuláře a extrahovat data formuláře pomocí rozhraní REST API pomocí cURL](quickstarts/curl-train-extract.md)
* [Rychlé zprovoznění: Trénování modelu Rozlišovač formuláře a extrahovat data formuláře pomocí rozhraní REST API s využitím Pythonu](quickstarts/python-train-extract.md)
* [Rychlé zprovoznění: Extrahování příjmu dat pomocí cURL](quickstarts/curl-receipts.md)
* [Rychlé zprovoznění: Extrahování příjmu dat pomocí Pythonu](quickstarts/python-receipts.md)

Doporučujeme používat bezplatné služby, při seznamování se s technologiemi. Uvědomte si, že počet volných stránek je omezený na 500 za měsíc.

**Krok 3:** Prohlédněte si rozhraní API REST

Následující rozhraní API použijete k natrénování a extrahovat strukturovaná data z formuláře.

|||
|---|---|
| Trénování modelu| Natrénovat nový model analýze formulářů pomocí pěti formy stejného typu. Nebo trénování s prázdný formulář a dva formuláře vyplněné.  |
| Analýza formuláře |Analýza předaný jako datový proud pro extrakci páry klíč/hodnota a tabulek z formuláře pomocí vlastního modelu jednotlivý dokument.  |
| Analýza příjmu |Analýza dokumentu jednoho potvrzení extrahovat klíčové informace a další text potvrzení.|

Prozkoumejte [dokumentace k rozhraní REST API](https://aka.ms/form-recognizer/api) Další informace. 

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Tato služba se nabízí jako [ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) služby Azure v rámci [Online podmínek použití služby](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Stejně jako u všech cognitive services, vývojáře, kteří používají službu rozpoznávání formuláře měli vědět zásadách společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Dokončení [rychlý Start](quickstarts/curl-train-extract.md) začít s [rozhraní API pro rozpoznávání úseků](https://aka.ms/form-recognizer/api).
