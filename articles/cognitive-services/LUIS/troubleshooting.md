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
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a2472064720af0a25568a2f173b971898b1f2e25
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393706"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Nejčastější dotazy Language Understanding

Tento článek obsahuje odpovědi na nejčastější dotazy o Language Understanding (LUIS).

## <a name="whats-new"></a>Co je nového

[Přečtěte si další](whats-new.md) informace o novinkách v Language UNDERSTANDING (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Vytváření obsahu

### <a name="what-are-the-luis-best-practices"></a>Co jsou doporučené postupy LUIS?
Začněte s [cyklem vytváření](luis-concept-app-iteration.md)a pak si přečtěte [osvědčené postupy](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Co je nejlepší způsob, jak můžete začít sestavovat aplikaci v LUIS?

Nejlepším způsobem, jak sestavit aplikaci, je [přírůstkový proces](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co je dobrým zvykem model záměry Moje aplikace? Je vhodné vytvořit konkrétnější nebo obecnějším záměry?

Zvolte záměrů, které nejsou tak obecné by se překrývají, ale nejsou tak specifické, že to ztěžuje LUIS k rozlišení mezi podobné záměry. Vytváření discriminative konkrétní záměry je jedním z osvědčených postupů pro modelování LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>Je důležité pro trénování záměru žádný?

Ano, je dobré naučit svůj záměr **none** s více projevy při přidávání dalších popisků k ostatním záměrům. Dobrý poměr je 1 nebo 2 přidaných popisků k **žádnému** pro každých 10 popisků přidaných k záměru. Tento poměr zvyšuje discriminative sílu LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak může oprava pravopisných chyb ve projevy?

Přečtěte si kurz [rozhraní API Bingu pro kontrolu pravopisu v7](luis-tutorial-bing-spellcheck.md) . Služba LUIS vynucuje omezení, která API V7 kontrola pravopisu Bingu.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak mohu programově upravit aplikaci LUIS?
Pokud chcete aplikaci LUIS upravit programově, použijte [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087). Příklady, jak volat rozhraní API pro vytváření, najdete v tématu [volání rozhraní API pro volání Luis](./get-started-get-model-rest-apis.md) a [Vytvoření aplikace Luis programově pomocí Node. js](./luis-tutorial-node-import-utterances-csv.md) . Rozhraní API pro vytváření obsahu vyžaduje použití [klíčového obsahu](luis-concept-keys.md#azure-resources-for-luis) místo klíče koncového bodu. Programové vytváření umožňuje až 1 000 000 volání za měsíc a pět transakcí za sekundu. Další informace o klíčích, které používáte se službou LUIS, najdete v tématu [Správa klíčů](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Pokud je odpovídající vzor funkci, která poskytuje regulární výraz?
Předchozí **funkce vzoru** je aktuálně zastaralá, Nahrazená **[vzorci](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak používat entity a vytáhnout správná data?
Viz [entity](luis-concept-entity-types.md) a [extrakce dat](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variace utterance příkladu by měl obsahovat interpunkce?
Přidejte různé variace jako příklad projevy k záměru nebo přidejte vzor ukázkového utterance s [syntaxí pro ignorování](luis-concept-patterns.md#pattern-syntax) interpunkce.

### <a name="does-luis-currently-support-cortana"></a>Služba LUIS aktuálně podporuje Cortany?

Cortana, které předem sestavených aplikací byla vyřazena jako zastaralá v 2017. Již nejsou podporovány.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak se převést vlastnictví aplikace LUIS?
Aplikace LUIS přenést do jiného předplatného Azure, aplikace LUIS exportujte a importujte ho pomocí nového účtu. Aktualizujte ID aplikace LUIS v klientské aplikaci, která je volá. Nová aplikace může vrátit mírně odlišné LUIS skóre z původní aplikace.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Předem vytvořená entita je označena jako utterance jako místo vlastní entity. Návody opravit? 

Na portálu LUIS můžete označit text pro přesně entitu, které vás zajímá při extrakci. Pokud LUIS Portal nezobrazuje správnou předpověď entit, možná budete muset přidat další projevy a označit entitu v rámci textu nebo Přidat popisovač (například funkci). 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Pokusili jste se importovat soubor aplikace nebo verze, ale zobrazila se chyba, co se stalo? 

Přečtěte si další informace o [chybách importu verzí](luis-how-to-manage-versions.md#import-errors).

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

Oprava modelu začíná [aktivním učením](luis-how-to-review-endpoint-utterances.md).

Nedeterministické školení můžete odebrat tak, že aktualizujete [rozhraní API pro nastavení verze aplikace](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) , aby se mohla používat všechna školicí data.

Projděte si [osvědčené postupy](luis-concept-best-practices.md) pro další tipy. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč služba LUIS přidá mezery v dotazu kolem nebo uprostřed slova?
LUIS [tokenizes](luis-glossary.md#token) utterance na základě [jazykové verze](luis-language-support.md#tokenization). Pro [extrakci dat](luis-concept-data-extraction.md#tokenized-entity-returned)jsou k dispozici jak původní hodnota, tak i hodnota s tokeny.

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak vytvořit a přiřadit klíče koncového bodu LUIS?
[Vytvořte v Azure klíč koncového bodu](luis-how-to-azure-subscription.md) pro vaši úroveň [služeb](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) . [Přiřaďte klíč](luis-how-to-azure-subscription.md) na stránce **[prostředky Azure](luis-how-to-azure-subscription.md)** . Neexistuje žádná odpovídající rozhraní API pro tuto akci. Pak musíte požadavek HTTP změnit na koncový bod, aby [používal nový klíč koncového bodu](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Jak se interpretují skóre, které se služba LUIS?
Systém by měl používat nejvyšší hodnocení záměr bez ohledu na jeho hodnotu. Například skóre pod 0,5 (méně než 50 %) nemusí nutně znamenat, že má služba LUIS s nízkou spolehlivostí. Poskytování více školicích dat může přispět ke zvýšení [skóre](luis-concept-prediction-score.md) nejpravděpodobnějšího záměru.

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
Viz [zabezpečení koncového bodu](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Práce v mezích limitů služby LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaký je maximální počet záměry a entity, které může podporovat aplikace LUIS?
Podívejte se na odkaz [hranice](luis-boundaries.md) .

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chci, aby k sestavení aplikace LUIS s více než maximální počet záměry. Co bych měl/a dělat?

Podívejte se [na osvědčené postupy pro záměry](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chci vytvořit aplikaci v LUIS s více než maximální počet entit. Co bych měl/a dělat?

Zobrazit [osvědčené postupy pro entity](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jaká jsou omezení na počtu a velikosti frázi uvádí?
Maximální délku [seznamu frází](./luis-concept-feature.md)najdete v odkazu na [hranice](luis-boundaries.md) .

### <a name="what-are-the-limits-on-example-utterances"></a>Jaká jsou omezení na příkladu projevy?
Podívejte se na odkaz [hranice](luis-boundaries.md) .

## <a name="testing-and-training"></a>Testování a školení

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Zobrazila se některé chyby ve službě batch testování podokně u některých modelů v aplikaci. Jak vyřešit tento problém?

Chyby označují, že je některá nesoulad mezi popisky a předpovědi z vašich modelů. K vyřešení problému, proveďte jednu z následujících úloh:
* Chcete-li LUIS zlepšit diskriminaci záměrů, přidejte další popisky.
* Chcete-li LUIS při učení, Přidat frázi seznam funkcí, které představují slovník jazyka specifického pro doménu.

Podívejte se na kurz [dávkového testování](luis-tutorial-batch-testing.md) .

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Pokud aplikace je exportovat a importovat do nové aplikace (s novým ID aplikace), skóre predikcí služby LUIS se liší. Proč k tomu dochází?

Podívejte se [na rozdíly předpovědi mezi kopiemi stejné aplikace](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Některé projevy přejděte do nesprávného záměr po provedené změny aplikace. Zdá se, že problém náhodně zmizí. Jak ho mám opravit? 

Podívejte [se na téma výuka se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikování aplikací

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co je ID tenanta v okně "Přidání klíče do vaší aplikace"?
V Azure představuje tenanta klient nebo organizace, která souvisí se službou. V Azure Portal v poli **ID adresáře** Najděte ID tenanta tak, že vyberete **Azure Active Directory** > **Spravovat** > ové **vlastnosti**.

![ID tenanta na portálu Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Proč existují další klíče koncového bodu přiřadit do mojí aplikace než přiřazená?
Každá aplikace LUIS má klíč pro vytváření/starter v seznamu koncových bodů v zájmu usnadnění práce. Tento klíč umožňuje pouze několik přístupů koncový bod tak, že budete moct vyzkoušet LUIS.  

Pokud vaše aplikace existovala předtím, než služba LUIS je všeobecně dostupná (GA), jsou automaticky přiřadit klíče koncového bodu služby LUIS ve vašem předplatném. To se pro usnadnění migrace všeobecné dostupnosti. Všechny nové klíče koncového bodu LUIS v Azure Portal _nejsou automaticky přiřazeny_ k Luis.

## <a name="key-management"></a>Správa klíčů

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Návody vědět, jakou klávesu potřebuji, jak ji získám a co s nimi mám dělat? 

Další informace o rozdílech mezi klíčovým slovem pro tvorbu a klíčovým modulem runtime najdete [v tématu klíče koncových bodů pro vytváření a předpovědi dotazů v Luis](luis-concept-keys.md) . 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Zobrazila se mi chyba při nedostatku kvóty. Jak ho mám opravit? 

Další informace najdete v tématu opravení stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Potřebuji zpracovat další dotazy koncového bodu. Návody to udělat? 

Další informace najdete v tématu opravení stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Vytvořili jsem klíč pro vytváření obsahu, ale nezobrazuje se na portálu LUIS. Co se stalo?

Vytváření klíčů je k dispozici na portálu LUIS po [migraci na prostředí pro tvorbu kódu](luis-migration-authoring.md).  

## <a name="app-management"></a>Správa aplikací

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak se stáhnout protokol projevy uživatele?
Ve výchozím nastavení zaznamená aplikace LUIS projevy od uživatelů. Pokud si chcete stáhnout protokol projevy, který uživatelé odesílají do vaší aplikace LUIS, pokračujte na **Moje aplikace**a vyberte aplikaci. Na panelu nástrojů kontext vyberte **exportovat protokoly koncových bodů**. Protokol je formátován jako soubor hodnot oddělených čárkami (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak lze zakázat protokolování projevy?
Protokolování uživatele projevy můžete vypnout nastavením `log=false` v adrese URL koncového bodu, kterou klientská aplikace používá pro dotazování LUIS. Vypnutí protokolování ale zakáže schopnost vaší aplikace LUIS navrhovat projevy nebo zdokonalovat výkon, který je založený na [aktivním učení](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Pokud jste nastavili `log=false` z důvodu ochrany osobních údajů, nemůžete stáhnout záznam o uživatelích projevy z LUIS ani použít tyto projevy k vylepšení vaší aplikace.

Protokolování je pouze úložiště projevy.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Proč nechcete všechny moje projevy koncový bod přihlášení?
Pokud používáte protokol pro předpověď analýzy, nezachytí projevy testu v protokolu.

## <a name="data-management"></a>Správa dat

### <a name="can-i-delete-data-from-luis"></a>Můžete odstranit data ze služby LUIS?

* Vždy můžete odstranit projevy příklad používá pro trénování služby LUIS. Pokud odstraníte utterance příklad z vaší aplikace LUIS, se odebere z webové služby LUIS a není k dispozici pro export.
* Projevy je možné odstranit ze seznamu uživatelských projevy, který LUIS navrhuje na stránce **zkontrolovat koncový bod pro projevy** . Odstraňuje se z tohoto seznamu projevy brání jejich navržena, ale nedojde k jejich odstranění z protokolů.
* Pokud odstraníte účet, se odstraní všechny aplikace, spolu s jejich příklad projevy a protokoly. Data se uchovávají na serverech za 60 dní, než je odstraníme trvale.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak Microsoft spravuje data zasílané na LUIS?

[Centrum zabezpečení](https://www.microsoft.com/trustcenter) vysvětluje naše závazky a možnosti pro správu a přístup k datům ve službách Azure.

## <a name="language-and-translation-support"></a>Podpora jazyka a překladu

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mám aplikaci v jednom jazyce a chcete vytvořit paralelní aplikace v jiném jazyce. Co je nejjednodušší způsob, jak to provést?
1. Exportujte aplikaci.
2. Převede uzel s popiskem projevy v souboru JSON aplikace exportovala do cílového jazyka.
3. Může být potřeba změnit názvy záměry a entity nebo je nechat, jak jsou.
4. Importujte finally, aby měla aplikace LUIS v cílovém jazyce aplikace.

## <a name="app-notification"></a>Oznámení aplikace

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Proč můžu získat e-mailu o tom, že jsem téměř překročení kvóty?
Klíč pro vytváření/starter je povolený jenom 1000 koncový bod dotazů za měsíc. Vytvoření klíče koncového bodu služby LUIS (bezplatné nebo placené) a používat při provádění dotazů koncový bod. Pokud provádíte dotazy koncového bodu z robota nebo v jiné klientské aplikaci, musíte změnit klíč koncového bodu služby LUIS existuje.

## <a name="bots"></a>Roboty

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUIS robot nepracuje. Co mám udělat?

Prvním problémem je izolovat, jestli problém souvisí s LUIS nebo nastane mimo middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Řešení potíží v LUIS
Předejte stejný utterance do LUIS z [koncového bodu Luis](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Pokud se zobrazí chyba, vyřešte problém v LUIS, dokud nebude chyba nadále vrácena. Běžné chyby patří:

* `Out of call volume quota. Quota will be replenished in <time>.` – tento problém znamená, že buď potřebujete změnit z klíčového obsahu na [klíč koncového bodu](luis-how-to-azure-subscription.md) , nebo potřebujete změnit [úrovně služeb](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Řešení potíží v Azure Bot Service

Pokud používáte Azure Bot Service a problém je, že **test ve webovém chatu** vrátí `Sorry, my bot code is having an issue`, zkontrolujte své protokoly:

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
Pokud vyberete šablonu LUIS a v podokně šablona vyberete tlačítko **Vybrat** , podokno vlevo se změní tak, aby obsahovalo typ šablony, a zobrazí dotaz, v jaké oblasti se má vytvořit šablona Luis. I když procesu web app bot nevytváří LUIS předplatného.

![Oblasti bot LUIS šablony webové aplikace.](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké oblasti LUIS podporují dočištění řeči Bot Framework?
Dočistění [řeči](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) je podporované jenom pro aplikace Luis v centrální instanci (US).

## <a name="api-programming-strategies"></a>Strategie programování API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Návody programově získat oblast LUIS prostředku? 

Pomocí ukázky LUIS můžete [najít oblast](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programově pomocí nástroje C# nebo Node. js. 

## <a name="luis-service"></a>Služba LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) k dispozici místně nebo v privátním cloudu?

Ano, [kontejner](luis-container-howto.md) Luis můžete použít pro tyto scénáře, pokud máte nezbytné připojení k využití měřiče. 

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

## <a name="next-steps"></a>Další kroky

Další informace o LUIS, najdete v následujících zdrojích:
* [Stack Overflow dotazy označené pomocí LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum služby MSDN Language Understanding Intelligent Services (LUIS)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
