---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Další informace o použití modulu pro rozpoznávání formuláře analyzovat data formuláře a tabulek.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601624"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

Rozpoznávání formulářů Azure je kognitivní služba, která využívá technologie strojového učení k identifikaci a extrahování párů klíč-hodnota a dat tabulek z formulářových dokumentů. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Vlastní model modulu pro rozpoznávání formuláře můžete volat pomocí jednoduchého rozhraní REST API a snižuje složitost a snadno ji integrovat do vašich pracovních postupů nebo aplikace. Abyste mohli začít, stačí pět dokumenty formuláře nebo prázdný formulář stejného typu jako vstupní materiál. Rychle získat přesné výsledky, které jsou přizpůsobené pro konkrétní obsah bez náročné ruční zásah nebo odbornými znalostmi o rozsáhlé datové vědy.

## <a name="request-access"></a>Vyžádat si přístup
Formulář Rozlišovač není k dispozici ve verzi preview omezeným přístupem. Pokud chcete získat přístup k verzi preview, vyplňte a odešlete [žádost o přístup formulář Rozlišovač](https://aka.ms/FormRecognizerRequestAccess) formuláře. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat nástroj pro rozpoznávání formuláře. Pokud vaše žádost o schválení od týmu služeb Azure Cognitive Services, dostanete e-mail s pokyny k přístupu ke službě.

## <a name="what-it-does"></a>Co dělá

Když odešlete vstupních dat, trénovat algoritmus, clustery formuláře podle typů, zjistí, jaké klíčů a tabulky jsou k dispozici a zjišťuje pro přiřazení hodnoty ke klíčům a položky do tabulky. Učení bez supervize umožňuje tomuto modelu porozumět relacím mezi poli a položkami a jejich rozložení a nevyžaduje se přitom ruční popisování dat ani náročné kódování nebo údržba. Modely předem vytrénovaných strojového učení naopak vyžadují standardizované data a jsou méně přesné při použití s vstupní materiálů, které se odchylují od tradiční formáty, jako jsou formuláře specifických pro dané odvětví.

Poté, co jste trénování modelu, můžete otestovat a přeučování ji a nakonec použijete spolehlivě extrahovat data z více formulářů podle vašich potřeb.

## <a name="what-it-includes"></a>Co zahrnuje

Formulář Rozlišovač není k dispozici jako rozhraní REST API. Můžete vytvářet, trénovat a stanovíte jeho skóre modelu prostřednictvím volání rozhraní API. Pokud chcete, můžete spustit model v místním kontejneru Dockeru.

## <a name="input-requirements"></a>Vstupní požadavky

Formulář pro rozpoznávání funguje na vstupních dokumentů, které splňují tyto požadavky:

* Formát musí být JPG, PNG nebo PDF (text nebo naskenované). Vložený text PDF jsou nejvhodnější, protože nemohly žádným způsobem při extrakci znaků a umístění.
* Velikost souboru musí být menší než 4 megabajtů (MB).
* Pro Image dimenze musí být mezi 50 × 50 pixelů a 4200 x 4200 pixelů.
* Pokud prohledány z papíru dokumentů, formuláře by měl být vysoce kvalitní kontroly.
* Text, musíte použít latinské abecedy (anglické znaky).
* Data musí být vytištěna (ne rukou).
* Data musí obsahovat klíče a hodnoty.
* Klíče můžete objeví nad či hodnot, ale ne míň než vlevo nebo vpravo.

Nástroj pro rozpoznávání formuláře v současné době nepodporuje tyto typy vstupních dat:

* Komplexní tabulky (vnořené tabulky, sloučené záhlaví nebo buňky a tak dále).
* Zaškrtávací políčka nebo rádiových tlačítek.
* Dokumenty PDF je delší než 50 stránky.

## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1:** Vytvořte prostředek Rozlišovač formuláře na webu Azure Portal.

**Krok 2:** Vyzkoušejte si rychlé zprovoznění pro praktické zkušenosti:
* [Rychlé zprovoznění: Trénování modelu Rozlišovač formuláře a extrahovat data formuláře pomocí rozhraní REST API pomocí cURL](quickstarts/curl-train-extract.md)
* [Rychlé zprovoznění: Trénování modelu Rozlišovač formuláře a extrahovat data formuláře pomocí rozhraní REST API s využitím Pythonu](quickstarts/python-train-extract.md)

Doporučujeme vám, že při seznamování se s technologiemi používat bezplatné služby, ale mějte na paměti, že počet volných stránek je omezený na 500 stránky za měsíc.

**Krok 3:** Prohlédněte si rozhraní API REST

Použijte následující rozhraní API pro trénování a extrakci strukturovanými daty z formulářů.

| REST API | Popis |
|-----|-------------|
| Trénovat | Natrénovat nový model analýze formulářů pomocí pěti formuláře ze stejného typu nebo prázdný formulář.  |
| Analyzovat  |Analýza předaný jako datový proud pro extrakci páry klíč hodnota a tabulek z formuláře pomocí vlastního modelu jednotlivý dokument.  |

Prozkoumejte [rozhraní REST API referenční dokument](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Tato služba se nabízí jako [ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) služby Azure v rámci [Online podmínek použití služby](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Stejně jako u všech cognitive services, vývojáře, kteří používají službu rozpoznávání formuláře měli vědět zásadách společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Dokončení [rychlý Start](quickstarts/curl-train-extract.md) začít s [rozhraní API pro rozpoznávání úseků](https://aka.ms/form-recognizer/api).
