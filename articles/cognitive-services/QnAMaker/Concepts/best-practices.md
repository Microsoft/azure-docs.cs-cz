---
title: Osvědčené postupy – QnA Maker
description: Využijte tyto osvědčené postupy k vylepšení znalostní báze a poskytněte uživatelům lepší výsledky pro koncové uživatele robota aplikace/konverzace.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 2f87f5c7e43757db476153db93d6ecc5082dde89
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376753"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Osvědčené postupy QnA Maker znalostní bázi Knowledge Base

[Životní cyklus vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás provede postupem správy KB od začátku do konce. Tyto osvědčené postupy použijte k vylepšení znalostní báze a poskytují lepší výsledky klientským aplikacím nebo koncovým uživatelům robotů pro konverzaci.

## <a name="extraction"></a>Extrakce

Služba QnA Maker neustále vylepšuje algoritmy, které extrahují QnAs z obsahu a rozbalí seznam podporovaných formátů souborů a HTML. Postupujte podle [pokynů](../Concepts/content-types.md) pro extrakci dat na základě typu dokumentu.

Obecně platí, že stránky nejčastějších dotazů by měly být samostatné a nekombinované s dalšími informacemi. Příručky k produktu by měly mít prázdná záhlaví a možnou stránku indexu.

### <a name="configuring-multi-turn"></a>Konfigurace vícenásobného zapnutí

[Vytvořte znalostní bázi](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) s povoleným extrakcí pomocí vícenásobného vypínání. Pokud vaše znalostní báze dělá nebo by měla podporovat hierarchii otázek, můžete tuto hierarchii extrahovat z dokumentu nebo vytvořit po extrakci dokumentu.

## <a name="creating-good-questions-and-answers"></a>Vytváření dobrých otázek a odpovědí

### <a name="good-questions"></a>Dobré otázky

Nejlepší otázky jsou jednoduché. Zvažte klíčové slovo nebo frázi pro každou otázku a pak vytvořte jednoduchou otázku pro toto klíčové slovo nebo frázi.

Přidejte tolik alternativních otázek, kolik potřebujete, ale ponechte jednoduché změny. Přidání dalších slov nebo frází, které nejsou součástí hlavního cíle otázky, neumožňuje QnA Maker najít shodu.


### <a name="add-relevant-alternative-questions"></a>Přidat relevantní alternativní otázky

Uživatel může zadávat dotazy buď pomocí konverzačního stylu textu, `How do I add a toner cartridge to my printer?` nebo pomocí klíčového slova, jako je například `toner cartridge` . Aby bylo možné správně vrátit nejlepší odpověď, musí mít znalostní báze obojí styly otázek. Pokud si nejste jistí, jaká klíčová slova zákazník zadá, použijte k analýze dotazů Application Insights dat.

### <a name="good-answers"></a>Dobré odpovědi

Nejlepší odpovědi jsou jednoduché odpovědi, ale nejsou příliš jednoduché. Nepoužívejte odpovědi, jako je `yes` a `no` . Pokud by vaše odpověď měla odkazovat na jiné zdroje nebo poskytovat bohatou zkušenost s médii a odkazy, použijte [označování metadat](../how-to/edit-knowledge-base.md#add-metadata) k rozlišení mezi odpověďmi a pak [odešlete dotaz](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) s značkami metadat ve `strictFilters` vlastnosti, abyste získali správnou verzi odpovědi.

|Odpověď|Follup výzvy|
|--|--|
|Vypněte Surface přenosného počítače pomocí tlačítka napájení na klávesnici.|* Kombinace kláves pro režim spánku, vypnutí a restartování.<br>* Jak pevně spustit Surface přenosného počítače<br>* Jak změnit systém BIOS pro Surface přenosného počítače<br>* Rozdíly mezi režimem spánku, vypnutí a restartování|
|Služba zákazníkům je dostupná přes telefon, Skype a textovou zprávu 24 hodin denně.|* Kontaktní informace pro prodej.<br> * Umístění a hodiny pro Office a úložiště pro navštívené osoby.<br> * Příslušenství pro Surface notebooků.|

## <a name="chit-chat"></a>Chit-Chat
Přidejte práci CHITEST – chat k robotovi, abyste měli větší konverzaci a poutavější, s nízkou intenzitou. Můžete snadno přidat datové sady funkce CHITEST-chat z předem definovaných osobních údajů při vytváření a kdykoli je kdykoli změnit. Naučte se, jak [Přidat CHITEST – chat ke své znalostní bázi](../How-To/chit-chat-knowledge-base.md).

CHITEST – chat je podporován v [mnoha jazycích](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Volba osobnosti
CHITEST – chat se podporuje pro několik předdefinovaných osobních důvodů:

|Osobnost |Soubor datové sady QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Vhodná |[qna_chitchat_friendly. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Dostáváme |[qna_chitchat_enthusiastic. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Reakce v rozsahu od formálního po neformální a irreverent. Měli byste vybrat osobnost, která je co nejblíže k požadovanému tónu pro robot. Můžete zobrazit [datové sady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)a zvolit jednu, která slouží jako základ pro robota, a pak přizpůsobit odpovědi.

### <a name="edit-bot-specific-questions"></a>Upravit otázky specifické pro roboty
K dispozici jsou některé otázky specifické pro roboty, které jsou součástí datové sady CHITEST-chat a byly vyplněny obecnými odpověďmi. Změňte tyto odpovědi tak, aby co nejlépe odpovídaly vašim bot detailům.

Doporučujeme, aby následující funkce CHITEST-chat QnAs konkrétnější:

* Kdo jsi?
* Co můžete udělat?
* Kolik ti je?
* Kdo vám to vytvořil?
* Dobrý den

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Přidání vlastního funkce CHITEST – chat se značkou metadat

Pokud přidáte vlastní páry funkce CHITEST-chat QnA, nezapomeňte přidat metadata, aby se tyto odpovědi vracely. Dvojice název/hodnota metadat je `editorial:chitchat` .

## <a name="searching-for-answers"></a>Hledání odpovědí

Rozhraní GenerateAnswer API používá otázky a odpověď k vyhledání nejlepších odpovědí na dotaz uživatele.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Hledání otázek pouze v případě, že odpověď není relevantní

Použijte v [`RankerType=QuestionOnly`](#choosing-ranker-type) případě, že nechcete hledat odpovědi.

Příkladem je, že znalostní báze je katalog zkratek jako otázky s jejich úplnou formou jako odpověď. Hodnota odpovědi vám nepomůže vyhledat příslušnou odpověď.

## <a name="rankingscoring"></a>Hodnocení a bodování
Ujistěte se, že využijete nejlepšího použití funkcí hodnocení QnA Maker podporuje. Tím dojde ke zvýšení pravděpodobnosti, že daný dotaz na uživatele bude zodpovězen příslušnou odpovědí.

### <a name="choosing-a-threshold"></a>Výběr prahové hodnoty

Výchozí [hodnocení spolehlivosti](confidence-score.md) , které se používá jako prahová hodnota, je 0, ale v závislosti na vašich potřebách můžete [změnit prahovou hodnotu](confidence-score.md#set-threshold) pro vaši databázi. Vzhledem k tomu, že se každá KB liší, měli byste otestovat a vybrat prahovou hodnotu, která je nejvhodnější pro vaši databázi.

### <a name="choosing-ranker-type"></a>Volba typu klasifikace
Ve výchozím nastavení QnA Maker vyhledává dotazy a odpovědi. Pokud chcete vygenerovat odpověď pouze v rámci otázek, použijte `RankerType=QuestionOnly` v těle žádosti GenerateAnswer v části post.

### <a name="add-alternate-questions"></a>Přidat alternativní otázky
[Alternativní otázky](../How-To/edit-knowledge-base.md) zlepšují pravděpodobnost shody s uživatelským dotazem. Alternativní otázky jsou užitečné, když existuje několik způsobů, jak se může zobrazit stejná otázka. To může zahrnovat změny ve struktuře vět a ve stylu Wordu.

|Původní dotaz|Alternativní dotazy|Změnit|
|--|--|--|
|Je parkoviště k dispozici?|Máte automobilový Park?|struktura věty|
 |Ahoj|Jo<br>Ahoj!|styl Wordu nebo slangem|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrování otázek a odpovědí pomocí značek metadat

Díky [metadatům](../How-To/edit-knowledge-base.md) může klientská aplikace zjistit, že by neměl přijímat všechny odpovědi, ale místo toho může zúžit výsledky dotazu uživatele na základě značek metadat. Odpověď znalostní báze se může lišit v závislosti na značce metadat, a to i v případě, že je dotaz stejný. Například *"kde je zaparkovaná síť"* může mít jinou odpověď v případě, že se umístění větve restaurace liší – to znamená, že metadata jsou *umístění Praha* a *umístění: Redmond*.

### <a name="use-synonyms"></a>Použít synonyma
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)
I když je k dispozici podpora synonym v anglickém jazyce, použijte změnu velikosti písmen bez rozlišení velkých a malých písmen prostřednictvím [rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) pro přidání synonym k klíčovým slovům, která přijímají různé formuláře. Synonyma se přidávají na úrovni služby QnA Maker a **sdílí se všemi bázemi znalostí ve službě**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)
I když je k dispozici podpora synonym v anglickém jazyce, použijte změnu velikosti písmen bez rozlišení velkých a malých písmen prostřednictvím [rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) pro přidání synonym k klíčovým slovům, která přijímají různé formuláře. **Pro každou znalostní bázi se přidala** synonyma ve spravovaném QnA maker (Preview).

|Původní slovo|Synonyma|
|--|--|
|zakoupit|nákup<br>síť – bankovnictví<br>čistý bankovní|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Odlišení otázek pomocí různých slov
QnA Maker algoritmus hodnocení, který odpovídá dotazu uživatele s otázkou ve znalostní bázi Knowledge Base, funguje nejlépe, pokud každá otázka řeší jinou potřebu. Opakování stejné sady wordové sady mezi otázkami snižuje pravděpodobnost, že je zvolena správná odpověď pro daný dotaz uživatele s těmito slovy.

Například můžete mít dvě samostatné QnAs s následujícími otázkami:

|QnAs|
|--|
|kde je *umístění* parkování|
|kde je *umístění* ATM|

Vzhledem k tomu, že tyto dvě QnAs jsou fráze s velmi podobnými slovy, může tato podobnost způsobit velmi podobné skóre pro mnoho uživatelských dotazů, které jsou fráze, jako  *je například `<x>` umístění*. Místo toho se pokuste jasně odlišit pomocí dotazů, jako  *je "kde je zaparkovaná dávka"* a *"kde je ATM"* , a to tak, že se vyhnete slovám, jako je "umístění", které by mohlo být ve vaší znalostní bázi hodně otázek.

## <a name="collaborate"></a>Spolupráce
QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) na znalostní bázi. Uživatelé potřebují přístup ke skupině prostředků Azure QnA Maker, aby mohli získat přístup ke znalostní bázi. Některé organizace můžou chtít využívat úpravy a údržbu znalostní báze a pořád budou moct chránit přístup ke svým prostředkům Azure. Tento model schvalovatele editoru se provádí nastavením dvou stejných [služeb QnA maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a výběrem jednoho pro cyklus úprav a testování. Po dokončení testování se obsah znalostní báze přenese pomocí procesu importu a [exportu](../Tutorials/migrate-knowledge-base.md) do QnA maker služby schvalovatele, která nakonec publikuje znalostní bázi a aktualizuje koncový bod.



## <a name="active-learning"></a>Aktivní učení

[Aktivní učení](../How-to/use-active-learning.md) nabízí nejlepší úlohu při navrhování alternativních otázek, když má široké spektrum kvality a množství dotazů založených na uživatelích. Je důležité, aby dotazy uživatelů klientské aplikace mohly se zúčastnit smyčky aktivního výukového zpětné vazby bez Censorship. Po navržení otázek na portálu QnA Maker můžete **[filtrovat podle návrhů](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** a tyto návrhy pak zobrazit a přijmout nebo odmítnout.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)