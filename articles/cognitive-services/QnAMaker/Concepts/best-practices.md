---
title: Osvědčené postupy – QnA Maker
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: bbd6c55337eb30c883ecbd542abbdfc0f842e3f9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563115"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Doporučené postupy nástroje QnA Maker znalostní báze

[Životního cyklu vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás o tom, jak spravovat znalostní BÁZÍ od začátku do konce. Tyto osvědčené postupy použijte k vylepšení znalostní báze a poskytují lepší výsledky klientským aplikacím nebo koncovým uživatelům robotů pro konverzaci.

## <a name="extraction"></a>Extrakce

Služba QnA Maker se neustále se zlepšovat algoritmy, které extrahují maximálně z obsahu a rozbalení seznamu podporovaných souborů a formátů HTML. Postupujte podle [pokyny](../Concepts/data-sources-supported.md) pro extrakci dat podle typu dokumentu. 

Obecně platí nejčastější dotazy k stránky by měl být samostatný a ne kombinované spolu s dalšími informacemi. Produktových příruček by měl mít vymazat záhlaví a pokud možno indexovou stránku. 

### <a name="configuring-multi-turn"></a>Konfigurace vícenásobného zapnutí

Vytvořte znalostní bázi s povoleným extrakcí pomocí vícenásobného vypínání. Pokud vaše znalostní báze dělá nebo by měla podporovat hierarchii otázek, můžete tuto hierarchii extrahovat z dokumentu nebo vytvořit po extrakci dokumentu. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Vytváření dobrých otázek a odpovědí

### <a name="good-questions"></a>Dobré otázky

Nejlepší otázky jsou jednoduché. Zvažte klíčové slovo nebo frázi pro každou otázku a pak vytvořte jednoduchou otázku pro toto klíčové slovo nebo frázi. 

Přidejte tolik alternativních otázek, kolik potřebujete, ale ponechte jednoduché změny. Přidání dalších slov nebo frází, které nejsou součástí hlavního cíle otázky, neumožňuje QnA Maker najít shodu. 


### <a name="add-relevant-alternative-questions"></a>Přidat relevantní alternativní otázky

Uživatel může zadávat dotazy buď pomocí konverzačního stylu textu, nebo pomocí klíčového slova `How do I add a toner cartridge to my printer?` `toner cartridge`, jako je například. Aby bylo možné správně vrátit nejlepší odpověď, musí mít znalostní báze obojí styly otázek. Pokud si nejste jistí, jaká klíčová slova zákazník zadá, použijte k analýze dotazů Application Insights dat.

### <a name="good-answers"></a>Dobré odpovědi

Nejlepší odpovědi jsou jednoduché odpovědi, ale nejsou příliš jednoduché. Nepoužívejte odpovědi, jako je `yes` a `no`. Pokud by vaše odpověď měla odkazovat na jiné zdroje nebo zajistit bohatou zkušenost s médii a odkazy, použijte [označování metadat](./knowledge-base.md#key-knowledge-base-concepts) k rozlišení mezi odpověďmi a pak [odešlete dotaz](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) s `strictFilters` značkami metadat ve vlastnosti, abyste získali správnou odpověď. znění.

## <a name="chit-chat"></a>Chit chatu
Přidat do svého robota, aby váš robot konverzační a zajímavější, chit chat s nízké úsilí. Můžete snadno přidat datové sady funkce CHITEST-chat z předem definovaných osobních údajů při vytváření a kdykoli je kdykoli změnit. Zjistěte, jak [přidat chit chat znalostní BÁZÍ](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Výběr posouzení vašich osobnostních
CHITEST – chat se podporuje pro několik předdefinovaných osobních důvodů: 

|Osobní |Soubor datové sady QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Vhodná |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Dostáváme |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Rozsah odpovědi z formální neformální a zlehčující. Měli byste vybrat charakteru, který je nejblíž v souladu s tón, které chcete pro svého robota. Můžete zobrazit [datové sady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)a zvolit jednu, která slouží jako základ pro robota, a pak přizpůsobit odpovědi. 

### <a name="edit-bot-specific-questions"></a>Upravit otázky týkající se robota
Existují některé otázky týkající se robota, které jsou součástí sady dat chit chatu a bylo vyplněno pomocí obecné odpovědi. Změňte tyto odpovědi, aby odrážely nejlépe vašeho robota podrobnosti. 

Doporučujeme následující maximálně chit chat konkrétnější:

* Kdo jste?
* Co můžete dělat?
* Kolik je vám let?
* Kdo vytvořil jste?
* Dobrý den,
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Přidání vlastního funkce CHITEST – chat se značkou metadat

Pokud přidáte vlastní páry funkce CHITEST-chat QnA, nezapomeňte přidat metadata, aby se tyto odpovědi vracely. Dvojice název/hodnota metadat je `editorial:chitchat`.

## <a name="searching-for-answers"></a>Hledání odpovědí

Rozhraní GenerateAnswer API používá otázky a odpověď k vyhledání nejlepších odpovědí na dotaz uživatele.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Hledání otázek pouze v případě, že odpověď není relevantní

Použijte v [`RankerType=QuestionOnly`](#choosing-ranker-type) případě, že nechcete hledat odpovědi. 

Příkladem je, že znalostní báze je katalog zkratek jako otázky s jejich úplnou formou jako odpověď. Hodnota odpovědi vám nepomůže vyhledat příslušnou odpověď.

## <a name="rankingscoring"></a>Pořadí a vyhodnocování
Ujistěte se, že provádíte co nejlíp využít hodnocení funkce, které podporuje QnA Maker. To zvýší pravděpodobnost, která daný uživatelský dotaz je zodpovězen odpovídající odpověď.

### <a name="choosing-a-threshold"></a>Výběr prahové hodnoty

Výchozí [hodnocení spolehlivosti](confidence-score.md) , které se používá jako prahová hodnota, je 50, ale v závislosti na vašich potřebách můžete [změnit prahovou hodnotu](confidence-score.md#set-threshold) pro vaši databázi. Protože každý KB se liší, by měl test a zvolte prahovou hodnotu, která je nejlepší vhodné pro vaše KB. 

### <a name="choosing-ranker-type"></a>Volba typu klasifikace
Ve výchozím nastavení QnA Maker vyhledává dotazy a odpovědi. Pokud chcete vygenerovat odpověď pouze `RankerType=QuestionOnly` v rámci otázek, použijte v těle žádosti GenerateAnswer v části post.

### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní dotazy](../How-To/edit-knowledge-base.md) zvýšit pravděpodobnost, že shoda se dotaz uživatele. Alternativní dotazy jsou užitečné, pokud existuje více způsobů, ve kterém se dotaz na stejnou otázku. To může zahrnovat změny ve struktuře věty a stylu aplikace word.

|Původní dotaz|Alternativních dotazů|Změnit| 
|--|--|--|
|Je parkovací k dispozici?|Máte car park?|Struktura větu|
 |Ahoj|Jo<br>Dobrý den existuje!|Word – vizuální styl nebo slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrování otázek a odpovědí pomocí značek metadat

[](../How-To/edit-knowledge-base.md) Díky metadatům může klientská aplikace zjistit, že by neměl přijímat všechny odpovědi, ale místo toho může zúžit výsledky dotazu uživatele na základě značek metadat. Odpověď znalostní báze se může lišit podle značky metadat i v případě, že dotaz je stejný. Například *"kde je* zaparkovaná síť" může mít jinou odpověď, pokud je umístění větve restaurace jiné – to znamená, že metadata jsou *umístění: V* Seattlu *versus umístění: Redmond*.

### <a name="use-synonyms"></a>Použití synonym
I když je k dispozici podpora synonym v anglickém jazyce, použijte změnu velikosti písmen bez rozlišení velkých a malých písmen prostřednictvím [rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) pro přidání synonym k klíčovým slovům, která přijímají odlišnou formu. Synonyma se přidávají na úrovni služby QnA Maker a sdílí se všemi bázemi znalostí ve službě.

|Původní aplikace word|Synonyma|
|--|--|
|Koupit|koupit<br>síť – bankovnictví<br>NET bankovnictví|

### <a name="use-distinct-words-to-differentiate-questions"></a>Používat různá slova k rozlišení dotazy
QnA Maker algoritmus hodnocení, který odpovídá dotazu uživatele s otázkou ve znalostní bázi Knowledge Base, funguje nejlépe, pokud každá otázka řeší jinou potřebu. Opakování téhož slova nastaven v rozmezí dotazy snižuje pravděpodobnost, že je vybrána správná odpověď pro daný uživatelský dotaz se tato slova. 

Například může mít maximálně dvě samostatné se na následující otázky:

|Maximálně|
|--|
|Pokud je parkovací *umístění*|
|kde je *umístění* ATM|

Protože se velmi podobá slov obsahuje jiné spojení těchto dvou maximálně, tento podobnosti by mohlo způsobit velmi podobné výsledky pro mnoho dotazy, které jsou obsahuje jiné spojení, jako je *"kde je `<x>` umístění"* . Místo toho se pokuste jasně odlišit pomocí dotazů, jako *je "kde je zaparkovaná dávka"* a *"kde je ATM"* , a to tak, že se vyhnete slovám, jako je "umístění", které by mohlo být ve vaší znalostní bázi hodně otázek. 

## <a name="collaborate"></a>Spolupráce
Nástroj QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) ve znalostní bázi. Uživatelé potřebovat přístup ke skupině prostředků Azure QnA Maker za účelem přístupu k znalostních bází. Některé organizace chtít externí pomocí úpravy znalostní báze knowledge base a údržba a stále mít možnost chránit přístup k jejich prostředky Azure. Tento model schvalovatele editoru se provádí nastavením dva identické [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a výběrem jedné pro cyklus úpravy testování. Po dokončení testování se přenáší obsah znalostní báze s [importu a exportu](../Tutorials/migrate-knowledge-base.md) zpracovat služba QnA Maker schvalovatele, který bude nakonec publikovat znalostní báze a aktualizujte koncový bod.



## <a name="active-learning"></a>Aktivní učení

[Aktivní učení](../How-to/improve-knowledge-base.md) nabízí nejlepší úlohu při navrhování alternativních otázek, když má široké spektrum kvality a množství dotazů založených na uživatelích. Je důležité, aby dotazy uživatelů klientské aplikace mohly se zúčastnit smyčky aktivního výukového zpětné vazby bez Censorship. Po navržení otázek na portálu QnA Maker můžete **[filtrovat podle návrhů](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** a tyto návrhy pak zobrazit a přijmout nebo odmítnout. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)
