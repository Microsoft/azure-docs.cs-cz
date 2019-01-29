---
title: Osvědčené postupy – QnA Maker
titlesuffix: Azure Cognitive Services
description: Použijte tyto osvědčené postupy pro zlepšení znalostní báze a application/chatovací robot koncovým uživatelům poskytovat lepší výsledky.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 20b6089d74752fd4c3f9fa5c761113b0ae145ad6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55090588"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Doporučené postupy nástroje QnA Maker znalostní báze
[Životního cyklu vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás o tom, jak spravovat znalostní BÁZÍ od začátku do konce. Použijte tyto osvědčené postupy pro zlepšení znalostní báze a application/chatovací robot koncovým uživatelům poskytovat lepší výsledky.

## <a name="extraction"></a>Extrakce
Služba QnA Maker se neustále se zlepšovat algoritmy, které extrahují maximálně z obsahu a rozbalení seznamu podporovaných souborů a formátů HTML. Postupujte podle [pokyny](../Concepts/data-sources-supported.md) pro extrakci dat podle typu dokumentu. 

Obecně platí nejčastější dotazy k stránky by měl být samostatný a ne kombinované spolu s dalšími informacemi. Produktových příruček by měl mít vymazat záhlaví a pokud možno indexovou stránku. 

## <a name="chit-chat"></a>Chit chatu
Přidat do svého robota, aby váš robot konverzační a zajímavější, chit chat s nízké úsilí. Můžete snadno přidat chit chat datových sad pro 3 předem definovaných osobnosti při vytváření znalostní BÁZÍ a kdykoli změnit. Zjistěte, jak [přidat chit chat znalostní BÁZÍ](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Výběr posouzení vašich osobnostních
Chit konverzace je podporována pro 3 předdefinované osobnosti: 

|Osobnosti|
|--|
|Profesionály|
|Spřátelené|
|Comic|

Rozsah odpovědi z formální neformální a zlehčující. Měli byste vybrat charakteru, který je nejblíž v souladu s tón, které chcete pro svého robota. Můžete zobrazit datové sady a vyberte ten, který slouží jako základ pro vašeho robota a potom přizpůsobte odpovědi. 

### <a name="edit-bot-specific-questions"></a>Upravit otázky týkající se robota
Existují některé otázky týkající se robota, které jsou součástí sady dat chit chatu a bylo vyplněno pomocí obecné odpovědi. Změňte tyto odpovědi, aby odrážely nejlépe vašeho robota podrobnosti. 

Doporučujeme následující maximálně chit chat konkrétnější:

* Kdo jste?
* Co můžete dělat?
* Kolik je vám let?
* Kdo vytvořil jste?
* Dobrý den,
   

## <a name="rankingscoring"></a>Pořadí a vyhodnocování
Ujistěte se, že provádíte co nejlíp využít hodnocení funkce, které podporuje QnA Maker. To zvýší pravděpodobnost, která daný uživatelský dotaz je zodpovězen odpovídající odpověď.

### <a name="choosing-a-threshold"></a>Výběr prahové hodnoty
Výchozí skóre spolehlivosti, který se používá jako prahová hodnota je 50, ale můžete ho změnit pro znalostní BÁZÍ podle svých potřeb. Protože každý KB se liší, by měl test a zvolte prahovou hodnotu, která je nejlepší vhodné pro vaše KB. Další informace najdete [skóre spolehlivosti](../Concepts/confidence-score.md). 


### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní dotazy](../How-To/edit-knowledge-base.md) zvýšit pravděpodobnost, že shoda se dotaz uživatele. Alternativní dotazy jsou užitečné, pokud existuje více způsobů, ve kterém se dotaz na stejnou otázku. To může zahrnovat změny ve struktuře věty a stylu aplikace word.

|Původní dotaz|Alternativních dotazů|Změnit| 
|--|--|--|
|Je parkovací k dispozici?|Máte car park?|Struktura větu|
 |Ahoj|Jo<br>Dobrý den existuje!|Word – vizuální styl nebo slang|

<a name="#use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Použití značek metadata do filtru otázek a odpovědí

[Metadata](../How-To/edit-knowledge-base.md) umožňuje zúžit výsledky dotazu uživatele na základě metadat značek. Odpověď znalostní báze se může lišit podle značky metadat i v případě, že dotaz je stejný. Například *"kde je umístěn parkovací"* může mít různé odpovědi, pokud umístění restaurace větve se liší – to znamená, metadata jsou *umístění: Seattle* oproti *umístění: Redmond*.

### <a name="use-synonyms"></a>Použití synonym
Zatímco některé podpora synonym v angličtině, použijte [word rozšiřuje](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) synonyma přidáte klíčová slova, které mají jiný formát. Synonyma by měl přidány na úrovni služby QnA Maker a sdílí všechny znalostních bází ve službě.

|Původní aplikace word|Synonyma|
|--|--|
|Koupit|koupit<br>netbanking<br>NET bankovnictví|

### <a name="use-distinct-words-to-differentiate-questions"></a>Používat různá slova k rozlišení dotazy
Nástroj QnA Maker porovnání a řazení algoritmy, odpovídající uživatelský dotaz se dotaz znalostní báze knowledge base, fungují nejlépe, pokud každý dotaz adres různých věcí a potřebovali. Opakování téhož slova nastaven v rozmezí dotazy snižuje pravděpodobnost, že je vybrána správná odpověď pro daný uživatelský dotaz se tato slova. 

Například může mít maximálně dvě samostatné se na následující otázky:

|Maximálně|
|--|
|Pokud je parkovací *umístění*|
|kde je atm *umístění*|

Protože se velmi podobá slov obsahuje jiné spojení těchto dvou maximálně, tento podobnosti by mohlo způsobit velmi podobné výsledky pro mnoho dotazy, které jsou obsahuje jiné spojení, jako je *"kde je `<x>` umístění"*. Místo toho zkuste jasně rozlišit pomocí dotazů jako *"kde je velké parkovací"* a *"kde je atm"*, vyhnout slova, jako je "umístění", který může být používán spoustu otázek v znalostní BÁZÍ. 


## <a name="collaborate"></a>Spolupráce
Nástroj QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) ve znalostní bázi. Uživatelé potřebovat přístup ke skupině prostředků Azure QnA Maker za účelem přístupu k znalostních bází. Některé organizace chtít externí pomocí úpravy znalostní báze knowledge base a údržba a stále mít možnost chránit přístup k jejich prostředky Azure. Tento model schvalovatele editoru se provádí nastavením dva identické [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a výběrem jedné pro cyklus úpravy testování. Po dokončení testování se přenáší obsah znalostní báze s [importu a exportu](../Tutorials/migrate-knowledge-base.md) zpracovat služba QnA Maker schvalovatele, který bude nakonec publikovat znalostní báze a aktualizujte koncový bod.

## <a name="active-learning"></a>Aktivní vzdělávání

[Aktivní učení](../How-to/improve-knowledge-base.md) je nejvhodnější navrhnout alternativní otázky, pokud obsahuje širokou škálu kvalitě a množství dotazů založené na uživatelích. Je potřeba povolit dotazy na klienta aplikací pro uživatele k účasti na aktivního učení smyčku zpětné vazby bez cenzurou.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)
