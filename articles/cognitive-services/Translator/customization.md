---
title: Přizpůsobení překlad Microsoft překladač Text rozhraní API | Microsoft Docs
description: Pomocí centra společnosti Microsoft překladač sestavení strojový překlad systému pomocí upřednostňované terminologie a stylu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: 1db22a414c41f338c4e7fd6ce9dc7ac739fa9237
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343559"
---
# <a name="customize-your-text-translations"></a>Přizpůsobit své překlady textu

Ve verzi preview Microsoft vlastní překladač je funkce služby Microsoft Translator, což umožňuje uživatelům přizpůsobení Microsoft Translator pokročilé neuronové strojový překlad, při překladu textu s použitím rozhraní API Text překladač Microsoft (pouze verze 3). 

Tuto funkci lze také přizpůsobit rozpoznávání řeči překlad při použití s [kognitivní služby řeči preview](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Vlastní překladač
S vlastní překladač můžete vytvořit neuronové překlad systémy, které rozuměli technologiím použitým v vlastní obchodní a odvětví. Systém přizpůsobené překlad pak integrovat do stávajících aplikací, pracovní postupy a weby. 

### <a name="how-does-it-work"></a>Jak to funguje?
Použijte dříve přeložený dokumentů (letáky, webové stránky, dokumentace atd.) k vytvoření překlad systém, který lépe než systém obecné překlad odráží specifické pro doménu terminologie a styl. Uživatelé mohou odesílat dokumenty TMX, XLIFF, TXT, DOCX a XLSX.  

Systém přijímá také data, která je paralelní na úrovni dokumentu, ale ještě není zarovnána na úrovni věty. Pokud mají uživatelé přístup k verzím stejný obsah v několika jazycích, ale v samostatných dokumentech vlastní překladač bude moct automaticky odpovídat věty na dokumentech.  Systém můžete data také využívají jeden jazyk v jazyce nebo oba aby doplňovala paralelní Cvičná data ke zlepšení překlady. 

Pak je k dispozici prostřednictvím regulární volání rozhraní API Microsoft překladač Text pomocí parametru kategorie přizpůsobený systém.

Zadaný příslušného typu a množství dat, školení není, můžete očekávat zvýšení rozsahu 5 až 10 nebo i další BLEU odkazuje na kvalitu překladu pomocí vlastní překladač.

Další podrobnosti o různých úrovní přizpůsobení na základě dostupných dat najdete v [vlastní překladač uživatelská příručka](http://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft překladač rozbočovače

Starší verze překladač centra společnosti Microsoft lze přeložit statistické strojového překladu. [Další informace](https://www.microsoft.com/en-us/translator/hub.aspx) 

## <a name="custom-translator-versus-hub"></a>Vlastní překladač versus rozbočovače

|   | **Centrum** | **Vlastní překladač**|
|:-----|:----:|:----:|
|Stav funkce přizpůsobení   | Všeobecná dostupnost  | Preview |
| Text rozhraní API verze  | Pouze v2   | Pouze v3 |
| Přizpůsobení SMT | Ano   | Ne | 
| Přizpůsobení NMT | Ne    | Ano |
| Nové sjednocené přizpůsobení služby řeči | Ne    | Ano | 
| [Žádné trasování](http://www.aka.ms/notrace) | Ano   | Ano | 

## <a name="collaborative-translations-framework"></a>Spolupráce překlady Framework

> [!NOTE]
> Od verze 1. února 2018 AddTranslation() a AddTranslationArray() již nejsou k dispozici pro použití s V2.0 překladač Text rozhraní API. Tyto metody se nezdaří a nic se zapíšou. V3.0 překladač Text rozhraní API nepodporuje tyto metody.

>Podobné funkce je k dispozici v rozhraní API překladač rozbočovače. V tématu [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger). 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Nastavit vlastní jazyk systému pomocí vlastní překladač](http://aka.ms/CustomTranslatorDocs)
