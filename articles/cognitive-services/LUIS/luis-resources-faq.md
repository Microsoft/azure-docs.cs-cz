---
title: Language Understanding (LUIS) v Azure – nejčastější dotazy | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy o Language Understanding (LUIS)
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 291eb9ef7d8d089b443b55ac181e9fdd65fcb413
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888853"
---
# <a name="language-understanding-faq"></a>Language Understanding – nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy o Language Understanding (LUIS).

## <a name="luis-authoring"></a>Služba LUIS pro tvorbu

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

## <a name="luis-endpoint"></a>Koncový bod služby LUIS

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč služba LUIS přidá mezery v dotazu kolem nebo uprostřed slova?
Služba LUIS [tokenizes](luis-glossary.md#token) na základě utterance [jazykovou verzi](luis-supported-languages.md#tokenization). Jsou k dispozici pro původní hodnotu a hodnotu tokenizovaná [extrakce dat](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak vytvořit a přiřadit klíče koncového bodu LUIS?
[Vytvoření klíče koncového bodu](luis-how-to-azure-subscription.md#create-luis-endpoint-key) v Azure pro vaše [služby](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) úroveň. [Přiřazení klíče](luis-how-to-manage-keys.md#assign-endpoint-key) na **[publikovat](luis-how-to-publish-app.md)** stránky. Neexistuje žádná odpovídající rozhraní API pro tuto akci. Pak musíte změnit požadavek HTTP na koncový bod, který [používání nového klíče koncového bodu](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Jak se interpretují skóre, které se služba LUIS? 
Systém by měl používat nejvyšší hodnocení záměr bez ohledu na jeho hodnotu. Například skóre pod 0,5 (méně než 50 %) nemusí nutně znamenat, že má služba LUIS s nízkou spolehlivostí. Poskytuje další trénovacích dat můžete zvýšit skóre nejpravděpodobnější záměr.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Proč nevidím moje přístupů koncový bod v řídicí panel Moje aplikace?
Celkový počet koncových bodů přístupů na řídicím panelu vaší aplikace jsou pravidelně aktualizovány, ale metriky související s vaší klíče koncového bodu služby LUIS na webu Azure Portal se aktualizuje častěji. 

Pokud se nezobrazí aktualizované koncový bod přístupů na řídicím panelu, přihlaste se k webu Azure portal a najít prostředek přidružený klíč koncového bodu služby LUIS a otevřete **metriky** vyberte **volání celkem** metriku. Pokud klíč koncového bodu se používá pro více než jednu aplikaci LUIS, metriky na webu Azure Portal zobrazí souhrnný počet volání ze všech aplikací LUIS, které ji používají.

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

## <a name="app-publishing"></a>Publikování aplikací

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co je ID tenanta v okně "Přidání klíče do vaší aplikace"?
V Azure představuje tenanta klient nebo organizace, který je spojen s využitím služby. Svoje ID tenanta zjistíte na webu Azure Portal v **ID adresáře** pole tak, že vyberete **Azure Active Directory** > **spravovat**  >  **Vlastnosti**.

![ID tenanta na portálu Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
### <a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Proč jsou existuje více klíčů koncový bod v mé aplikaci publikovat stránku než přiřadí aplikaci? 
Každá aplikace LUIS má klíč pro vytváření/starter. Klíče koncového bodu služby LUIS vytvořené během časového rámce všeobecné dostupnosti jsou zobrazeny na stránce publikování, bez ohledu na to, pokud jste přidali do aplikace. To se pro usnadnění migrace všeobecné dostupnosti. Všechny nové klíče koncového bodu služby LUIS se nezobrazí na stránce publikovat. 

## <a name="app-management"></a>Správa aplikací

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak se převést vlastnictví aplikace LUIS?
Aplikace LUIS přenést do jiného předplatného Azure, aplikace LUIS exportujte a importujte ho pomocí nového účtu. Aktualizujte ID aplikace LUIS v klientské aplikaci, která je volá. Nová aplikace může vrátit mírně odlišné LUIS skóre z původní aplikace. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak se stáhnout protokol projevy uživatele?
Ve výchozím nastavení zaznamená aplikace LUIS projevy od uživatelů. Pokud chcete stáhnout protokol projevy, které uživatelům odeslat do vaší aplikace LUIS, přejděte na **Moje aplikace**a klikněte na symbol tří teček (***...*** ) v seznamu pro vaši aplikaci. Pak klikněte na tlačítko **exportovat protokoly koncového bodu**. Protokol je formátován jako soubor hodnot oddělených čárkami (CSV).

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

## <a name="language-and-translation-support"></a>Podpora jazyka a překladu 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mám aplikaci v jednom jazyce a chcete vytvořit paralelní aplikace v jiném jazyce. Co je nejjednodušší způsob, jak to provést?
1. Exportujte aplikaci.
2. Převede uzel s popiskem projevy v souboru JSON aplikace exportovala do cílového jazyka.
3. Může být potřeba změnit názvy záměry a entity nebo je nechat, jak jsou.
4. Importujte finally, aby měla aplikace LUIS v cílovém jazyce aplikace.

## <a name="app-notification"></a>Oznámení aplikace

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Proč můžu získat e-mailu o tom, že jsem téměř překročení kvóty?
Klíč pro vytváření/starter je povolený jenom 1000 koncový bod dotazů za měsíc. Vytvoření klíče koncového bodu služby LUIS (bezplatné nebo placené) a používat při provádění dotazů koncový bod. Pokud provádíte dotazy koncového bodu z robota nebo v jiné klientské aplikaci, musíte změnit klíč koncového bodu služby LUIS existuje. 

## <a name="integrating-luis"></a>Integrace LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Kde je Moje aplikace LUIS vytvořené během procesu předplatného Azure web app bot?
Pokud vyberete šablonu služby LUIS a vyberte **vyberte** tlačítko v podokně šablony, v levém podokně se změní na zahrnují typ šablony a zeptá se v jaké oblasti se má vytvořit šablonu služby LUIS. I když procesu web app bot nevytváří LUIS předplatného.

![Oblasti bot LUIS šablony webové aplikace.](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké oblasti LUIS podporují dočištění řeči Bot Framework?
[Rozpoznávání řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) je podporována pouze pro aplikace LUIS v instanci střed (USA). 

## <a name="luis-service"></a>Služba LUIS 

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>Služba LUIS je k dispozici místně nebo v privátním cloudu?
Ne. 

## <a name="changes-to-the-docs"></a>Změny do této dokumentace

### <a name="where-did-the-tutorials-go"></a>Kde najdu kurzy? 
Články, které byly dříve v části kurzu jsou teď v části dokumenty s postupy. 

|Kurz|
|--|
|Integrace LUIS s využitím botu pomocí [jazyka C#](luis-csharp-tutorial-build-bot-framework-sample.md) a [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Přidat službu Application Insights k Robotovi s [jazyka C#](luis-tutorial-bot-csharp-appinsights.md) a [Node.js](luis-tutorial-function-appinsights.md)|
|Sestavení aplikace LUIS programově pomocí [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Použití [složený entity](luis-tutorial-composite-entity.md) extrahovat seskupených dat|
|Přidat [seznam entit](luis-tutorial-list-entity.md) k detekci zvýšení entity pomocí Node.js|
|Zvyšte přesnost předpovědi pomocí [seznam frází](luis-quickstart-primary-and-secondary-data.md), [vzory](luis-tutorial-pattern.md), a [testování služby batch](luis-tutorial-batch-testing.md)|
|[Opravte pravopis](luis-tutorial-batch-testing.md) API kontrola pravopisu Bingu v7

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na konferenci Build 2018 funkce umožňující porozumět jazyku nebo ukázka slyšeli, ale nepamatuji byla volána? 

Následující funkce byly vydány na konferenci Build 2018:

|Název|Obsah|
|--|--|
|Vylepšení|[Regulární výraz](luis-concept-data-extraction.md##regular-expression-entity-data) entity a [klíčových frází to](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entity
|Vzory|Vzory [koncept](luis-concept-patterns.md), [kurzu](luis-tutorial-pattern.md), [s postupy](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entity koncept včetně [explicitní seznam](luis-concept-patterns.md#explicit-lists) pro výjimky<br>[Role](luis-concept-roles.md) koncept|
|Integrace|[Rozhraní text analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrace [analýzu subjektivního hodnocení](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>[Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech) integrace [řeči dočištění](luis-how-to-publish-app.md#enable-speech-priming) ve spojení s [sadou SDK pro řeč](https://aka.ms/SpeechSDK)|
|Nástroj pro odesílání|Součást [BotBuilder nástroje](https://github.com/Microsoft/botbuilder-tools), odeslání příkazového řádku [nástroj](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) zkombinovat více LUIS a nástroje QnA Maker aplikace do jedné aplikace LUIS pro lepší rozpoznání záměru v robota

Další vytváření [trasy rozhraní API](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) byly zahrnuty. 

Videa: 
* [Azure Friday na sestavení 2018: Cognitive Services – Language (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - co je nového službou Language Understanding](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Seminář na konferenci Build 2018 – Inteligence robotů, možnosti využití řeči a osvědčené postupy NLU](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - aktualizace LUIS](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekty: 
* [Contoso Cafe bot](https://github.com/botbuilderbuild2018/build2018demo) demo - zdrojového kódu na Githubu

## <a name="next-steps"></a>Další postup

Další informace o LUIS, najdete v následujících zdrojích:
* [Dotazy přetečení zásobníku označené LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 