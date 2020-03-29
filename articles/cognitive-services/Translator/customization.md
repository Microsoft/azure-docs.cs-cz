---
title: Přizpůsobení překladu – překladač textového rozhraní API
titleSuffix: Azure Cognitive Services
description: Pomocí centra Microsoft Translator Hub můžete vytvořit vlastní systém strojového překladu pomocí preferované terminologie a stylu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71257624"
---
# <a name="customize-your-text-translations"></a>Přizpůsobení textových překladů

Microsoft Custom Translator je funkce služby Microsoft Translator, která umožňuje uživatelům přizpůsobit pokročilý překlad neurální stroj Microsoft Translator při překladu textu pomocí překladače text API (pouze verze 3).

Tuto funkci lze také použít k přizpůsobení překladu řeči při použití s [funkcí Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

S vlastní překladač, můžete vytvářet neuronové překladové systémy, které rozumí terminologii používané ve vašem vlastním podnikání a průmyslu. Přizpůsobený překladový systém se pak integruje do stávajících aplikací, pracovních postupů a webových stránek.

### <a name="how-does-it-work"></a>Jak to funguje?

Použijte dříve přeložené dokumenty (letáky, webové stránky, dokumentaci atd.) k vytvoření překladatelského systému, který bude odrážet terminologii a styl specifický pro vaši doménu, lepší než standardní překladatelský systém. Uživatelé mohou nahrávat dokumenty TMX, XLIFF, TXT, DOCX a XLSX.  

Systém také přijímá data, která jsou paralelní na úrovni dokumentu, ale ještě nejsou zarovnána na úrovni věty. Pokud mají uživatelé přístup k verzím stejného obsahu ve více jazycích, ale v samostatných dokumentech, bude vlastní překladač schopen automaticky spárovat věty mezi dokumenty.  Systém může také použít jednojazyčná data v jednom nebo obou jazycích k doplnění paralelních trénovacích dat ke zlepšení překladů.

Přizpůsobený systém je pak k dispozici prostřednictvím pravidelného volání rozhraní Microsoft Translator Text API pomocí parametru kategorie.

Vzhledem k příslušnému typu a množství trénovacích dat není neobvyklé očekávat zisky mezi 5 a 10, nebo dokonce více bodů BLEU na kvalitě překladu pomocí vlastního překladače.

Další podrobnosti o různých úrovních přizpůsobení na základě dostupných údajů naleznete v [uživatelské příručce custom translator .](https://aka.ms/CustomTranslatorDocs)


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> Starší verze centra Microsoft Translator Hub bude vyřazena v květnu 17, 2019. [Zobrazení důležitých informací o migraci a kalendářních dat](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Vlastní překladač versus rozbočovač

|   | **Rozbočovač** | **Custom Translator**|
|:-----|:----:|:----:|
|Stav funkce přizpůsobení   | Obecná dostupnost  | Obecná dostupnost |
| Verze textového rozhraní API  | Pouze V2   | Pouze V3 |
| Přizpůsobení SMT | Ano   | Ne |
| Přizpůsobení NMT | Ne    | Ano |
| Nové jednotné přizpůsobení služeb rozpoznávání řeči | Ne    | Ano |
| [Bez trasování](https://www.aka.ms/notrace) | Ano  | Ano |

## <a name="collaborative-translations-framework"></a>Rámec pro kolaborativní překlady

> [!NOTE]
> února 2018, AddTranslation() a AddTranslationArray() již nejsou k dispozici pro použití s překladačem text API V2.0. Tyto metody se nezdaří a nic nebude zapsáno. Translator Text API V3.0 nepodporuje tyto metody.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení přizpůsobeného jazykového systému pomocí vlastního překladače](https://aka.ms/CustomTranslatorDocs)
