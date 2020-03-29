---
title: Doporučené postupy – QnA Maker
description: Pomocí těchto doporučených postupů můžete zlepšit znalostní bázi a poskytnout lepší výsledky koncovým uživatelům vašeho robota aplikace/chatu.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053137"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Osvědčené postupy znalostní báze QnA Maker

[Životní cyklus vývoje znalostní báze](../Concepts/development-lifecycle-knowledge-base.md) vás provede tím, jak spravovat kb od začátku do konce. Pomocí těchto doporučených postupů můžete zlepšit znalostní bázi a zajistit lepší výsledky koncovým uživatelům klientské aplikace nebo chatovacího robota.

## <a name="extraction"></a>Extrakce

Služba QnA Maker neustále vylepšuje algoritmy, které extrahují qnA z obsahu, a rozšiřuje seznam podporovaných formátů souborů a HTML. Postupujte podle [pokynů](../Concepts/content-types.md) pro extrakci dat na základě typu dokumentu.

Stránky s nejčastějšími dotazy by obecně měly být samostatné a neměly by být kombinovány s dalšími informacemi. Příručky k produktům by měly mít jasné nadpisy a pokud možno stránku indexu.

### <a name="configuring-multi-turn"></a>Konfigurace víceotočení

[Vytvořte si znalostní bázi](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) s povolenou extrakcí s více otočeními. Pokud znalostní báze podporuje nebo by měla podporovat hierarchii otázek, lze tuto hierarchii extrahovat z dokumentu nebo vytvořit po extrahování dokumentu.

## <a name="creating-good-questions-and-answers"></a>Vytváření dobrých otázek a odpovědí

### <a name="good-questions"></a>Dobré otázky

Nejlepší otázky jsou jednoduché. Zvažte klíčové slovo nebo frázi pro každou otázku a vytvořte jednoduchou otázku pro toto klíčové slovo nebo frázi.

Přidejte tolik alternativních otázek, kolik potřebujete, ale udržujte změny jednoduché. Přidání dalších slov nebo frází, které nejsou součástí hlavního cíle otázky, nepomůže QnA Makernajít shodu.


### <a name="add-relevant-alternative-questions"></a>Přidání relevantních alternativních otázek

Uživatel může zadávat otázky buď konverzačním `How do I add a toner cartridge to my printer?` stylem textu, `toner cartridge`nebo vyhledáváním klíčových slov, například . Znalostní báze by měl y mít oba styly otázek, aby bylo možné správně vrátit nejlepší odpověď. Pokud si nejste jisti, jaká klíčová slova zákazník zadává, použijte data Application Insights k analýze dotazů.

### <a name="good-answers"></a>Dobré odpovědi

