---
title: Osvědčené postupy – QnA Maker
description: Použijte tyto osvědčené postupy pro zlepšení znalostní báze a application/chatovací robot koncovým uživatelům poskytovat lepší výsledky.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: fb935aeed7b492a3a0c213d6d7166bd5d80144c1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220773"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Doporučené postupy nástroje QnA Maker znalostní báze

[Životní cyklus vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás provede postupem správy KB od začátku do konce. Tyto osvědčené postupy použijte k vylepšení znalostní báze a poskytují lepší výsledky klientským aplikacím nebo koncovým uživatelům robotů pro konverzaci.

## <a name="extraction"></a>Extrakce

Služba QnA Maker se neustále se zlepšovat algoritmy, které extrahují maximálně z obsahu a rozbalení seznamu podporovaných souborů a formátů HTML. Postupujte podle [pokynů](../Concepts/content-types.md) pro extrakci dat na základě typu dokumentu.

Obecně platí nejčastější dotazy k stránky by měl být samostatný a ne kombinované spolu s dalšími informacemi. Produktových příruček by měl mít vymazat záhlaví a pokud možno indexovou stránku.

### <a name="configuring-multi-turn"></a>Konfigurace vícenásobného zapnutí

[Vytvořte znalostní bázi](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) s povoleným extrakcí pomocí vícenásobného vypínání. Pokud vaše znalostní báze dělá nebo by měla podporovat hierarchii otázek, můžete tuto hierarchii extrahovat z dokumentu nebo vytvořit po extrakci dokumentu.

## <a name="creating-good-questions-and-answers"></a>Vytváření dobrých otázek a odpovědí

### <a name="good-questions"></a>Dobré otázky

Nejlepší otázky jsou jednoduché. Zvažte klíčové slovo nebo frázi pro každou otázku a pak vytvořte jednoduchou otázku pro toto klíčové slovo nebo frázi.

Přidejte tolik alternativních otázek, kolik potřebujete, ale ponechte jednoduché změny. Přidání dalších slov nebo frází, které nejsou součástí hlavního cíle otázky, neumožňuje QnA Maker najít shodu.


### <a name="add-relevant-alternative-questions"></a>Přidat relevantní alternativní otázky

Uživatel může do otázek zadat typ konverzace text, `How do I add a toner cartridge to my printer?` nebo hledání klíčových slov, jako je například `toner cartridge`. Aby bylo možné správně vrátit nejlepší odpověď, musí mít znalostní báze obojí styly otázek. Pokud si nejste jistí, jaká klíčová slova zákazník zadá, použijte k analýze dotazů Application Insights dat.

### <a name="good-answers"></a>Dobré odpovědi

Nejlepší odpovědi jsou jednoduché odpovědi, ale nejsou příliš jednoduché. Nepoužívejte odpovědi, jako je `yes` a `no`. Pokud by vaše odpověď měla odkazovat na jiné zdroje nebo poskytovat bohatou zkušenost s médii a odkazy, použijte [označování metadat](../how-to/edit-knowledge-base.md#add-metadata) k rozlišení mezi odpověďmi a pak [odešlete dotaz](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) s značkami metadat ve vlastnosti `strictFilters`, abyste získali správnou verzi odpovědi.

|Odpověď|Follup výzvy|
|--|--|
|Vypněte Surface přenosného počítače pomocí tlačítka napájení na klávesnici.|* Kombinace kláves pro režim spánku, vypnutí a restartování.<br>* Jak pevně spustit Surface přenosného počítače<br>* Jak změnit systém BIOS pro Surface přenosného počítače<br>* Rozdíly mezi režimem spánku, vypnutí a restartování|
|Služba zákazníkům je dostupná přes telefon, Skype a textovou zprávu 24 hodin denně.|* Kontaktní informace pro prodej.<br> * Umístění a hodiny pro Office a úložiště pro navštívené osoby.<br> * Příslušenství pro Surface notebooků.|

## <a name="chit-chat"></a>Chit chatu
Přidat do svého robota, aby váš robot konverzační a zajímavější, chit chat s nízké úsilí. Můžete snadno přidat datové sady funkce CHITEST-chat z předem definovaných osobních údajů při vytváření a kdykoli je kdykoli změnit. Naučte se, jak [Přidat CHITEST – chat ke své znalostní bázi](../How-To/chit-chat-knowledge-base.md).

CHITEST – chat je podporován v [mnoha jazycích](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Výběr posouzení vašich osobnostních
CHITEST – chat se podporuje pro několik předdefinovaných osobních důvodů:

|Osobní |Soubor datové sady QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Vhodná |[qna_chitchat_friendly. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Dostáváme |[qna_chitchat_enthusiastic. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

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

Pokud nechcete hledat odpovědi, použijte [`RankerType=QuestionOnly`](#choosing-ranker-type) .

Příkladem je, že znalostní báze je katalog zkratek jako otázky s jejich úplnou formou jako odpověď. Hodnota odpovědi vám nepomůže vyhledat příslušnou odpověď.

## <a name="rankingscoring"></a>Pořadí a vyhodnocování
Ujistěte se, že provádíte co nejlíp využít hodnocení funkce, které podporuje QnA Maker. To zvýší pravděpodobnost, která daný uživatelský dotaz je zodpovězen odpovídající odpověď.

### <a name="choosing-a-threshold"></a>Výběr prahové hodnoty

Výchozí [hodnocení spolehlivosti](confidence-score.md) , které se používá jako prahová hodnota, je 0, ale v závislosti na vašich potřebách můžete [změnit prahovou hodnotu](confidence-score.md#set-threshold) pro vaši databázi. Protože každý KB se liší, by měl test a zvolte prahovou hodnotu, která je nejlepší vhodné pro vaše KB.

### <a name="choosing-ranker-type"></a>Volba typu klasifikace
Ve výchozím nastavení QnA Maker vyhledává dotazy a odpovědi. Chcete-li vyhledávat pouze otázky, vygenerujte odpověď, použijte `RankerType=QuestionOnly` v těle žádosti GenerateAnswer.

### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní otázky](../How-To/edit-knowledge-base.md) zlepšují pravděpodobnost shody s uživatelským dotazem. Alternativní dotazy jsou užitečné, pokud existuje více způsobů, ve kterém se dotaz na stejnou otázku. To může zahrnovat změny ve struktuře věty a stylu aplikace word.

|Původní dotaz|Alternativních dotazů|Změnit|
|--|--|--|
|Je parkovací k dispozici?|Máte car park?|Struktura větu|
 |Ahoj|Jo<br>Dobrý den existuje!|Word – vizuální styl nebo slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrování otázek a odpovědí pomocí značek metadat

Díky [metadatům](../How-To/edit-knowledge-base.md) může klientská aplikace zjistit, že by neměl přijímat všechny odpovědi, ale místo toho může zúžit výsledky dotazu uživatele na základě značek metadat. Odpověď znalostní báze se může lišit podle značky metadat i v případě, že dotaz je stejný. Například *"kde je zaparkovaná síť"* může mít jinou odpověď v případě, že se umístění větve restaurace liší – to znamená, že metadata jsou *umístění Praha* a *umístění: Redmond*.

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
|kde je *umístění* parkování|
|kde je *umístění* ATM|

Vzhledem k tomu, že tyto dvě QnAs jsou fráze s velmi podobnými slovy, může tato podobnost způsobit velmi podobné skóre pro mnoho uživatelských dotazů, které jsou fráze, jako *je například umístění `<x>`* . Místo toho se pokuste jasně odlišit pomocí dotazů, jako *je "kde je zaparkovaná dávka"* a *"kde je ATM"* , a to tak, že se vyhnete slovám, jako je "umístění", které by mohlo být ve vaší znalostní bázi hodně otázek.

## <a name="collaborate"></a>Spolupráce
QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) na znalostní bázi. Uživatelé potřebovat přístup ke skupině prostředků Azure QnA Maker za účelem přístupu k znalostních bází. Některé organizace chtít externí pomocí úpravy znalostní báze knowledge base a údržba a stále mít možnost chránit přístup k jejich prostředky Azure. Tento model schvalovatele editoru se provádí nastavením dvou stejných [služeb QnA maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a výběrem jednoho pro cyklus úprav a testování. Po dokončení testování se obsah znalostní báze přenese pomocí procesu importu a [exportu](../Tutorials/migrate-knowledge-base.md) do QnA maker služby schvalovatele, která nakonec publikuje znalostní bázi a aktualizuje koncový bod.



## <a name="active-learning"></a>Aktivní učení

[Aktivní učení](../How-to/improve-knowledge-base.md) nabízí nejlepší úlohu při navrhování alternativních otázek, když má široké spektrum kvality a množství dotazů založených na uživatelích. Je důležité, aby dotazy uživatelů klientské aplikace mohly se zúčastnit smyčky aktivního výukového zpětné vazby bez Censorship. Po navržení otázek na portálu QnA Maker můžete **[filtrovat podle návrhů](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** a tyto návrhy pak zobrazit a přijmout nebo odmítnout.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)
