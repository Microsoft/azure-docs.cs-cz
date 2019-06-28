---
title: Osvědčené postupy – QnA Maker
titlesuffix: Azure Cognitive Services
description: Použijte tyto osvědčené postupy pro zlepšení znalostní báze a application/chatovací robot koncovým uživatelům poskytovat lepší výsledky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c796114d124c64ac1c373baacabe00c7dcd70aa7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447631"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Doporučené postupy nástroje QnA Maker znalostní báze

[Životního cyklu vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás o tom, jak spravovat znalostní BÁZÍ od začátku do konce. Použijte tyto osvědčené postupy pro zlepšení znalostní báze a poskytují lepší výsledky do klientské aplikace nebo chat bodu robotů také koncovým uživatelům.

## <a name="extraction"></a>Extrakce

Služba QnA Maker se neustále se zlepšovat algoritmy, které extrahují maximálně z obsahu a rozbalení seznamu podporovaných souborů a formátů HTML. Postupujte podle [pokyny](../Concepts/data-sources-supported.md) pro extrakci dat podle typu dokumentu. 

Obecně platí nejčastější dotazy k stránky by měl být samostatný a ne kombinované spolu s dalšími informacemi. Produktových příruček by měl mít vymazat záhlaví a pokud možno indexovou stránku. 

### <a name="configuring-multi-turn"></a>Konfigurace více zapnout

Vytvořte znalostní bázi s více zapnout extrakce povolena. Pokud znalostní báze nemá nebo by měly podporovat hierarchie otázek, může tato hierarchie extrahují z dokumentu nebo vytvořené po dokumentu je extrahován. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Vytváří se správné otázky a odpovědi

### <a name="good-questions"></a>Správné otázky

Nejlepší dotazy se dají snadno. Vezměte v úvahu klíčové slovo nebo slovní spojení na každou otázku pak vytvořte na jednoduchou otázku pro toto klíčové slovo nebo fráze. 

Přidejte tolik alternativní otázky, jako třeba ale zjednodušení změny. Přidání více slov nebo frází, které nejsou součástí hlavním cílem otázky nepomůže QnA Maker najít shoda. 


### <a name="add-relevant-alternative-questions"></a>Přidat alternativní relevantní otázky

Uživatel může zadat otázky pomocí dotazu v přirozeném stylu textu, `How do I add a toner cartridge to my printer?` nebo klíčové slovo jako například hledat `toner cartridge`. Znalostní báze by měl mít obou stylů otázky, aby bylo možné správně vrátí nejlepší odpověď. Pokud si nejste jistí, jaká klíčová slova zákazníka přechází, použijte k analýze dotazy na data Application Insights.

### <a name="good-answers"></a>Správné odpovědi

Nejlepší odpovědi jsou jednoduché odpovědi, ale příliš jednoduché. Nepoužívejte například odpovědi `yes` a `no`. Pokud vaše odpověď by měla propojit s dalšími zdroji nebo poskytují bohaté uživatelské prostředí s médii a odkazy, použijte [označování metadat](./knowledge-base.md#key-knowledge-base-concepts) rozlišovat mezi odpovědi, pak [odeslat dotaz](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) s značky metadat v `strictFilters` vlastnost verzi správné odpovědi.

## <a name="chit-chat"></a>Chit chatu
Přidat do svého robota, aby váš robot konverzační a zajímavější, chit chat s nízké úsilí. Můžete snadno přidat chit chat datových sad z předem definovaných osobnosti při vytváření znalostní BÁZÍ a kdykoli změnit. Zjistěte, jak [přidat chit chat znalostní BÁZÍ](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Výběr posouzení vašich osobnostních
Chit konverzace je podporována pro několik předdefinovaných osobnosti: 

|Posouzení vašich osobnostních |Soubor datové sady QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Popisný |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Velmi se zajímám |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Rozsah odpovědi z formální neformální a zlehčující. Měli byste vybrat charakteru, který je nejblíž v souladu s tón, které chcete pro svého robota. Můžete zobrazit [datových sad](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)a vyberte ten, který slouží jako základ pro vašeho robota a upravte odpovědi. 

### <a name="edit-bot-specific-questions"></a>Upravit otázky týkající se robota
Existují některé otázky týkající se robota, které jsou součástí sady dat chit chatu a bylo vyplněno pomocí obecné odpovědi. Změňte tyto odpovědi, aby odrážely nejlépe vašeho robota podrobnosti. 

Doporučujeme následující maximálně chit chat konkrétnější:

* Kdo jste?
* Co můžete dělat?
* Kolik je vám let?
* Kdo vytvořil jste?
* Dobrý den,
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Přidání vlastní chit chat s značky metadat

Pokud chcete přidat vlastní dvojice QnA chit chatu, ujistěte se, že jste přidali metadata, proto se vrátí tyto odpovědi. Dvojice název/hodnota metadat `editorial:chitchat`.

## <a name="searching-for-answers"></a>Hledání odpovědí

Rozhraní API GenerateAnswer využívá otázky a odpovědi k vyhledání nejlepších odpovědi na dotaz uživatele.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Hledání dotazy pouze v případě, že odpověď se nevztahuje

Použití [ `RankerType=QuestionOnly` ](#choosing-ranker-type) Pokud nechcete hledat odpovědi. 

Příkladem je při znalostní báze je katalog zkratky jako dotazy s jejich úplný formát jako odpověď. Hodnota odpovědi nepomůže k vyhledání příslušné odpovědi.

## <a name="rankingscoring"></a>Pořadí a vyhodnocování
Ujistěte se, že provádíte co nejlíp využít hodnocení funkce, které podporuje QnA Maker. To zvýší pravděpodobnost, která daný uživatelský dotaz je zodpovězen odpovídající odpověď.

### <a name="choosing-a-threshold"></a>Výběr prahové hodnoty

Výchozí hodnota [skóre spolehlivosti](confidence-score.md#) , který se používá jako prahová hodnota je 50, ale můžete [změnit prahovou hodnotu](confidence-score.md#set-threshold) pro znalostní BÁZÍ podle svých potřeb. Protože každý KB se liší, by měl test a zvolte prahovou hodnotu, která je nejlepší vhodné pro vaše KB. 

### <a name="choosing-ranker-type"></a>Výběr typu klasifikátor
Nástroj QnA Maker se ve výchozím nastavení, prohledá otázek a odpovědí. Pokud chcete prohledávat pouze dotazy, generovat odpověď, použijte `RankerType=QuestionOnly` v textu POST GenerateAnswer požadavku.

### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní dotazy](../How-To/edit-knowledge-base.md) zvýšit pravděpodobnost, že shoda se dotaz uživatele. Alternativní dotazy jsou užitečné, pokud existuje více způsobů, ve kterém se dotaz na stejnou otázku. To může zahrnovat změny ve struktuře věty a stylu aplikace word.

|Původní dotaz|Alternativních dotazů|Změnit| 
|--|--|--|
|Je parkovací k dispozici?|Máte car park?|Struktura větu|
 |Ahoj|Jo<br>Dobrý den existuje!|Word – vizuální styl nebo slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Použití značek metadata do filtru otázek a odpovědí

[Metadata](../How-To/edit-knowledge-base.md) přidává možnost pro klientskou aplikaci znát ho nepřijímat všechny odpovědi ale místo toho můžete zúžit výsledky dotazu uživatele na základě metadat značek. Odpověď znalostní báze se může lišit podle značky metadat i v případě, že dotaz je stejný. Například *"kde je umístěn parkovací"* může mít různé odpovědi, pokud umístění restaurace větve se liší – to znamená, metadata jsou *umístění: Seattle* oproti *umístění: Redmond*.

### <a name="use-synonyms"></a>Použití synonym
Zatímco některé podpora synonym v angličtině, použijte malá a velká písmena slov změny prostřednictvím [změny rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) synonyma přidáte klíčová slova, které mají jiný formát. Synonyma jsou přidány na úrovni služby QnA Maker a sdílí všechny znalostních bází ve službě.

|Původní aplikace word|Synonyma|
|--|--|
|Koupit|koupit<br>NET bankovnictví<br>NET bankovnictví|

### <a name="use-distinct-words-to-differentiate-questions"></a>Používat různá slova k rozlišení dotazy
Algoritmus řazení QnA Maker, odpovídající uživatelský dotaz se dotaz znalostní báze knowledge base, funguje nejlépe, když se každý dotaz adresy různých věcí a potřebovali. Opakování téhož slova nastaven v rozmezí dotazy snižuje pravděpodobnost, že je vybrána správná odpověď pro daný uživatelský dotaz se tato slova. 

Například může mít maximálně dvě samostatné se na následující otázky:

|Maximálně|
|--|
|Pokud je parkovací *umístění*|
|kde je ATM *umístění*|

Protože se velmi podobá slov obsahuje jiné spojení těchto dvou maximálně, tento podobnosti by mohlo způsobit velmi podobné výsledky pro mnoho dotazy, které jsou obsahuje jiné spojení, jako je *"kde je `<x>` umístění"* . Místo toho zkuste jasně rozlišit pomocí dotazů jako *"kde je velké parkovací"* a *"kde je ATM"* , vyhnout slova, jako je "umístění", který může být používán spoustu otázek v znalostní BÁZÍ. 

## <a name="collaborate"></a>Spolupráce
Nástroj QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) ve znalostní bázi. Uživatelé potřebovat přístup ke skupině prostředků Azure QnA Maker za účelem přístupu k znalostních bází. Některé organizace chtít externí pomocí úpravy znalostní báze knowledge base a údržba a stále mít možnost chránit přístup k jejich prostředky Azure. Tento model schvalovatele editoru se provádí nastavením dva identické [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a výběrem jedné pro cyklus úpravy testování. Po dokončení testování se přenáší obsah znalostní báze s [importu a exportu](../Tutorials/migrate-knowledge-base.md) zpracovat služba QnA Maker schvalovatele, který bude nakonec publikovat znalostní báze a aktualizujte koncový bod.



## <a name="active-learning"></a>Aktivní učení

[Aktivní učení](../How-to/improve-knowledge-base.md) je nejvhodnější navrhnout alternativní otázky, pokud obsahuje širokou škálu kvalitě a množství dotazů založené na uživatelích. Je potřeba povolit dotazy na klienta aplikací pro uživatele k účasti na aktivního učení smyčku zpětné vazby bez cenzurou. Po portálu QnA Maker jsou navrhované dotazy, můžete **[filtrovat podle návrhy](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** pak zkontrolovat a přijmout nebo odmítnout tyto návrhy. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)