Nejlepší odpovědi jsou jednoduché odpovědi, ale ne příliš jednoduché. Nepoužívejte odpovědi, `yes` jako `no`jsou a . Pokud by vaše odpověď měla odkazovat na jiné zdroje nebo poskytovat bohaté možnosti s médii a odkazy, použijte [označování metadat](../how-to/edit-knowledge-base.md#add-metadata) k rozlišení odpovědí a [odešlete dotaz](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se značkami metadat ve `strictFilters` vlastnosti, abyste získali správnou verzi odpovědi.

|Odpověď|Výzvy follup-up|
|--|--|
|Zapněte notebook Surface tlačítkem napájení na klávesnici.|* Kombinace klíčů do režimu spánku, vypnutí a restartování.<br>* Jak hard-boot notebook surface<br>* Jak změnit BIOS pro notebook Surface<br>* Rozdíly mezi spánku, vypnutí a restart|
|Zákaznický servis je k dispozici prostřednictvím telefonu, Skype a textové zprávy 24 hodin denně.|* Kontaktní informace pro prodej.<br> * Umístění kanceláří a obchodů a hodiny pro osobní návštěvu.<br> * Příslušenství pro notebook Surface.|

## <a name="chit-chat"></a>Chit-Chat
Přidejte chit-chat do svého robota, aby se váš bot více konverzační a poutavé, s nízkým úsilím. Při vytváření kb můžete snadno přidávat datové sady chit-chat od předdefinovaných osobností a kdykoli je změnit. Přečtěte si, jak [do kb přidat chit-chat](../How-To/chit-chat-knowledge-base.md).

Chit-chat je podporován v [mnoha jazycích](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Výběr osobnosti
Chit-chat je podporován pro několik předdefinovaných osobností:

|Osobnosti |Soubor datové sady QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Přátelské |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Vtipný |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Péče |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Nadšený |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Odpovědi sahají od formálních až po neformální a neuctivé. Měli byste vybrat osobnost, která je nejblíže v souladu s tónem, který chcete pro svého robota. Můžete zobrazit [datové sady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)a vybrat ten, který slouží jako základ pro vašeho robota, a pak přizpůsobit odpovědi.

### <a name="edit-bot-specific-questions"></a>Úprava otázek specifických pro roboty
Existují některé otázky specifické pro roboty, které jsou součástí datové sady chit-chat a byly vyplněny obecnými odpověďmi. Změňte tyto odpovědi tak, aby co nejlépe odrážely podrobnosti o botech.

Doporučujeme, aby následující chit-chat QnAs konkrétnější:

* Kdo jsi?
* Co můžete dělat?
* Kolik ti je?
* Kdo tě stvořil?
* Dobrý den

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Přidání vlastního chatu pomocí značky metadat

Pokud přidáte vlastní chit-chat QnA páry, ujistěte se, že přidat metadata, takže tyto odpovědi jsou vráceny. Dvojice název/hodnota metadat `editorial:chitchat`je .

## <a name="searching-for-answers"></a>Hledání odpovědí

GenerateAnswer API používá otázky i odpověď k hledání nejlepší odpovědi na dotaz uživatele.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Vyhledávání otázek pouze v případě, že odpověď není relevantní

Použijte, [`RankerType=QuestionOnly`](#choosing-ranker-type) pokud nechcete hledat odpovědi.

Příkladem je, když znalostní báze je katalog zkratky jako otázky s jejich plnou formou jako odpověď. Hodnota odpovědi nepomůže hledat vhodnou odpověď.

## <a name="rankingscoring"></a>Pořadí/bodování
Ujistěte se, že nejlépe využíváte funkce hodnocení, které QnA Maker podporuje. Tím se zlepší pravděpodobnost, že daný uživatelský dotaz je zodpovězena s příslušnou odpovědí.

### <a name="choosing-a-threshold"></a>Výběr prahové hodnoty

Výchozí [skóre spolehlivosti,](confidence-score.md) které se používá jako prahová hodnota, je 0, ale můžete [změnit prahovou hodnotu](confidence-score.md#set-threshold) pro kb na základě vašich potřeb. Vzhledem k tomu, že každá kb je jiná, měli byste otestovat a zvolit prahovou hodnotu, která je nejvhodnější pro vaši KB.

### <a name="choosing-ranker-type"></a>Volba typu Ranker
Ve výchozím nastavení služba QnA Maker prohledává otázky a odpovědi. Pokud chcete prohledávat pouze otázky, chcete-li `RankerType=QuestionOnly` vygenerovat odpověď, použijte v textu POST požadavku GenerateAnswer.

### <a name="add-alternate-questions"></a>Přidání alternativních otázek
[Alternativní otázky](../How-To/edit-knowledge-base.md) zvyšují pravděpodobnost shody s uživatelským dotazem. Alternativní otázky jsou užitečné, pokud existuje více způsobů, jak může být položena stejná otázka. To může zahrnovat změny ve struktuře věty a stylu slova.

|Původní dotaz|Alternativní dotazy|Změnit|
|--|--|--|
|Je k dispozici parkování?|Máte parkoviště?|větná struktura|
 |Ahoj|Yo<br>Ahoj!|slovní styl nebo slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrování otázek a odpovědí pomocí značek metadat

[Metadata](../How-To/edit-knowledge-base.md) přidá možnost pro klientské aplikace vědět, že by neměla trvat všechny odpovědi, ale místo toho zúžit výsledky uživatelského dotazu na základě značek metadat. Odpověď znalostní báze se může lišit v závislosti na značce metadat, i když je dotaz stejný. Například *"kde se nachází parkoviště"* může mít jinou odpověď, pokud umístění pobočky restaurace se liší - to znamená, že metadata je *Umístění: Seattle* versus *Umístění: Redmond*.

### <a name="use-synonyms"></a>Použití synonym
Zatímco existuje určitá podpora synonym v anglickém jazyce, použijte změny slov bez rozlišování velkých a malých písmen prostřednictvím [rozhraní API změny](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) přidat synonyma klíčová slova, která mají jinou podobu. Synonyma jsou přidávána na úrovni služeb QnA Maker a sdílena všemi znalostními bázemi ve službě.

|Původní slovo|Synonyma|
|--|--|
|Koupit|nákup<br>net-bankovnictví<br>čisté bankovnictví|

### <a name="use-distinct-words-to-differentiate-questions"></a>Použití odlišných slov k rozlišení otázek
Algoritmus hodnocení QnA Maker, který odpovídá uživatelskému dotazu s otázkou ve znalostní bázi, funguje nejlépe, pokud každá otázka řeší jinou potřebu. Opakování stejné sady slov mezi otázkami snižuje pravděpodobnost, že je pro daný uživatelský dotaz těmito slovy vybrána správná odpověď.

Můžete mít například dvě samostatné qnas s následujícími otázkami:

|QnAs|
|--|
|kde je *parkovací místo*|
|kde je *umístění* atm|

Vzhledem k tomu, že tyto dvě QnAs jsou formulovány s velmi podobnými slovy, tato podobnost může způsobit velmi podobné skóre pro mnoho uživatelských dotazů, které jsou formulovány jako *"kde je `<x>` umístění"*. Místo toho se snažte jasně rozlišovat s dotazy jako *"kde je parkoviště"* a *"kde je bankomat"*, tím, že se vyhnete slovům jako "umístění", které by mohly být v mnoha otázkách ve vaší KB.

## <a name="collaborate"></a>Spolupráce
QnA Maker umožňuje uživatelům [spolupracovat](../How-to/collaborate-knowledge-base.md) na znalostní bázi. Uživatelé potřebují přístup ke skupině prostředků Azure QnA Maker, aby měli přístup ke znalostním základům. Některé organizace mohou chtít zadávat úpravy a údržbu znalostní báze a stále mohou chránit přístup ke svým prostředkům Azure. Tento model schvalovatele editoru se provádí nastavením dvou identických [služeb QnA Maker](../How-to/set-up-qnamaker-service-azure.md) v různých předplatných a výběrem jedné pro cyklus testování úprav. Po dokončení testování se obsah znalostní báze přenese s procesem [importu a exportu](../Tutorials/migrate-knowledge-base.md) do služby QnA Maker schvalovatele, která nakonec publikuje znalostní bázi a aktualizuje koncový bod.



## <a name="active-learning"></a>Aktivní učení

[Aktivní učení](../How-to/use-active-learning.md) dělá nejlepší práci při navrhování alternativních otázek, když má širokou škálu kvality a množství uživatelských dotazů. Je důležité povolit dotazy uživatelů klientských aplikací k účasti v aktivní zpětné vazbě učení bez cenzury. Jakmile jsou na portálu QnA Maker například naportálu QnA Maker, můžete **[filtrovat podle návrhů](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** a pak tyto návrhy zkontrolovat a přijmout nebo odmítnout.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-to/edit-knowledge-base.md)
