---
title: Funkce
titleSuffix: Language Understanding - Azure Cognitive Services
description: Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 89d18ebd2f52467a19a76940044fea3ae254970a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770159"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Seznam frázi je součástí aplikace LUIS

Ve službě machine learning *funkce* rozlišovací vlastností nebo atribut data, která dodržuje vašeho systému. 

Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat. Funkce pomáhají LUIS rozpoznat záměry a entity, ale funkce nejsou záměry a entity sami. Místo toho funkce může poskytnout příklady jejími podmínkami.  

## <a name="what-is-a-phrase-list-feature"></a>Co je součástí seznamu frází?
Seznam frázi je seznam slova nebo fráze, které jsou důležité pro vaši aplikaci, takže než jiná slova v projevy. Fráze seznamu se přidá do slovníku domény aplikace jako další signál k LUIS o těchto slov. Služba LUIS dozvídá o jeden z nich se automaticky využije na ostatní také. Tento seznam není uzavřená [seznam entit](luis-concept-entity-types.md#types-of-entities) přesný text odpovídá.

Fráze seznamy vám s slovního rozboru, budete muset přidat utterance příklady použití širokou škálu slovního rozboru pro všechny důležité slovník slova a slovní spojení.

## <a name="phrase-lists-help-all-models"></a>Seznamy frázi pomohou všechny modely

Fráze seznamy nejsou připojeny na specifické cílem nebo entity, ale jsou přidány jako výrazné zvýšení výkonu pro všechny záměry a entity. Jeho účelem je zlepšit záměru klasifikace zjišťování a entity.

## <a name="how-to-use-phrase-lists"></a>Použití seznamů fráze

Vytvořte seznam frází, když slova nebo fráze, které jsou důležité pro aplikace, jako má vaše aplikace:

* oborových termínech
* slang
* zkratky
* jazyka specifického pro společnost
* jazyk, který je z jiného jazyka, ale často používaných v aplikaci
* klíčová slova a frází ve vaší příklad projevy

Po zadání několika slova nebo fráze, použijte **doporučujeme** funkce k vyhledání souvisejících hodnot. Zkontrolujte související hodnoty před přidáním do seznamu hodnoty frázi.

|Typ seznamu|Účel|
|--|--|
|Zaměňovat|Synonyma nebo slova, že při změně do jiného slova v seznamu, mají stejné záměr a extrakce entity.|
|Non zaměnitelné|Slovník aplikace, specifické pro vaši aplikaci více, než obecně jiné slov v daném jazyce.|

### <a name="interchangeable-lists"></a>Zaměňovat seznamy

*Zaměnitelné* je frázi seznam hodnot, které jsou synonyma. Pokud chcete najít všechny obsahy vody a máte projevy příklad například: 

* Jaké měst jsou blízko Vysočina? 
* Jaké silniční spustí podél Lake Havasu?
* Pokud Nil počáteční a koncové? 

Každý utterance byste měli určit záměr a entit bez ohledu na text vody: 

* Jaké měst jsou blízko [bodyOfWater]?
* Jaké silniční spustí podél [bodyOfWater]?
* Pokud [bodyOfWater] počáteční a koncové? 

Vzhledem k tomu, že slova nebo fráze těla vody je synonymní a můžou používat Zaměnitelně v projevy, použít **Interchangeable** nastavení v seznamu frázi. 

### <a name="non-interchangeable-lists"></a>Non zaměnitelné seznamy

Signál, který zvyšuje zjišťování, aby se služba LUIS je seznam-zaměnitelné frázi. Seznam frází označuje slova nebo fráze, které jsou mnohem závažnější, další slova. To pomáhá při obou určování zjišťování záměr a entity. Řekněme například, že máte domény předmětu jako cesty, který je globální (tj. napříč jazykovými verzemi, ale stále v jediném jazyce). Existují slova a slovní spojení, které jsou důležité pro aplikace, ale nejsou totožná. 

Další příklad použijte seznam bez zaměnitelné frázi vzácné, proprietární a cizí slova. Služba LUIS možná nebudete moct rozpoznat vzácné a proprietární slova, jakož i cizí slova (mimo jazykovou verzi aplikace). Zaměnitelné nastavení znamená, že sadu výjimečných slova tvoří třídu, která by měla služba LUIS učení se rozpoznávání, ale nejsou synonyma nebo mezi sebou vzájemně zaměnitelné.

Nezadávejte přidat všechny možné slova nebo fráze do seznamu frázi, přidat několik slova nebo fráze v době, pak přeučování a publikovat. 

S růstem seznamu frázi v čase, může být pro vás termíny, které se mají celou řadu forem (synonym). Rozdělte tyto do jiného seznamu frázi, která je zaměnitelné. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Fráze uvádí nápovědy identifikovat jednoduchých zaměnitelné entit
Zaměňovat frázi seznamy jsou dobrým způsobem, jak optimalizovat výkon vaší aplikace LUIS. Pokud má vaše aplikace potíže při predikci projevy na správné záměr nebo rozpoznávání entit, zamyslete se, jestli projevy obsahovat neobvyklé slova ani slova, která může být nejednoznačný ve smyslu. Tato slova jsou vhodnými kandidáty zahrnout do seznamu frázi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fráze uvádí nápovědy identifikovat záměry lepší porozumění kontextu
Seznam frázi není instrukce k LUIS provést odpovídající strict nebo vždy označit všechny podmínky v seznamu frázi stejně. Je jednoduše nápovědu. Například můžete mít seznam frázi, která označuje, že "Patti" a "Selma" jsou názvy, ale LUIS můžete stále použít kontextové informace k rozpoznání něco jiného v znamenají "Provést rezervaci 2 ve vaší Patti Diner večeře" a "Nepracuju, řízení Pokyny k Selma, Gruzie". 

Přidání seznamu frázi je alternativa k přidání další příklad projevy záměru. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kdy použít seznamy frázi a seznam entit
Zatímco frázi seznamu a seznam entit může mít vliv na projevy přes všechny záměrů, mají funkce to jiným způsobem. Pomocí seznamu frázi vliv na skóre záměru předpovědi. Použijte seznam entit ovlivnit extrakce entity shodu přesný text. 

### <a name="use-a-phrase-list"></a>Použijte seznam fráze
Se seznamem frázi LUIS stále vezměte v úvahu kontext a generalizace k identifikaci položky, které jsou podobné, ale není přesná shoda, jako položky v seznamu. Pokud potřebujete aplikaci LUIS tak, aby mohli generalize a identifikaci nových položek v kategorii, použijte seznam frázi. 

Pokud chcete být schopni rozpoznat nové instance entity, jako je plánovače schůzku, který by měl rozpoznávat názvy nových kontaktů, nebo aplikaci inventář, který by měl rozpoznat nové produkty, použijte jiný typ počítače zjistili entitu, jako je jednoduchý nebo hierarchické entity. Pak vytvořte frázi seznam slova a slovní spojení, která pomáhá LUIS, hledání podobně jako v entitě jiná slova. Tento seznam provede LUIS rozpoznat příklady entity tak, že přidáte další význam hodnotu z těchto slov. 

Fráze seznamy jsou jako slovník jazyka specifického pro doménu, která usnadní vylepšení kvality znalost záměry a entity. Běžné použití seznamu frázi je podstatná jména správných například názvy měst. Název města, může být několik slova, včetně pomlček nebo apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nepoužívejte seznam frází 
Seznam entit explicitně definuje každá hodnota může trvat entity a identifikuje pouze hodnoty, které přesně odpovídají. Seznam entit může být vhodný pro aplikaci, ve kterém je známo, že všechny instance entity a neměnit často. Příklady jsou potravin položky v nabídce restaurace, která mění jen zřídka. Pokud potřebujete přesný text výskyty entity, nepoužívejte seznam frázi. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

Zobrazit [přidat funkce](luis-how-to-add-features.md) získat další informace o tom, jak přidat funkce do vaší aplikace LUIS.
