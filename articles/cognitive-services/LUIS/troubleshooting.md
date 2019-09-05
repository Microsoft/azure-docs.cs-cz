---
title: Nejčastější dotazy (FAQ) – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje odpovědi na nejčastější dotazy o Language Understanding (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 3907a244bc2d85e7225f94b15150298fd80a032f
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382344"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Nejčastější dotazy Language Understanding

Tento článek obsahuje odpovědi na nejčastější dotazy o Language Understanding (LUIS).

## <a name="whats-new"></a>Co je nového

[Přečtěte si další](whats-new.md) informace o tom, co je nového v Language Understanding.

<a name="luis-authoring"></a>

## <a name="authoring"></a>Vytváření obsahu

### <a name="what-are-the-luis-best-practices"></a>Co jsou doporučené postupy LUIS?
Začněte [vytváření cyklu](luis-concept-app-iteration.md), pak si můžete přečíst [osvědčené postupy](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Co je nejlepší způsob, jak můžete začít sestavovat aplikaci v LUIS?

Nejlepší způsob, jak sestavit aplikaci je prostřednictvím [přírůstkové procesu](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co je dobrým zvykem model záměry Moje aplikace? Je vhodné vytvořit konkrétnější nebo obecnějším záměry?

Zvolte záměrů, které nejsou tak obecné by se překrývají, ale nejsou tak specifické, že to ztěžuje LUIS k rozlišení mezi podobné záměry. Vytváření discriminative konkrétní záměry je jedním z osvědčených postupů pro modelování LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>Je důležité pro trénování záměru žádný?

Ano, je vhodné pro trénování vaše **žádný** záměru s více projevy při přidávání více popisků do jiných záměry. Je dobrý poměr 1 nebo 2 popisky přidané k **žádný** pro každých 10 popisky přidané k záměru. Tento poměr zvyšuje discriminative sílu LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak může oprava pravopisných chyb ve projevy?

Zobrazit [API V7 kontrola pravopisu Bingu](luis-tutorial-bing-spellcheck.md) kurzu. Služba LUIS vynucuje omezení, která API V7 kontrola pravopisu Bingu.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak mohu programově upravit aplikaci LUIS?
Chcete-li upravit aplikace LUIS prostřednictvím kódu programu, použijte [rozhraní API pro vytváření](https://go.microsoft.com/fwlink/?linkid=2092087). Zobrazit [LUIS volání rozhraní API pro vytváření](./luis-quickstart-node-add-utterance.md) a [sestavení aplikace LUIS programově pomocí Node.js](./luis-tutorial-node-import-utterances-csv.md) příklady toho, jak volat rozhraní API pro vytváření. Rozhraní API pro vytváření vyžaduje použití [vytváření klíč](luis-concept-keys.md#authoring-key) místo klíče rozhraní koncového bodu. Programové vytváření umožňuje až 1 000 000 volání za měsíc a pět transakcí za sekundu. Další informace o klíče pomocí služby LUIS, naleznete v tématu [spravovat klíče](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Pokud je odpovídající vzor funkci, která poskytuje regulární výraz?
Předchozí **funkce** je momentálně zastaralý, nahrazuje  **[vzory](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak používat entity a vytáhnout správná data?
Zobrazit [entity](luis-concept-entity-types.md) a [extrakce dat](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variace utterance příkladu by měl obsahovat interpunkce?
Přidejte různými variantami jako příklad projevy k příslušnému záměru nebo vzorec, podle kterého utterance příklad s [syntaxe Ignorovat](luis-concept-patterns.md#pattern-syntax) znaky interpunkce.

### <a name="does-luis-currently-support-cortana"></a>Služba LUIS aktuálně podporuje Cortany?

Cortana, které předem sestavených aplikací byla vyřazena jako zastaralá v 2017. Již nejsou podporovány.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak se převést vlastnictví aplikace LUIS?
Aplikace LUIS přenést do jiného předplatného Azure, aplikace LUIS exportujte a importujte ho pomocí nového účtu. Aktualizujte ID aplikace LUIS v klientské aplikaci, která je volá. Nová aplikace může vrátit mírně odlišné LUIS skóre z původní aplikace.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Předem vytvořená entita je označena jako utterance jako místo vlastní entity. Návody opravit? 

Viz [Poradce při potížích s předem vytvořenými entitami](luis-concept-entity-types.md#troubleshooting-prebuilt-entities).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Pokusili jste se importovat soubor aplikace nebo verze, ale zobrazila se chyba, co se stalo? 

Přečtěte si další informace o [chybách importu verzí](luis-how-to-manage-versions.md#import-errors) a [chybách importu aplikací](luis-how-to-start-new-app.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Spolupráce a přispívání

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Návody dát spolupracovníkům přístup k LUIS pomocí Azure Active Directory (Azure AD) nebo řízení přístupu na základě role (RBAC)?

Informace o tom, jak dát spolupracovníkům přístup, najdete v tématu [Azure Active Directory prostředky](luis-how-to-collaborate.md#azure-active-directory-resources) a [Azure Active Directory uživatele klienta](luis-how-to-collaborate.md#azure-active-directory-tenant-user) . 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Koncový bod

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Obdržel (a) jsem stavový kód chyby HTTP 403. Jak ho mám opravit?

Stavové kódy chyb 403 a 429 získáte, když překročíte transakce za sekundu nebo transakce za měsíc pro vaši cenovou úroveň. Zvyšte svou cenovou úroveň nebo použijte Language Understanding [kontejnery](luis-container-howto.md).

Když použijete všechny tyto bezplatné dotazy na koncový bod 1000 nebo překročíte kvótu měsíčních transakcí vaší cenové úrovně, obdržíte kód stavu chyby HTTP 403. 

Pokud chcete tuto chybu opravit, musíte [změnit svou cenovou úroveň](luis-how-to-azure-subscription.md#change-pricing-tier) na vyšší úroveň nebo [vytvořit nový prostředek](get-started-portal-deploy-app.md#create-the-endpoint-resource) a [přiřadit ho k aplikaci](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Mezi řešení této chyby patří:

* V [Azure Portal](https://portal.azure.com)v prostředku Language Understanding na **cenové úrovni správa prostředků – >** změňte cenovou úroveň na vyšší úroveň TPS. Pokud je váš prostředek už přiřazený k aplikaci Language Understanding, nemusíte dělat nic na Language Understandingovém portálu.
*  Pokud vaše využití přesáhne nejvyšší cenovou úroveň, přidejte další Language Understanding prostředky s nástrojem pro vyrovnávání zatížení před nimi. K tomu může pomáhat [kontejner Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Obdržel (a) jsem stavový kód chyby HTTP 429. Jak ho mám opravit?

Stavové kódy chyb 403 a 429 získáte, když překročíte transakce za sekundu nebo transakce za měsíc pro vaši cenovou úroveň. Zvyšte svou cenovou úroveň nebo použijte Language Understanding [kontejnery](luis-container-howto.md).

Tento stavový kód se vrátí, když vaše transakce za sekundu překročí vaši cenovou úroveň.  

Mezi řešení patří:

* Pokud nejste na nejvyšší úrovni, můžete [zvýšit svou cenovou úroveň](luis-how-to-azure-subscription.md#change-pricing-tier).
* Pokud vaše využití přesáhne nejvyšší cenovou úroveň, přidejte další Language Understanding prostředky s nástrojem pro vyrovnávání zatížení před nimi. K tomu může pomáhat [kontejner Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose.
* Pomocí [zásady opakování](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) , kterou sami implementujete, můžete své požadavky na klientskou aplikaci vymezit tak, že se zobrazí tento stavový kód. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Můj dotaz koncový bod vrátil neočekávané výsledky. Co bych měl/a dělat?

Neočekávaný dotaz predikované výsledky jsou založeny na stav publikovaného modelu. Pokud chcete model opravit, možná budete muset model, vlak a publikování znovu změnit. 

Oprava modelu začíná [aktivně učit](luis-how-to-review-endpoint-utterances.md).

Můžete odebrat Nedeterministický školení aktualizací [aplikace verze nastavení rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aby bylo možné používat všechny trénovací data.

Zkontrolujte [osvědčené postupy](luis-concept-best-practices.md) další tipy. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč služba LUIS přidá mezery v dotazu kolem nebo uprostřed slova?
Služba LUIS [tokenizes](luis-glossary.md#token) na základě utterance [jazykovou verzi](luis-language-support.md#tokenization). Jsou k dispozici pro původní hodnotu a hodnotu tokenizovaná [extrakce dat](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak vytvořit a přiřadit klíče koncového bodu LUIS?
[Vytvoření klíče koncového bodu](luis-how-to-azure-subscription.md) v Azure pro vaše [služby](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) úroveň. [Přiřaďte klíč](luis-how-to-azure-subscription.md) na stránce **[prostředky Azure](luis-how-to-azure-subscription.md)** . Neexistuje žádná odpovídající rozhraní API pro tuto akci. Pak musíte změnit požadavek HTTP na koncový bod, který [používání nového klíče koncového bodu](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Jak se interpretují skóre, které se služba LUIS?
Systém by měl používat nejvyšší hodnocení záměr bez ohledu na jeho hodnotu. Například skóre pod 0,5 (méně než 50 %) nemusí nutně znamenat, že má služba LUIS s nízkou spolehlivostí. Poskytuje další trénovací data můžou pomoct zvýšit [skóre](luis-concept-prediction-score.md) nejpravděpodobnější záměru.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Proč nevidím moje přístupů koncový bod v řídicí panel Moje aplikace?
Celkový počet koncových bodů přístupů na řídicím panelu vaší aplikace jsou pravidelně aktualizovány, ale metriky související s vaší klíče koncového bodu služby LUIS na webu Azure Portal se aktualizuje častěji.

Pokud na řídicím panelu nevidíte aktualizované přístupy ke koncovému bodu, přihlaste se k Azure Portal a vyhledejte prostředek přidružený ke klíči koncového bodu LUIS a otevřete **metriky** pro výběr metriky **Celkový počet volání** . Pokud klíč koncového bodu se používá pro více než jednu aplikaci LUIS, metriky na webu Azure Portal zobrazí souhrnný počet volání ze všech aplikací LUIS, které ji používají.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Je k dispozici příkaz prostředí PowerShell k dosažení kvóty koncového bodu?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

K zobrazení kvóty koncového bodu můžete použít příkaz prostředí PowerShell:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moje aplikace LUIS pracoval včerejšího dne, ale ještě dnes se zobrazují chyby 403. Můžu aplikaci nezměnila. Jak ho mám opravit?
Podle těchto [pokynů](#how-do-i-create-and-assign-a-luis-endpoint-key) vytvořte klíč koncového bodu Luis a přiřaďte ho k aplikaci. Pak je nutné změnit požadavek HTTP klientské aplikace na koncový bod tak, aby [používal nový klíč koncového bodu](luis-concept-keys.md). Pokud jste vytvořili nový prostředek v jiné oblasti, změňte také oblast žádosti klienta HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpečit tento koncový bod služby LUIS?
Zobrazit [zabezpečení koncového bodu](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Práce v mezích limitů služby LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaký je maximální počet záměry a entity, které může podporovat aplikace LUIS?
Zobrazit [hranice](luis-boundaries.md) odkaz.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chci, aby k sestavení aplikace LUIS s více než maximální počet záměry. Co bych měl/a dělat?

Zobrazit [osvědčené postupy pro záměry](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chci vytvořit aplikaci v LUIS s více než maximální počet entit. Co bych měl/a dělat?

Zobrazit [osvědčené postupy pro entity](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jaká jsou omezení na počtu a velikosti frázi uvádí?
Pro maximální délku [seznam frází](./luis-concept-feature.md), najdete v článku [hranice](luis-boundaries.md) odkaz.

### <a name="what-are-the-limits-on-example-utterances"></a>Jaká jsou omezení na příkladu projevy?
Zobrazit [hranice](luis-boundaries.md) odkaz.

## <a name="testing-and-training"></a>Testování a školení

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Zobrazila se některé chyby ve službě batch testování podokně u některých modelů v aplikaci. Jak vyřešit tento problém?

Chyby označují, že je některá nesoulad mezi popisky a předpovědi z vašich modelů. K vyřešení problému, proveďte jednu z následujících úloh:
* Chcete-li LUIS zlepšit diskriminaci záměrů, přidejte další popisky.
* Chcete-li LUIS při učení, Přidat frázi seznam funkcí, které představují slovník jazyka specifického pro doménu.

Zobrazit [Batch testování](luis-tutorial-batch-testing.md) kurzu.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Pokud aplikace je exportovat a importovat do nové aplikace (s novým ID aplikace), skóre predikcí služby LUIS se liší. Proč k tomu dochází?

Zobrazit [předpovědi rozdíly mezi kopie stejnou aplikaci](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Některé projevy přejděte do nesprávného záměr po provedené změny aplikace. Zdá se, že problém náhodně zmizí. Jak ho mám opravit? 

Zobrazit [trénování se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikování aplikací

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co je ID tenanta v okně "Přidání klíče do vaší aplikace"?
V Azure představuje tenanta klient nebo organizace, která souvisí se službou. Svoje ID tenanta zjistíte na webu Azure Portal v **ID adresáře** pole tak, že vyberete **Azure Active Directory** > **spravovat**  >  **Vlastnosti**.

![ID tenanta na portálu Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Proč existují další klíče koncového bodu přiřadit do mojí aplikace než přiřazená?
Každá aplikace LUIS má klíč pro vytváření/starter v seznamu koncových bodů v zájmu usnadnění práce. Tento klíč umožňuje pouze několik přístupů koncový bod tak, že budete moct vyzkoušet LUIS.  

Pokud vaše aplikace existovala předtím, než služba LUIS je všeobecně dostupná (GA), jsou automaticky přiřadit klíče koncového bodu služby LUIS ve vašem předplatném. To se pro usnadnění migrace všeobecné dostupnosti. Jsou nějaké nové klíče koncového bodu služby LUIS na webu Azure Portal _není_ automaticky přiřazená k LUIS.

## <a name="key-management"></a>Správa klíčů

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Návody vědět, jakou klávesu potřebuji, jak ji získám a co s nimi mám dělat? 

Další informace o rozdílech mezi klíčovým slovem pro tvorbu a klíčovým modulem runtime najdete [v tématu klíče koncových bodů pro vytváření a předpovědi dotazů v Luis](luis-concept-keys.md) . 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Zobrazila se mi chyba při nedostatku kvóty. Jak ho mám opravit? 

Další informace najdete v tématu opravení stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Potřebuji zpracovat další dotazy koncového bodu. Návody to udělat? 

Další informace najdete v tématu opravení stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

## <a name="app-management"></a>Správa aplikací

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak se stáhnout protokol projevy uživatele?
Ve výchozím nastavení zaznamená aplikace LUIS projevy od uživatelů. Pokud chcete stáhnout protokol projevy, které uživatelům odeslat do vaší aplikace LUIS, přejděte na **Moje aplikace**a vyberte aplikaci. V kontextové nástrojů vyberte **exportovat protokoly koncového bodu**. Protokol je formátován jako soubor hodnot oddělených čárkami (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak lze zakázat protokolování projevy?
Protokolování projevy uživatele můžete vypnout nastavením `log=false` v adrese URL koncového bodu, který klientské aplikace používá k dotazování služby LUIS. Vypnutí protokolování ale, zakáže schopnost aplikace LUIS navrhnout projevy nebo zvýšit výkon, který je založen na [aktivně učit](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Pokud nastavíte `log=false` z důvodu ochrany osobních údajů se týká, nelze stáhnout záznam o těchto projevů uživatele služby luis nebo pomocí těchto projevů můžete aplikaci vylepšovat.

Protokolování je pouze úložiště projevy.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Proč nechcete všechny moje projevy koncový bod přihlášení?
Pokud používáte protokol pro předpověď analýzy, nezachytí projevy testu v protokolu.

## <a name="data-management"></a>Správa dat

### <a name="can-i-delete-data-from-luis"></a>Můžete odstranit data ze služby LUIS?

* Vždy můžete odstranit projevy příklad používá pro trénování služby LUIS. Pokud odstraníte utterance příklad z vaší aplikace LUIS, se odebere z webové služby LUIS a není k dispozici pro export.
* Projevy můžete odstranit ze seznamu uživatelů projevy, které navrhuje LUIS v **zkontrolujte koncový bod projevy** stránky. Odstraňuje se z tohoto seznamu projevy brání jejich navržena, ale nedojde k jejich odstranění z protokolů.
* Pokud odstraníte účet, se odstraní všechny aplikace, spolu s jejich příklad projevy a protokoly. Data se uchovávají na serverech za 60 dní, než je odstraníme trvale.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak Microsoft spravuje data zasílané na LUIS?

[Centrum](https://www.microsoft.com/trustcenter) vysvětluje závazcích a možnosti pro správu dat a přístup ve službách Azure.

## <a name="language-and-translation-support"></a>Podpora jazyka a překladu

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mám aplikaci v jednom jazyce a chcete vytvořit paralelní aplikace v jiném jazyce. Co je nejjednodušší způsob, jak to provést?
1. Exportujte aplikaci.
2. Převede uzel s popiskem projevy v souboru JSON aplikace exportovala do cílového jazyka.
3. Může být potřeba změnit názvy záměry a entity nebo je nechat, jak jsou.
4. Importujte finally, aby měla aplikace LUIS v cílovém jazyce aplikace.

## <a name="app-notification"></a>Oznámení aplikace

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Proč můžu získat e-mailu o tom, že jsem téměř překročení kvóty?
Klíč pro vytváření/starter je povolený jenom 1000 koncový bod dotazů za měsíc. Vytvoření klíče koncového bodu služby LUIS (bezplatné nebo placené) a používat při provádění dotazů koncový bod. Pokud provádíte dotazy koncového bodu z robota nebo v jiné klientské aplikaci, musíte změnit klíč koncového bodu služby LUIS existuje.

## <a name="bots"></a>Roboti

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUIS robot nepracuje. Co mám udělat?

Prvním problémem je izolovat, jestli problém souvisí s LUIS nebo nastane mimo middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Řešení potíží v LUIS
Předejte stejný utterance do LUIS z [koncového bodu Luis](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Pokud se zobrazí chyba, vyřešte problém v LUIS, dokud nebude chyba nadále vrácena. Běžné chyby patří:

* `Out of call volume quota. Quota will be replenished in <time>.`– Tento problém znamená, že buď potřebujete změnit z klíčového obsahu na [klíč koncového bodu](luis-how-to-azure-subscription.md) , nebo potřebujete změnit [úrovně služeb](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Řešení potíží v Azure Bot Service

Pokud používáte Azure bot Service a problém je, že se **test ve webovém chatu** vrátí `Sorry, my bot code is having an issue`, zkontrolujte protokoly:

1. V Azure Portal pro robota v části **Správa robota** vyberte **Build (sestavit**).
1. Otevřete Editor kódu online. 
1. V horním, modrém navigačním panelu vyberte název robota (druhá položka napravo).
1. V rozevíracím seznamu výsledek vyberte možnost **otevřít konzolu Kudu**.
1. Vyberte **soubory protokolu**a pak vyberte **aplikace**. Zkontrolujte všechny soubory protokolu. Pokud se ve složce aplikace nezobrazuje chyba, zkontrolujte všechny soubory protokolu v **souboru**protokolu. 
1. Nezapomeňte projekt znovu sestavit, pokud používáte zkompilovaný jazyk, jako je například C#.

> [!Tip] 
> Konzolu nástroje může nainstalovat i balíčky. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Vyřešte problém při ladění na místním počítači s rozhraním bot Framework. 

Další informace o místním ladění robota najdete v tématu věnovaném [ladění robota](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrace LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Kde je Moje aplikace LUIS vytvořené během procesu předplatného Azure web app bot?
Pokud vyberete šablonu služby LUIS a vyberte **vyberte** tlačítko v podokně šablony, v levém podokně se změní na zahrnují typ šablony a zeptá se v jaké oblasti se má vytvořit šablonu služby LUIS. I když procesu web app bot nevytváří LUIS předplatného.

![Oblasti bot LUIS šablony webové aplikace.](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké oblasti LUIS podporují dočištění řeči Bot Framework?
[Rozpoznávání řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) je podporována pouze pro aplikace LUIS v instanci střed (USA).

## <a name="api-programming-strategies"></a>Strategie programování API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Návody programově získat oblast LUIS prostředku? 

Pomocí ukázky LUIS můžete [najít oblast](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programově pomocí nástroje C# nebo Node. js. 

## <a name="luis-service"></a>Služba LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) k dispozici místně nebo v privátním cloudu?

Ano, můžete použít LUIS [kontejneru](luis-container-howto.md) pro tyto scénáře, pokud máte nezbytné připojení k měření využití. 

## <a name="migrating-to-the-next-version"></a>Migrace na další verzi

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Návody migrujete na verzi Preview V3 API? 

Viz [Průvodce migrací rozhraní API v2 na verzi v3 pro aplikace Luis](luis-migration-api-v3.md) .

## <a name="build-2019-conference-announcements"></a>Oznámení o konferenci Build 2019

Následující funkce byly vydány na konferenci Build 2019:

* [Verze Preview Průvodce migrací rozhraní V3 API](luis-migration-api-v3.md)
* [Vylepšený řídicí panel analýzy](luis-how-to-use-dashboard.md)
* [Vylepšené předem připravené domény](luis-reference-prebuilt-domains.md) 
* [Dynamické seznam entit](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Externí entity](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videa:

* [Jak používat konverzační službu Azure ke škálování vašeho podnikání pro novou generaci](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Další postup

Další informace o LUIS, najdete v následujících zdrojích:
* [Dotazy přetečení zásobníku označené LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)