---
title: Co je nástroj pro rozpoznávání formuláře?
titleSuffix: Azure Cognitive Services
description: Další informace o použití modulu pro rozpoznávání formuláře analyzovat data formuláře a tabulek.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 2a120a59a58eb8d7a017cce0dd85c21038bdcf51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143219"
---
# <a name="what-is-form-recognizer"></a>Co je nástroj pro rozpoznávání formuláře?

Azure formuláře Rozlišovač není služby cognitive Services, který používá technologií pro strojové učení k identifikaci a extrahovat páry klíč hodnota a data tabulky z formuláře dokumentů. Potom vypíše strukturovanými daty, která obsahuje vztahy v původním souboru. Můžete zavolat vlastní formulář Rozlišovač modelu pomocí jednoduchých rozhraní REST API k zjednodušení a snadno ji integrovat do vašich pracovních postupů nebo aplikace. Potřebujete pouze pět dokumenty formuláře nebo prázdný formulář stejného typu jako vstupní materiál, abyste mohli začít. Můžete rychle, přesně získat výsledky a přizpůsobená pro váš konkrétní obsah bez nutnosti náročné ruční zásah nebo odbornými znalostmi o rozsáhlé datové vědy.

## <a name="request-access"></a>Vyžádání přístup
Formulář Rozlišovač není k dispozici jako verze preview omezený přístup. Pokud chcete získat přístup k verzi preview, prosím vyplňte a odešlete [Cognitive Services pro rozpoznávání formulář žádosti o přístup](https://aka.ms/FormRecognizerRequestAccess) formuláře. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat nástroj pro rozpoznávání formuláře. Pokud vaše žádost o schválení od týmu služeb Azure Cognitive Services, dostanete e-mail s pokyny, jak získat přístup k službě.

## <a name="what-it-does"></a>Co dělá

Když odešlete vstupních dat, trénovat algoritmus, clustery formuláře podle typů, zjistí, jaké klíčů a tabulky jsou k dispozici a zjišťuje pro přiřazení hodnoty ke klíčům a položky do tabulky. Bez dohledu learning umožňuje modelu, který má seznamte s rozložením a vztahy mezi poli a položky bez označování ruční nebo psaní kódu a údržby. Naopak předem vytrénovaných strojového učení modely standardizované se data a jsou méně přesné s vstupní materiálů, které se odchylují od tradiční formáty, jako jsou formuláře specifických pro dané odvětví.

Jakmile model se trénuje, testování a přeučování nakonec použijete spolehlivě extrahovat data z více formulářů podle vašich potřeb.

## <a name="what-it-includes"></a>Co zahrnuje

Formulář Rozlišovač není k dispozici jako rozhraní REST API. Můžete vytvářet, trénovat a určení skóre modelu prostřednictvím volání rozhraní API a modelu můžete případně spustit v místním kontejneru Dockeru.

## <a name="input-requirements"></a>Vstupní požadavky

Nástroj pro rozpoznávání formuláře funguje na vstupních dokumentů, které splňují následující požadavky:

* Formát JPG, PNG nebo PDF (text nebo naskenované). Text vložené PDF je vhodnější, protože neexistuje žádná možnost při extrakci znaků a umístění.
* Velikost souboru musí být menší než 4 megabajtů (MB)
* Pro Image dimenze musí být až 50 × 50 pixelů 4200 x 4200
* Pokud prohledány z papíru dokumentů, formuláře by měl být vysoce kvalitní kontroly
* Musíte použít latinské abecedy (anglické znaky)
* Data vytištěna (ne rukou)
* Musí obsahovat klíče a hodnoty
* Klíče můžete objeví nad či hodnot, ale ne míň než vlevo nebo vpravo.

Kromě toho nástroj pro rozpoznávání formuláře zatím nepodporuje následující typy vstupních dat:

* Komplexní tabulky (vnořené tabulky, sloučené záhlaví nebo buňky atd.) 
* Zaškrtávací políčka nebo rádiových tlačítek
* Dokumenty PDF delší než 50 stránky

## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1:** Vytvořte prostředek Rozlišovač formuláře na webu Azure Portal.

**Krok 2:** Vyzkoušejte si rychlé zprovoznění pro praktické zkušenosti:
* [Rychlé zprovoznění: Trénování modelu Rozlišovač formuláře a extrahování dat formuláře pomocí rozhraní REST API pomocí cURL](quickstarts/curl-train-extract.md)
* [Rychlé zprovoznění: Trénování modelu Rozlišovač formuláře a extrahování dat formuláře pomocí rozhraní REST API s využitím Pythonu](quickstarts/python-train-extract.md)

Jsme bezplatnou službu pro účely výuky doporučujeme, ale mějte na paměti, že počet volných stránek je omezený na 500 stránky za měsíc.

**Krok 3:** Projděte si následující rozhraní API pro trénování a extrakci strukturovanými daty z formulářů použití rozhraní API služby REST.

| REST API | Popis |
|-----|-------------|
| Trénování | Natrénovat nový model k analýze formulářů pomocí 5 formuláře ze stejného typu nebo prázdný formulář.  |
| Analýza  |Analýza předaný jako datový proud pro extrakci páry klíč hodnota a tabulek z formuláře pomocí vlastního modelu jednotlivý dokument.  |

Prozkoumejte [rozhraní REST API referenční dokument](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Ochrany osobních údajů a zabezpečení

Služba se nabízí jako [ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) služby Azure v rámci [Online podmínek použití služby](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Jako u všech kognitivních služeb vývojářům, kteří používají službu rozpoznávání formuláře měli vědět zásady společnosti Microsoft na zákaznická data. Zobrazit [stránku služeb Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na webu Microsoft Trust Center další informace.

## <a name="next-steps"></a>Další postup

Postupujte podle [rychlý Start](quickstarts/curl-train-extract.md) a začněte využívat [rozhraní API pro rozpoznávání úseků](https://aka.ms/form-recognizer/api).
