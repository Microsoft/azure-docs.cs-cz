---
title: Nejčastější dotazy
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje odpovědi na nejčastější dotazy o Language Understanding (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/15/2019
ms.author: diberry
ms.openlocfilehash: 71fb289f03467147acb2ef254ab87a2c753bc75c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416543"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding – nejčastější dotazy (FAQ)

Tento článek obsahuje odpovědi na nejčastější dotazy o Language Understanding (LUIS).

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
Chcete-li upravit aplikace LUIS prostřednictvím kódu programu, použijte [rozhraní API pro vytváření](https://aka.ms/luis-authoring-apis). Zobrazit [LUIS volání rozhraní API pro vytváření](./luis-quickstart-node-add-utterance.md) a [sestavení aplikace LUIS programově pomocí Node.js](./luis-tutorial-node-import-utterances-csv.md) příklady toho, jak volat rozhraní API pro vytváření. Rozhraní API pro vytváření vyžaduje použití [vytváření klíč](luis-concept-keys.md#authoring-key) místo klíče rozhraní koncového bodu. Programové vytváření umožňuje až 1 000 000 volání za měsíc a pět transakcí za sekundu. Další informace o klíče pomocí služby LUIS, naleznete v tématu [spravovat klíče](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Pokud je odpovídající vzor funkci, která poskytuje regulární výraz?
Předchozí **funkce** je momentálně zastaralý, nahrazuje  **[vzory](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak používat entity a vytáhnout správná data?
Zobrazit [entity](luis-concept-entity-types.md) a [extrakce dat](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variace utterance příkladu by měl obsahovat interpunkce?
Přidejte různými variantami jako příklad projevy k příslušnému záměru nebo vzorec, podle kterého utterance příklad s [syntaxe Ignorovat](luis-concept-patterns.md#pattern-syntax) znaky interpunkce.

### <a name="does-luis-currently-support-cortana"></a>Služba LUIS aktuálně podporuje Cortany?

Cortana, které předem sestavených aplikací byla vyřazena jako zastaralá v 2017. Již nejsou podporovány.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak se převést vlastnictví aplikace LUIS?
Aplikace LUIS přenést do jiného předplatného Azure, aplikace LUIS exportujte a importujte ho pomocí nového účtu. Aktualizujte ID aplikace LUIS v klientské aplikaci, která je volá. Nová aplikace může vrátit mírně odlišné LUIS skóre z původní aplikace.

<a name="luis-collaborating"></a>

## <a name="collaborating"></a>Spolupráce

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Jak můžu poskytnout spolupracovníci přístup k LUIS s Azure Active Directory (Azure AD) nebo řízení přístupu na základě Role (RBAC)?

Zobrazit [prostředky služby Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) a [uživatel tenanta Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) se naučíte udělit přístup spolupracovníky. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Koncový bod

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Můj dotaz koncový bod vrátil neočekávané výsledky. Co bych měl/a dělat?

Neočekávaný dotaz predikované výsledky jsou založeny na stav publikovaného modelu. Chcete-li model, je může potřebovat změnit model, trénování a publikujte ho znovu. 

Oprava modelu začíná [aktivně učit](luis-how-to-review-endoint-utt.md).

Můžete odebrat Nedeterministický školení aktualizací [aplikace verze nastavení rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aby bylo možné používat všechny trénovací data.

Zkontrolujte [osvědčené postupy](luis-concept-best-practices.md) další tipy. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč služba LUIS přidá mezery v dotazu kolem nebo uprostřed slova?
Služba LUIS [tokenizes](luis-glossary.md#token) na základě utterance [jazykovou verzi](luis-language-support.md#tokenization). Jsou k dispozici pro původní hodnotu a hodnotu tokenizovaná [extrakce dat](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak vytvořit a přiřadit klíče koncového bodu LUIS?
[Vytvoření klíče koncového bodu](luis-how-to-azure-subscription.md) v Azure pro vaše [služby](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) úroveň. [Přiřazení klíče](luis-how-to-azure-subscription.md) na **[klíče a koncových bodů](luis-how-to-azure-subscription.md)** stránky. Neexistuje žádná odpovídající rozhraní API pro tuto akci. Pak musíte změnit požadavek HTTP na koncový bod, který [používání nového klíče koncového bodu](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Jak se interpretují skóre, které se služba LUIS?
Systém by měl používat nejvyšší hodnocení záměr bez ohledu na jeho hodnotu. Například skóre pod 0,5 (méně než 50 %) nemusí nutně znamenat, že má služba LUIS s nízkou spolehlivostí. Poskytuje další trénovací data můžou pomoct zvýšit [skóre](luis-concept-prediction-score.md) nejpravděpodobnější záměru.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Proč nevidím moje přístupů koncový bod v řídicí panel Moje aplikace?
Celkový počet koncových bodů přístupů na řídicím panelu vaší aplikace jsou pravidelně aktualizovány, ale metriky související s vaší klíče koncového bodu služby LUIS na webu Azure Portal se aktualizuje častěji.

Pokud se nezobrazí aktualizované koncový bod přístupů na řídicím panelu, přihlaste se k webu Azure portal a najít prostředek přidružený klíč koncového bodu služby LUIS a otevřete **metriky** vyberte **volání celkem** metriku. Pokud klíč koncového bodu se používá pro více než jednu aplikaci LUIS, metriky na webu Azure Portal zobrazí souhrnný počet volání ze všech aplikací LUIS, které ji používají.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Je k dispozici PowerShell příkaz získat koncový bod kvóty?

Příkaz prostředí PowerShell můžete zobrazit kvóty koncový bod:

```powershell
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moje aplikace LUIS pracoval včerejšího dne, ale ještě dnes se zobrazují chyby 403. Můžu aplikaci nezměnila. Jak ho mám opravit?
Následující [pokyny](#how-do-i-create-and-assign-a-luis-endpoint-key) v další nejčastější dotazy k vytvoření klíče koncového bodu služby LUIS a přiřaďte ho do aplikace. Pak musíte změnit požadavek HTTP na koncový bod, který [používání nového klíče koncového bodu](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpečit tento koncový bod služby LUIS?
Zobrazit [zabezpečení koncového bodu](luis-concept-security.md#securing-the-endpoint).

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

Zobrazit [předpovědi rozdíly mezi kopie stejnou aplikaci](luis-concept-prediction-score.md#differences-with-predictions).

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

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Jak poznám, že klíč, co budu potřebovat, kde můžu získat, a co ho můžu použít? 

Naleznete v tématu [vytváření obsahu a dotaz předpovědi klíče koncového bodu v LUIS](luis-concept-keys.md) Další informace o rozdílech mezi [vytváření klíč](luis-how-to-account-settings.md) a [klíče koncového bodu předpovědi](luis-how-to-azure-subscription.md). 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Zobrazila se chyba o překročení kvóty. Jak ho mám opravit? 

Zobrazit, [k vyřešení chyby limit kvóty, pokud klíč překročí využití cenové úrovně](luis-how-to-azure-subscription.md##how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage) Další informace.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Potřebuji další koncový bod dotazy zpracovávají. Jak to mohu provést? 

Zobrazit, [k vyřešení chyby limit kvóty, pokud klíč překročí využití cenové úrovně](luis-how-to-azure-subscription.md##how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage) Další informace.



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

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Moje LUIS bot nefunguje. Co mám udělat?

Prvním problémem, je izolovat-li tento problém má vztah k LUIS nebo se stane mimo LUIS middlewaru. 

#### <a name="resolve-issue-in-luis"></a>Řešení potíží v LUIS
Předat stejnou utterance LUIS z [koncový bod služby LUIS](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance). Pokud obdržíte chybu, dokud se už vrátila řešení problému s LUIS. Běžné chyby patří:

* `Out of call volume quota. Quota will be replenished in <time>.` – Tento problém znamená, že buď potřebujete změnit z vytváření klíče do [klíče koncového bodu](luis-how-to-azure-subscription.md) nebo budete muset změnit [úrovně služeb](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Řešení potíží v Azure Bot Service

Pokud používáte Azure Bot Service a problému je, že **testování ve Web Chat** vrátí `Sorry, my bot code is having an issue`, najdete v protokolech:

1. Na webu Azure Portal, pro svého robota z **Bot správu** vyberte **sestavení**.
1. Otevřete editor kódu online. 
1. Na začátek, modrá navigačním panelu vyberte název robota (druhá položka určená k pravé straně).
1. V seznamu výsledků rozevíracího seznamu vyberte **otevřete konzoly Kudu**.
1. Vyberte **LogFiles**a pak vyberte **aplikace**. Projděte si všechny soubory protokolu. Pokud nevidíte ve složce aplikace zobrazí chyba, zkontrolujte všechny soubory protokolu v **LogFiles**. 
1. Nezapomeňte znovu sestavit projekt, pokud používáte jazyk kompilovaný jako C#.

> [!Tip] 
> Balíčky můžete nainstalovat také konzole. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Řešení potíží při ladění na místním počítači pomocí rozhraní Bot Framework. 

Další informace o místním ladění kódu robota, naleznete v tématu [ladění robota](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrace LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Kde je Moje aplikace LUIS vytvořené během procesu předplatného Azure web app bot?
Pokud vyberete šablonu služby LUIS a vyberte **vyberte** tlačítko v podokně šablony, v levém podokně se změní na zahrnují typ šablony a zeptá se v jaké oblasti se má vytvořit šablonu služby LUIS. I když procesu web app bot nevytváří LUIS předplatného.

![Oblasti bot LUIS šablony webové aplikace.](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké oblasti LUIS podporují dočištění řeči Bot Framework?
[Rozpoznávání řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) je podporována pouze pro aplikace LUIS v instanci střed (USA).

## <a name="api-programming-strategies"></a>Strategie programování rozhraní API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Jak prostřednictvím kódu programu získat oblasti LUIS prostředku? 

Použijte ukázku služby LUIS [najít oblast](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programově pomocí C# nebo Node.Js. 

## <a name="luis-service"></a>Služba LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) k dispozici místně nebo v privátním cloudu?

Ano, můžete použít LUIS [kontejneru](luis-container-howto.md) pro tyto scénáře, pokud máte nezbytné připojení k měření využití. 

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na konferenci Build 2018 funkce umožňující porozumět jazyku nebo ukázka slyšeli, ale nepamatuji byla volána?

Následující funkce byly vydány na konferenci Build 2018:

|Název|Obsah|
|--|--|
|Vylepšení|[Regulární výraz](luis-concept-data-extraction.md##regular-expression-entity-data) entity a [klíčových frází to](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entity
|Vzory|Vzory [koncept](luis-concept-patterns.md), [kurzu](luis-tutorial-pattern.md), [s postupy](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entity koncept včetně [explicitní seznam](luis-concept-patterns.md#explicit-lists) pro výjimky<br>[Role](luis-concept-roles.md) koncept|
|Integrace|[Rozhraní text analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrace [analýzu subjektivního hodnocení](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>[Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech) integrace dočištění řeči ve spojení s [sadou SDK pro řeč](https://aka.ms/SpeechSDK)|
|Nástroj pro odesílání|Součást [BotBuilder nástroje](https://github.com/Microsoft/botbuilder-tools), odeslání příkazového řádku [nástroj](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) zkombinovat více LUIS a nástroje QnA Maker aplikace do jedné aplikace LUIS pro lepší rozpoznání záměru v robota

Další vytváření [trasy rozhraní API](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/authoring-routes.md) byly zahrnuty.

Videa:
* [Azure Friday At Build 2018: Cognitive Services – Language (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - co je nového službou Language Understanding](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Seminář na konferenci Build 2018 – Inteligence robotů, možnosti využití řeči a osvědčené postupy NLU](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - aktualizace LUIS](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekty:
* [Contoso Cafe bot](https://github.com/botbuilderbuild2018/build2018demo) demo - zdrojového kódu na Githubu

## <a name="next-steps"></a>Další postup

Další informace o LUIS, najdete v následujících zdrojích:
* [Dotazy přetečení zásobníku označené LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
