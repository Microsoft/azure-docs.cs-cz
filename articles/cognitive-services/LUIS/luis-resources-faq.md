---
title: Nejčastější dotazy k pochopení jazyka (LEOŠ) v Azure | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy o znalosti jazyka (LEOŠ)
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 9804b9d5187637fe509d79134f93280d93daca76
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266137"
---
# <a name="language-understanding-faq"></a>Nejčastější dotazy k pochopení jazyka

Tento článek obsahuje odpovědi na nejčastější dotazy o znalosti jazyka (LEOŠ).

## <a name="luis-authoring"></a>LEOŠ vytváření

### <a name="what-are-the-luis-best-practices"></a>Jaké jsou doporučené postupy LEOŠ? 
Začínat [vytváření cyklu](luis-concept-app-iteration.md), přečtěte si [osvědčené postupy](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Co je nejlepší způsob, jak začít vytvářet aplikace my v LEOŠ?

Nejlepší způsob, jak sestavit aplikaci je prostřednictvím [přírůstkové proces](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co je dobrým zvykem model tříd Intent Moje aplikace? Lze vytvořit více konkrétních nebo obecnější záměry?

Zvolte záměry, které nejsou tak obecné tak, aby byly překrývající se, ale není tak konkrétní, aby ho ztěžuje LEOŠ rozlišovat podobné záměry. Vytváření discriminative konkrétní záměry patří mezi osvědčené postupy pro LEOŠ modelování.

### <a name="is-it-important-to-train-the-none-intent"></a>Je důležité ke cvičení záměrné žádné?

Ano, je vhodné školení vaše **žádné** záměrné s další utterances při přidávání dalších záměry další popisky. Dobrý poměr je 1 nebo 2 popisky, které jsou přidány do **žádné** pro každých 10 popisky přidány do záměrem. Tento poměr zvyšuje discriminative sílu LEOŠ.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak můžete opravit chyby v pravopisu v utterances?

Najdete v článku [API V7 kontrola pravopisu Bing](luis-tutorial-bing-spellcheck.md) kurzu. LEOŠ vynucuje omezení, která API V7 kontrola pravopisu Bing. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak upravit mé aplikace LEOŠ prostřednictvím kódu programu?
Chcete-li upravit aplikaci LEOŠ prostřednictvím kódu programu, použijte [vytváření rozhraní API](https://aka.ms/luis-authoring-apis). V tématu [volání LEOŠ vytváření rozhraní API](./luis-quickstart-node-add-utterance.md) a [vytvořit aplikaci LEOŠ programově pomocí Node.js](./luis-tutorial-node-import-utterances-csv.md) příklady, jak volat rozhraní API pro vytváření obsahu. Rozhraní API pro vytváření obsahu vyžaduje, abyste používali [vytváření klíč](luis-concept-keys.md#authoring-key) místo klíčem koncový bod. Programové vytváření umožňuje až 1 000 000 volání za měsíc a pět transakcí za sekundu. Další informace o klíčích pomocí LEOŠ najdete v tématu [Správa klíčů](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Kde je odpovídající funkce vzor, která regulární výraz k dispozici?
Předchozí **vzorová funkce** je aktuálně nepoužívané, nahrazuje  **[vzory](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak použít entitu vysunout správná data? 
V tématu [entity](luis-concept-entity-types.md) a [extrakce dat](luis-concept-data-extraction.md).

## <a name="luis-endpoint"></a>Koncový bod LEOŠ

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč LEOŠ prostory chcete do dotazu přidat kolem nebo uprostřed slova?
LEOŠ [tokenizes](luis-glossary.md#token) na základě utterance [jazykovou verzi](luis-supported-languages.md#tokenization). Původní hodnotu i tokenizovaná hodnoty jsou k dispozici pro [extrakce dat](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak vytvořit a přiřadit LEOŠ klíč koncového bodu?
[Vytvořte klíč koncového bodu](luis-how-to-azure-subscription.md#create-luis-endpoint-key) v Azure pro vaše [služby](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) úroveň. [Přiřazení klíče](Manage-keys.md#assign-endpoint-key) na **[publikovat](publishapp.md)** stránky. Neexistuje žádný odpovídající rozhraní API pro tuto akci. Pak musíte změnit požadavek HTTP na koncový bod pro [používání nového klíče koncový bod](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Interpretace LEOŠ skóre 
Systém musí používat nejvyšší vyhodnocování záměr bez ohledu na jeho hodnotu. Například skóre níže 0,5 (méně než 50 %) nutně znamenat, že LEOŠ má nízkou spolehlivosti. Poskytuje další data školení může přispět k vyšší skóre nejpravděpodobnější záměr.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Proč nevidím moje koncový bod přístupů do mojí aplikace řídicího panelu?
Koncový bod celkový počet přístupů do řídicího panelu vaší aplikace jsou pravidelně aktualizovány, ale metriku související s svůj klíč předplatného LEOŠ na portálu Azure jsou aktualizovány častěji. 

Pokud se nezobrazí aktualizované koncový bod přístupů do řídicího panelu, přihlaste se k portálu Azure a najít prostředek přidružené svůj klíč předplatného LEOŠ a otevřete **metriky** vyberte **celkový počet volání** metriku. Pokud klíč předplatného se používá pro více než jednu aplikaci LEOŠ, údaj na portálu Azure se zobrazí souhrnný počet volání z všechny LEOŠ aplikace, které ho používají.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Aplikace My LEOŠ pracoval včera, ale ještě dnes zobrazuje chyby 403. Aplikace k nezměnil Jakým způsobem ji lze upravit? 
Následující [pokyny](#how-do-i-create-and-assign-a-luis-endpoint-key) v další – nejčastější dotazy k vytvoření klíče LEOŠ koncový bod a přiřaďte ho do aplikace. Pak musíte změnit požadavek HTTP na koncový bod pro [používání nového klíče koncový bod](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpečit Moje LEOŠ koncový bod? 
V tématu [zabezpečení koncového bodu](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Práce v rámci LEOŠ limitů

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaký je maximální počet tříd Intent a entitami, které může podporovat LEOŠ aplikace?
Najdete v článku [hranice](luis-boundaries.md) odkaz.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chcete vytvořit aplikaci LEOŠ s více než maximální počet tříd Intent. Co bych měl/a dělat?

V tématu [osvědčené postupy pro záměry](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chcete vytvořit aplikaci LEOŠ s více než maximální počet entit. Co bych měl/a dělat?

V tématu [osvědčené postupy pro entity](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jaká jsou omezení na počtu a velikosti frázi uvádí?
Pro maximální délku [frázi seznamu](./luis-concept-feature.md), najdete v článku [hranice](luis-boundaries.md) odkaz.

### <a name="what-are-the-limits-on-example-utterances"></a>Jaká jsou omezení na příkladu utterances?
Najdete v článku [hranice](luis-boundaries.md) odkaz.

## <a name="testing-and-training"></a>Testování a školení

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Vidím chybami v dávce testování podokně u některých modelů v mé aplikace. Jak můžete tento problém vyřešit?

Chyby znamenat, že některé nesoulad mezi štítky a předpovědi z vaší modelů. Chcete-li potíže vyřešit, proveďte jednu z následujících úloh:
* LEOŠ zlepšit diskriminace mezi záměry, přidáte další popisky.
* LEOŠ další rychleji, přidáte seznam frází funkce, které zavést termínů specifické pro doménu.

Najdete v článku [testování Batch](luis-tutorial-batch-testing.md) kurzu.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Když je aplikace exportovat pak importovat do nové aplikace (s novým ID aplikace), se liší skóre LEOŠ předpovědi. Proč to stává? 

V tématu [předpovědi rozdíly mezi kopie stejné aplikaci](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Publikování aplikací

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co je ID klienta v okně "Přidání klíče do vaší aplikace"?
V Azure představuje klienta, klient nebo organizace, který je spojen s služby. Najít na portálu Azure v vaše ID klienta **ID adresáře** pole výběrem **Azure Active Directory** > **spravovat**  >  **Vlastnosti**.

![ID klienta v portálu Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

### <a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Proč jsou existuje další předplatné klíče v mojí aplikaci publikovat stránku než jsou přiděleny do aplikace? 
Každá aplikace LEOŠ má klíč vytváření obsahu nebo spuštění. LEOŠ předplatné klíče vytvořené během časového rámce GA se zobrazí na stránku publikovat bez ohledu na to, pokud jste přidali do aplikace. K tomu bylo potřeba usnadnění GA migrace. Všechny nové předplatné klíče LEOŠ se nezobrazí na stránce publikovat. 

## <a name="app-management"></a>Správa aplikací

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak převést vlastnictví LEOŠ aplikace?
Přenos LEOŠ aplikace do jiného předplatného Azure, aplikaci LEOŠ export a import pomocí nového účtu. ID aplikace LEOŠ v aplikaci klienta, který volá ho aktualizujte. Nové aplikace může vrátit mírně odlišné LEOŠ skóre z původní aplikace. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak lze stáhnout protokolu utterances uživatele?
Ve výchozím nastavení zaznamená aplikace LEOŠ utterances od uživatelů. Chcete-li stáhnout protokolu utterances, které uživatelé odeslat do aplikace LEOŠ, přejděte na **Moje aplikace**a klikněte na se třemi tečkami (***...*** ) v seznamu pro vaši aplikaci. Pak klikněte na tlačítko **exportovat protokoly koncový bod**. V protokolu je naformátován jako soubor s oddělovači (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak lze vypnout protokolování utterances?
Protokolování utterances uživatele můžete vypnout pomocí nastavení `log=false` adresu URL koncového bodu, která klientské aplikace používá k dotazování LEOŠ. Vypnutí protokolování ale zakáže aplikaci LEOŠ možnost navrhnout utterances nebo zvýšit výkon, která je založená na dotazech uživatele. Pokud nastavíte `log=false` kvůli riziko z hlediska ochrany osobních údajů, nelze z LEOŠ stahovat záznam utterances tyto uživatele, nebo použít tyto utterances ke zlepšení aplikace.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Proč nechcete všechna Moje utterances koncový bod protokolují?
Pokud používáte protokol pro analýzu předpovědi, není zachytit utterances testu v protokolu.

## <a name="data-management"></a>Správa dat

### <a name="can-i-delete-data-from-luis"></a>Z LEOŠ, odstranit data? 

* Vždy můžete odstranit utterances příklad používá pro trénování LEOŠ. Pokud odstraníte utterance příklad z vaší aplikace LEOŠ, je odebrán z LEOŠ webové služby a není k dispozici pro export.
* Utterances můžete odstranit ze seznamu utterances uživatele, které LEOŠ navrhuje v **zkontrolujte koncový bod utterances** stránky. Z tohoto seznamu odstraníte utterances sdělovat navržena, ale nedojde k jejich odstranění z protokolů.
* Pokud účet odstraníte, se odstraní všechny aplikace, spolu s jejich příklad utterances a protokoly. Data se uchovávají na serverech 60 dnů, než se trvale odstraní.

## <a name="language-and-translation-support"></a>Podpora jazyka a překlad 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mám aplikace v jednom jazyce a chcete vytvořit paralelní aplikace v jiném jazyce. Co je nejjednodušší způsob?
1. Exportujte aplikace.
2. Převede s popiskem utterances v souboru JSON exportovaný aplikaci cílový jazyk.
3. Možná budete muset změnit názvy tříd Intent a entity nebo je nechat, jak jsou.
4. Nakonec importujte aplikaci, aby měla LEOŠ aplikace v jazyce cíl.

## <a name="app-notification"></a>Oznámení aplikace

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Proč získat e-mailu, že jsem téměř mimo kvótu?
Klíč pro tvorbu nebo spuštění je povolené jenom 1000 koncový bod dotazuje za měsíc. Vytvořte klíč předplatného LEOŠ (volné nebo placené) a používat při provádění dotazů koncový bod. Pokud bude z robotu nebo jinou aplikaci klienta se koncový bod dotazy, potřebujete změnit klíč koncového bodu LEOŠ existuje. 

## <a name="integrating-luis"></a>Integrace LEOŠ

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Kde je aplikace my LEOŠ vytvořené během procesu službě Azure web app robota předplatné?
Pokud vyberete šablonu LEOŠ a vyberte **vyberte** tlačítko v podokně šablony, v levém podokně se změní na zahrnují typ šablony a zobrazí dotaz, ve které oblasti pro vytvoření šablony LEOŠ. Proces robota webové aplikace, když není vytvořit odběr LEOŠ.

![Oblast robota LEOŠ šablony webové aplikace](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké LEOŠ oblasti podporují robota Framework řeči dočištění?
[Rozpoznávání řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) je podporována pouze pro aplikace LEOŠ v centrální instanci (USA). 

## <a name="luis-service"></a>LEOŠ služby 

### <a name="is-luis-available-on-premise-or-in-private-cloud"></a>Je LEOŠ k dispozici místní nebo v privátním cloudu?
Ne. 

## <a name="changes-to-the-docs"></a>Změny v dokumentaci

### <a name="where-did-the-tutorials-go"></a>Kde? kurzů k 
Články, které byly dříve v části kurzu jsou nyní v části postupy dokumentů. 

|Kurz|
|--|
|Integrovat LEOŠ robota s [C#](luis-csharp-tutorial-build-bot-framework-sample.md) a [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Přidejte Application Insights do robota s [C#](luis-tutorial-bot-csharp-appinsights.md) a [Node.js](luis-tutorial-function-appinsights.md)|
|Vytvořit aplikaci LEOŠ programově pomocí [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Použití [složené entity](luis-tutorial-composite-entity.md) extrahovat seskupené data|
|Přidat [seznamu entity](luis-tutorial-list-entity.md) pro vyšší entity zjišťování pomocí Node.js|
|Zvyšte přesnost předpovědi s [frázi seznamu](luis-tutorial-interchangeable-phrase-list.md), [vzory](luis-tutorial-pattern.md), a [batch testování](luis-tutorial-batch-testing.md)|
|[Opravte pravopis](luis-tutorial-batch-testing.md) s v7 API kontrola pravopisu Bing

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na konferenci 2018 sestavení dozvěděli znalosti jazyka funkce nebo ukázku, ale nemohu si vzpomenout, co byla volána? 

Následující funkce byly vydané na konferenci 2018 sestavení:

|Název|Obsah|
|--|--|
|Vylepšení|[Regulární výraz](luis-concept-data-extraction.md##regular-expression-entity-data) entity a [klíč frázi](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entity
|Vzory|Vzory [koncept](luis-concept-patterns.md), [kurzu](luis-tutorial-pattern.md), [postupy:](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entity koncept včetně [explicitní seznam](luis-concept-patterns.md#explicit-lists) pro výjimky<br>[Role](luis-concept-roles.md) koncept|
|Integrace|[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrace [postojích analýzy](publishapp.md#enable-sentiment-analysis)<br>[Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech) integrace [řeči dočištění](publishapp.md#enable-speech-priming) ve spojení s [řeči SDK](https://aka.ms/SpeechSDK)|
|Nástroj pro odesílání|Součást [BotBuilder nástroje](https://github.com/Microsoft/botbuilder-tools), odesílání příkazového řádku [nástroj](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) kombinovat více LEOŠ a QnA Maker aplikací do jednoho LEOŠ aplikace pro lepší záměrné rozpoznávání v robotu

Další vytváření [rozhraní API trasy](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) byly zahrnuty. 

Videa: 
* [Azure pátek v sestavení 2018: Kognitivní služeb - Language (LEOŠ)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Sestavení 2018 AI zobrazit - co je nového službou znalosti jazyka](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Sestavení 2018 relace - intelligence, funkce a NLU robota osvědčené postupy](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Sestavení 2018 - LEOŠ aktualizace](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekty: 
* [Contoso Cafe robota](https://github.com/botbuilderbuild2018/build2018demo) demo - zdrojového kódu na Githubu

## <a name="next-steps"></a>Další postup

Další informace o LEOŠ, najdete v následujících zdrojích informací:
* [Otázky přetečení zásobníku označené LEOŠ](https://stackoverflow.com/questions/tagged/luis)
* [Jazyk MSDN pochopení inteligentního služeb fórum (LEOŠ)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
