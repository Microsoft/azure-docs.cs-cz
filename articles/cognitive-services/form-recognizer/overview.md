---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Nástroj pro rozpoznávání formulářů Azure Cognitive Services umožňuje identifikovat a extrahovat páry klíč/hodnota a tabulková data z dokumentů formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 86e0494ded84e9711b2283ae5fad77ff626cf792
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904488"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

Nástroj pro rozpoznávání formulářů Azure je výtahová služba, která pomocí technologie strojového učení identifikuje a extrahuje páry klíč/hodnota a tabulková data z dokumentů formuláře. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Vlastní model rozpoznávání formulářů můžete volat pomocí jednoduchého REST API, abyste snížili složitost a mohli se snadno integrovat do pracovního postupu nebo aplikace. Chcete-li začít, stačí pouze pět vyplněných dokumentů formuláře nebo dva vyplněné formuláře plus prázdná forma stejného typu jako vstupní materiál. Rychle získáte přesné výsledky, které jsou přizpůsobené vašemu konkrétnímu obsahu bez nutnosti ručních zásahů nebo rozsáhlých znalostí z oblasti datové vědy.

## <a name="custom-models"></a>Vlastní modely

Vlastní model nástroje pro rozpoznávání, který je součástí vlastních dat, stačí pro spuštění pouze pěti vzorových vstupních formulářů. Když odesíláte vstupní data, algoritmus clusteruje formuláře podle typu, zjistí, jaké klíče a tabulky jsou k dispozici, a přidruží hodnoty k klíčům a záznamům k tabulkám. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

Učení bez supervize umožňuje tomuto modelu porozumět relacím mezi poli a položkami a jejich rozložení a nevyžaduje se přitom ruční popisování dat ani náročné kódování nebo údržba. Naproti tomu modely strojového učení předem proškolenou data vyžadují standardizovaná data. Jsou méně přesné u vstupních materiálů, které se odchylují od tradičních formátů, jako jsou například formuláře specifické pro jednotlivé obory.

## <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Nástroj pro rozpoznávání formulářů obsahuje také model pro čtení prodejních příjmů. Tento model extrahuje klíčové informace, jako je čas a datum transakce, informace o obchodníkech, množství daní a součtů a další. Předem sestavený model příjmu je navíc vyškolen pro rozpoznání a vrácení veškerého textu na účtence.

## <a name="what-it-includes"></a>Co zahrnuje

Nástroj pro rozpoznávání formulářů je k dispozici jako REST API. Pomocí těchto rozhraní API můžete vytvořit, naučit a vyhodnotit vlastní model nebo získat přístup k předdefinovanému modelu. Pokud chcete, můžete v místním kontejneru Docker vytvořit vlastní modely a spustit je.

## <a name="input-requirements"></a>Požadavky na vstup
### <a name="custom-model"></a>Vlastní model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Vstupní požadavky pro model příjemky se mírně liší.

* Formát musí být JPEG, PNG, BMP, PDF (text nebo naskenovaný) nebo TIFF.
* Velikost souboru musí být menší než 20 MB.
* Rozměry obrázku musí být mezi 50 × 50 pixelů a 10000 × 10000 pixelů. 
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají zákonným nebo a3 velikosti papíru a menšímu.
* Pro PDF a TIFF se zpracovávají jenom první 200 stránky (s předplatným úrovně Free, zpracovávají se jenom první dvě stránky).

## <a name="request-access"></a>Vyžádání přístup

Nástroj pro rozpoznávání formulářů je k dispozici ve verzi Preview s omezeným přístupem. Chcete-li získat přístup k verzi Preview, vyplňte a odešlete formulář [žádosti o přístup pro rozpoznávání formulářů](https://aka.ms/FormRecognizerRequestAccess) . Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete používat nástroj pro rozpoznávání formulářů. Pokud je váš požadavek schválen týmem Azure Cognitive Services, obdržíte e-mail s pokyny pro přístup ke službě.

## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1:** Vytvořte prostředek pro rozpoznávání formulářů v Azure Portal.

**Krok 2:** Postupujte podle rychlého startu a použijte REST API:
* [Rychlý Start: výuka modelu pro rozpoznávání formulářů a extrakce dat formuláře pomocí REST API s kudrlinkou](quickstarts/curl-train-extract.md)
* [Rychlý Start: výuka modelu pro rozpoznávání formulářů a extrakce dat formuláře pomocí REST API s Pythonem](quickstarts/python-train-extract.md)
* [Rychlý Start: extrakce dat příjmu pomocí oblé](quickstarts/curl-receipts.md)
* [Rychlý Start: extrakce údajů o příjemcích pomocí Pythonu](quickstarts/python-receipts.md)

Při učení technologie doporučujeme používat bezplatnou službu. Mějte na paměti, že počet bezplatných stránek je omezený na 500 za měsíc.

**Krok 3:** Kontrola rozhraní REST API

Pomocí následujících rozhraní API můžete vyškolit a extrahovat strukturovaná data z formulářů.

|||
|---|---|
| Trénování modelu| Vytvořte nový model pro analýzu formulářů s použitím pěti forem stejného typu. Nebo se naučíte prázdným formulářem a dvěma vyplněnými formuláři.  |
| Analyzovat formulář |Analyzujte jeden dokument předaný jako datový proud pro extrakci párů klíč/hodnota a tabulky z formuláře pomocí vlastního modelu.  |
| Analyzovat příjem |Analyzujte jeden příjmový doklad pro extrakci klíčových informací a dalšího textu příjmu.|

Další informace najdete v [referenční dokumentaci k REST API](https://aka.ms/form-recognizer/api) . 

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Tato služba se nabízí jako [verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) služby Azure pod [podmínkami online služby](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Stejně jako u všech služeb rozpoznávání by měli vývojáři, kteří používají službu pro rozpoznávání formulářů, znát zásady Microsoftu u zákaznických dat. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Dokončete [rychlý Start](quickstarts/curl-train-extract.md) , abyste mohli začít s [rozhraními API pro rozpoznávání formulářů](https://aka.ms/form-recognizer/api).