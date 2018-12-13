---
title: Přizpůsobení překladu – Translator Text API
titlesuffix: Azure Cognitive Services
description: Pomocí Microsoft Translatoru Hub můžete vytvářet strojový překlad systému pomocí upřednostňované terminologie a stylu.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: f02c495c7448334529de2d5b42cda02206daea0d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877968"
---
# <a name="customize-your-text-translations"></a>Upravte svoje textové překlady

Microsoft Translatoru Custom je funkce služby Microsoft Translator, který umožňuje uživatelům přizpůsobit Microsoft Translatoru pokročilé Neurální strojový překlad, při překladu textu s použitím rozhraní Translator Text API (pouze verze 3).

Funkci lze použít také k přizpůsobení překladu řeči při použití s [Cognitive Services řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

S vlastní překlady můžete vytvořit Neurální překladové systémy, které rozuměli technologiím použitým v obchodních a oboru. Systém přizpůsobených překladových pak integrovat do stávajících aplikací, pracovní postupy a websites.

### <a name="how-does-it-work"></a>Jak to funguje?

Použijte dříve přeložené dokumentů (letáků, webové stránky, dokumentaci, atd.) na sestavovací systém překladu, který odráží specifického pro doménu terminologie a stylu, lepší výsledky než obecné překladový systém. Uživatelé můžou nahrávat dokumenty, TMX XLIFF, TXT, DOCX a XLSX.  

Systém přijímá také data, která je paralelní na úrovni dokumentu, ale zatím není zarovnána na úrovni vět. Pokud mají uživatelé přístup k verzím stejný obsah ve více jazycích, ale v samostatných dokumentech vlastní Translator budou moci automaticky odpovídat věty v dokumentech.  Systém můžete také jeden jazyk data v těchto jazycích k doplnění paralelní trénovacích dat do vylepšovat překlady.

Přizpůsobený systém je pak k dispozici prostřednictvím pravidelné volání parametr kategorie pomocí rozhraní Microsoft Translator Text API.

Zadaný odpovídající typ a velikost trénovacích dat není, můžete očekávat zvýšení rozsahu 5 až 10 nebo ještě více BLEU odkazuje na kvalitu překladu s použitím překladač vlastní.

Další podrobnosti o různých úrovních přizpůsobení na základě dostupných dat najdete v [uživatelská příručka k vlastní Translator](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Centrum Microsoft Translatoru

Starší verze centra Microsoft Translator umožňuje překládat statistické strojového překladu. [Další informace](https://www.microsoft.com/en-us/translator/hub.aspx)

## <a name="custom-translator-versus-hub"></a>Vlastní Translator oproti centra

|   | **Centrum** | **Vlastní Translator**|
|:-----|:----:|:----:|
|Stav funkce vlastního nastavení   | Všeobecná dostupnost  | Všeobecná dostupnost |
| Verze text API  | Pouze v2   | Pouze v3 |
| Přizpůsobení SMT | Ano   | Ne |
| Přizpůsobení NMT | Ne    | Ano |
| Nové sjednocené přizpůsobení služby řeči | Ne    | Ano |
| [Bez trasování](https://www.aka.ms/notrace) | Ano  | Ano |

## <a name="collaborative-translations-framework"></a>Framework spolupráci překlady

> [!NOTE]
> Od 1. února 2018 je AddTranslation() a AddTranslationArray() již nejsou k dispozici pro použití s Translator Text API V2.0. Tyto metody se nezdaří a nic budou zapsány. Verze Translator Text API 3.0 nepodporuje tyto metody.

>Podobně jako funkce jsou dostupné v rozhraní Translator API rozbočovače. Zobrazit [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení vlastní jazyk systému pomocí Translatoru Custom](https://aka.ms/CustomTranslatorDocs)
