---
title: Pochopili funkcí v LEOŠ aplikace v Azure | Microsoft Docs
description: Další informace o funkcích, které pomoct zlepšit výkon LEOŠ aplikace. Funkce zahrnují frázi seznamy a vzory pro rozpozná regulární výrazy.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35345999"
---
# <a name="phrase-list-features-in-luis"></a>Seznam funkcí frázi v LEOŠ

Ve strojovém učení se *funkce* je rozlišovací znak nebo atribut dat, které dodržuje systému. 

Přidání funkce do modelu jazyk zajistit pomocné parametry o tom, jak rozpoznat vstup, který chcete popisku nebo klasifikovat. Funkce pomoci LEOŠ rozpoznat tříd Intent a entity, ale funkce nejsou záměry nebo entity sami. Místo toho může funkce obsahují příklady související podmínky.  

## <a name="what-is-a-phrase-list-feature"></a>Co je funkce seznamu frázi?
Seznam frázi obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí být považované podobně (například názvy města nebo produkty). Co LEOŠ dozví o jeden z nich je automaticky použita pro ostatní také. Toto není uzavřenou [seznamu entity](luis-concept-entity-types.md#types-of-entities) (přesnou text odpovídá) odpovídající slov.

Seznam frázi přidá do slovníku doména aplikace jako druhý signál k LEOŠ o tato slova.

## <a name="how-to-use-phrase-lists"></a>Použití seznamů fráze
V aplikaci agenta cesta vytvořte seznam frázi s názvem "Města", obsahující hodnoty, Londýn, Paříž a Káhiře. Pokud jeden z těchto hodnot označovat jako jednoduché entity v [příklad utterance](luis-how-to-add-example-utterances.md#add-simple-entity-label) v záměrem, LEOŠ zjišťuje ostatní rozpoznat. 

Seznam frázi může být zaměňovat nebo zaměnitelné. *Zaměňovat* je frázi seznam hodnot, které jsou synonyma, a *-zaměňovat* frázi seznam je určený pro hodnoty, které nejsou synonyma, ale jsou podobné jiným způsobem. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Fráze uvádí nápovědy identifikovat jednoduché exchangeable entity
Exchangeable frázi seznamy jsou vhodné pro optimalizaci výkonu LEOŠ aplikace. Pokud vaše aplikace obsahuje řešení problémů predikci utterances do správné záměr nebo rozpozná entity, rozmyslete si, jestli utterances obsahovat neobvyklou slova nebo slova, která může být v význam nejednoznačný. Slova jsou vhodnými kandidáty pro zahrnutí do seznamu frázi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fráze uvádí nápovědy identifikovat záměry lepší pochopení kontext
Pomocí seznamů frázi pro taková situace vzácná, proprietární a cizí slova. LEOŠ může nemůže rozpoznat výjimečná a vlastnické slova, jakož i cizí slova (mimo jazykovou verzi aplikace), a proto musí být přidaní do seznamu frázi. Tento seznam frázi by měl být označen jiný zaměňovat, označíte, že sada výjimečných slova forms třídu, která by měl LEOŠ další rozpoznat, ale nejsou synonyma nebo zaměňovat mezi sebou.

Seznam frázi není instrukce k LEOŠ provést striktní odpovídající nebo vždy popisku všechny podmínky v seznamu frázi stejně. Je jednoduše nápovědu. Například můžete mít seznam frázi, která označuje, že "Patti" a "Selma" jsou názvy, ale LEOŠ můžete dál používat kontextové informace rozpoznat jejich znamenat něco jiného v "Provést rezervaci 2 na Diner na Patti večeře" a "pět me řídí Pokyny k Selma, Georgie". 

Přidávání seznamu frázi představuje alternativu k přidání další příklad utterances do záměrem. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kdy použít seznamy frázi oproti seznamu entit
Při seznam frázi a seznamu entit může mít vliv na utterances napříč všechny záměry, každý tomu jiným způsobem. Použijte seznam frázi ovlivnit skóre záměrné předpovědi. Pomocí seznamu entity ovlivnit entity extrakce text přesnou shodu. 

### <a name="use-a-phrase-list"></a>Použijte seznam fráze
S seznam frázi LEOŠ stále vezměte v úvahu kontextu a generalize k identifikaci položek, které jsou podobné, ale ne přesnou shodu, jako položky v seznamu. Pokud potřebujete LEOŠ aplikaci, aby mohli generalize a identifikaci nových položek v kategorii, použijte seznam frázi. 

Pokud chcete být schopni rozpoznat nové instance entity, jako plánovače schůzky, který by měl rozpoznat názvy nové kontakty nebo inventáře aplikaci, která by měla rozpoznat nové produkty, použít jiný typ počítač naučili entity, jako je například jednoduchou nebo Hierarchická entity. Pak vytvořte seznam slova slovní spojení a fráze, které pomáhá LEOŠ vyhledejte jiná slova podobné entity. Tento seznam provede LEOŠ rozpoznat příklady entity přidáním dalších násobek. hodnota tato slova. 

Seznamy frázi se jako slovník specifické pro doménu, která usnadní zlepšení kvality pochopení tříd Intent a entity. Použití běžných frázi seznamu je správné podstatná jména, například města názvy. Název města může být několik slova, včetně pomlček nebo apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nepoužívejte seznam fráze 
Seznam entity explicitně definuje každé hodnotě entity, může trvat a identifikuje pouze hodnoty, které se přesně shodují. Entity seznamu může být vhodné pro aplikaci, ve kterém se ví, že všechny instance entity a jako jídlo položky v nabídce restaurace, která se zřídka mění často neměnit. Pokud potřebujete entity v případě shody přesný text, nepoužívejte seznam frázi. 

## <a name="best-practices"></a>Osvědčené postupy
Další informace [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

V tématu [přidat funkce](luis-how-to-add-features.md) Další informace o tom, jak přidat do aplikace LEOŠ funkce.