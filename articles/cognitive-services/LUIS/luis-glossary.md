---
title: Glosář – LUIS
description: Glosář vysvětluje výrazy, se kterými se můžete setkat při práci se službou LUIS API.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: cc7dac3767ca5c9e2429f4691a458f88ad1af707
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151973"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Jazyk porozumění běžným slovníkům a konceptům
Glosář Language Understanding (LUIS) vysvětluje výrazy, se kterými se můžete setkat při práci se službou LUIS.

## <a name="active-version"></a>Aktivní verze

Aktivní verze je [verze](luis-how-to-manage-versions.md) vaší aplikace, která se aktualizuje, když provedete změny modelu pomocí portálu Luis. Pokud chcete provést změny verze, která není aktivní, musíte na portálu LUIS nejdřív nastavit tuto verzi jako aktivní.

## <a name="active-learning"></a>Aktivní učení

Aktivní učení je technika strojového učení, ve kterém se k identifikaci informativních nových příkladů pro jmenovku používá strojový model. Aktivní učení v LUIS odkazuje na přidání projevy z provozu koncového bodu, jehož aktuální předpovědi nejsou jasné pro zlepšení modelu. Kliknutím na "zkontrolovat koncový bod projevy" Zobrazte projevy pro popisek.

Viz také:
* [Koncepční informace](luis-concept-review-endpoint-utterances.md)
* [Kurz revize projevy koncového bodu](luis-tutorial-review-endpoint-utterances.md)
* Postup zlepšení aplikace LUIS [kontrolou Endpoint projevy](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Aplikace (aplikace)

V LUIS je vaše aplikace nebo aplikace kolekce modelů strojového učení, které jsou postavené na stejné datové sadě, které spolupracují na prediktivních záměrech a entitách pro konkrétní scénář. Každá aplikace má samostatný koncový bod předpovědi.

Pokud vytváříte roboty na HR, můžete mít sadu záměrů, jako je například "naplánovat dobu trvání", "dotazování na výhody" a "aktualizovat osobní údaje" a entity pro každý z těchto záměrů, které můžete seskupit do jedné aplikace.

## <a name="authoring"></a>Vytváření obsahu

Vytváření, Správa a nasazování aplikace LUIS, a to buď pomocí portálu LUIS nebo rozhraní API pro vytváření obsahu, je možné vytvářet.

### <a name="authoring-key"></a>Vytváření klíče

[Klíč pro vytváření obsahu](luis-how-to-azure-subscription.md) slouží k vytváření aplikací. Nepoužívá se pro dotazy na koncové body na úrovni výroby. Další informace najdete v tématu [omezení klíčů](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Vytváření prostředku

Prostředek pro [vytváření obsahu](luis-how-to-azure-subscription.md#azure-resources-for-luis) Luis je spravovatelná položka, která je dostupná prostřednictvím Azure. Prostředek je váš přístup k přidruženým schopnostem vytváření obsahu, školení a publikování služby Azure. Prostředek obsahuje informace o ověřování, autorizaci a zabezpečení, které potřebujete pro přístup k přidružené službě Azure.

Prostředek pro vytváření obsahu obsahuje typ Azure `LUIS-Authoring` .

## <a name="batch-test"></a>Dávkový test

Dávkové testování je schopnost ověřit aktuální modely aplikace v LUIS s konzistentní a známou sadou testů uživatele projevy. Dávkový test je definován v souboru ve [formátu JSON](luis-concept-batch-test.md#batch-file-format).

Viz také:
* [Koncepty](luis-concept-batch-test.md)
* [Postup](luis-how-to-batch-test.md) spuštění dávkového testu
* [Kurz](luis-tutorial-batch-testing.md) – vytvoření a spuštění dávkového testu

### <a name="f-measure"></a>Měření F

V dávkovém testování je míra přesnosti testu.

### <a name="false-negative-fn"></a>Falešně negativní (FN)

V dávkovém testování představuje datový bod projevy, ve kterém vaše aplikace nesprávně předpovídá absenci cílového záměru nebo entity.

### <a name="false-positive-fp"></a>Falešně pozitivní (FP)

V dávkovém testování představuje datový bod projevy, ve kterém vaše aplikace nesprávně předpovězena existenci cílového záměru nebo entity.

### <a name="precision"></a>Přesnost
V dávkovém testování je přesnost (označovaná také jako kladná prediktivní hodnota) zlomkem relevantního projevy mezi načteným projevy.

Příkladem pro dávkový test zvířat je počet ovcí, které byly předpovězeny dělený celkovým počtem zvířat (ovce a non-ovce).

### <a name="recall"></a>Recall

Při dávkovém testování je možné LUIS zobecnit (označované také jako citlivost).

Příkladem pro dávkový test zvířat je počet ovcí, které byly předpovězeny dělený celkovým počtem dostupných ovcí.

### <a name="true-negative-tn"></a>Pravý záporný (TN)

Hodnota true negativ je, pokud vaše aplikace správně nevyhovuje. Při dávkovém testování dojde k kladné záporné hodnotě, pokud vaše aplikace odhadne záměr nebo entitu pro příklad, který nebyl označený záměrem nebo entitou.

### <a name="true-positive-tp"></a>Pravdivý pozitivní (TP)

Hodnota true pozitivní (TP) pravdivé kladné hodnoty je, když vaše aplikace správně odhadne shodu. Při dávkovém testování dojde k pravdivé kladné události, pokud vaše aplikace předpovídá záměr nebo entitu pro příklad, který byl označen tímto záměrem nebo entitou.

## <a name="classifier"></a>Klasifikátor

Klasifikátor je model strojového učení, který předpovídá, do jaké kategorie nebo třídy se vstup vejde.

[Záměrem](#intent) je příklad třídění.

## <a name="collaborator"></a>Spolupracovník

Spolupracovníci je koncepčně stejné jako [Přispěvatel](#contributor). Spolupracovníka má udělený přístup, když vlastník přidá e-mailovou adresu spolupracovníka do aplikace, která není řízená řízením přístupu na základě role Azure (Azure RBAC). Pokud stále používáte spolupracovníky, měli byste migrovat svůj účet LUIS a pomocí prostředků vytváření LUIS spravovat přispěvatele s využitím Azure RBAC.

## <a name="contributor"></a>Přispěvatel

Přispěvatel není [vlastníkem](#owner) aplikace, ale má stejná oprávnění k přidávání, úpravám a odstraňování záměrů, entit a projevy. Přispěvatel poskytuje řízení přístupu na základě role Azure (Azure RBAC) do aplikace LUIS.

Viz také:
* [Postup](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) přidání přispěvatelů

## <a name="descriptor"></a>Popisovač

Popisovač je termín, který se dřív používal pro [funkci](#features)strojového učení.

## <a name="domain"></a>Doména

V kontextu LUIS je doména oblastí znalostí. Vaše doména je specifická pro váš scénář. Různé domény používají konkrétní jazyk a terminologii, které mají význam v souvislosti s doménou. Pokud například vytváříte aplikaci pro přehrávání hudby, budou mít vaše aplikace určité termíny a jazyky specifické pro hudbu – slova jako "skladba, sledování, album, texty, b-strana, Interpret". Příklady domén najdete v tématu [předem připravené domény](#prebuilt-domain).

## <a name="endpoint"></a>Koncový bod

### <a name="authoring-endpoint"></a>Vytváření koncového bodu

Adresa URL koncového bodu pro vytváření LUIS je místo, kde vytváříte, naučíte a publikujete svoji aplikaci. Adresa URL koncového bodu obsahuje oblast nebo vlastní subdoménu publikované aplikace i ID aplikace.

Další informace o vytváření aplikací prostřednictvím kódu programu z [referenčních](developer-reference-resource.md#rest-endpoints) informací pro vývojáře

### <a name="prediction-endpoint"></a>Koncový bod předpovědi

Adresa URL koncového bodu předpovědi LUIS je místo, kam odešlete dotazy LUIS, až bude [aplikace Luis](#application-app) vytvořená a publikovaná. Adresa URL koncového bodu obsahuje oblast nebo vlastní subdoménu publikované aplikace i ID aplikace. Koncový bod najdete na stránce **[prostředky Azure](luis-how-to-azure-subscription.md)** v aplikaci, nebo můžete získat adresu URL koncového bodu z rozhraní API [získat informace o aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

Váš přístup k koncovému bodu předpovědi je autorizován pomocí klíče předpovědi LUIS.

## <a name="entity"></a>Entita

[Entity](luis-concept-entity-types.md) jsou slova v projevy, která popisují informace používané ke splnění nebo identifikaci záměru. Pokud je vaše entita složitá a chcete, aby váš model identifikoval konkrétní části, můžete model rozdělit do subentit. Můžete například chtít model předpovědět adresu, ale také subentity ulic, City, State a PSČ. Entity je také možné použít jako funkce modelů. Vaše odpověď z aplikace LUIS bude zahrnovat předpovězené záměry i všechny entity.

### <a name="entity-extractor"></a>Extraktor entit

Extraktor entit se někdy označuje jenom jako extraktor je typ strojového modelu, který LUIS používá k předpovědi entit.

### <a name="entity-schema"></a>Schéma entity

Schéma entit je struktura, kterou definujete pro entity, které se strojově seznámily, s podentitami. Koncový bod předpovědi vrátí všechny extrahované entity a subentity definované ve schématu.

### <a name="entitys-subentity"></a>Subentity entity

Podentita je podřízenou entitou entity strojového učení.

### <a name="non-machine-learning-entity"></a>Entita bez strojového učení

Entita, která používá text odpovídající extrakci dat:
* Entita seznamu
* Entiay regulárního výrazu

### <a name="list-entity"></a>Entita seznamu

[Entita seznamu](reference-entity-list.md) představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. Seznam entit je přesná shoda, na rozdíl od strojově zjištěných entit.

Entita bude předpovězena, pokud je v seznamu obsaženo slovo v entitě seznamu. Pokud například máte entitu seznamu s názvem "size" a v seznamu máte slova "malá, střední, Velká", pak se entita velikosti bude vypovídat pro všechny projevy, kde se používají slova "malá", "střední" nebo "Velká", bez ohledu na kontext.

### <a name="regular-expression"></a>Regulární výraz

[Entita regulárního výrazu](reference-entity-regular-expression.md) představuje regulární výraz. Entity regulárních výrazů jsou přesné shody, na rozdíl od strojově vyučených entit.
### <a name="prebuilt-entity"></a>Předem vytvořená entita

Zobrazit položku předem sestaveného modelu pro [předem vytvořenou entitu](#prebuilt-entity)

## <a name="features"></a>Funkce

Ve strojovém učení je funkcí charakteristikou, která pomáhá modelu pochopit konkrétní koncept. Je to pomocný parametr, který může LUIS použít, ale ne pevné pravidlo.

Tento termín se také označuje jako **[funkce strojového učení](luis-concept-feature.md)**.

Tyto pomocné údaje se používají ve spojení s popisky a Naučte se, jak předpovídat nová data. LUIS podporuje seznamy frází a jako funkce používají jiné modely.

### <a name="required-feature"></a>Požadovaná funkce

Požadovaná funkce je způsob, jak omezit výstup modelu LUIS. Je-li funkce pro entitu označena jako povinná, musí být tato funkce uvedena v příkladu, aby bylo možné entitu odhadnout, bez ohledu na to, co stroj dozvěděl model předpověď.

Vezměte v úvahu příklad, ve kterém máte předdefinovanou funkci, kterou jste označili jako povinnou pro entitu množství pro robota řazení nabídky. Když se robot uvidí `I want a bajillion large pizzas?` , bajillion se neodhadne jako množství bez ohledu na kontext, ve kterém se zobrazí. Bajillion není platné číslo a nebude se předpokládat číslem předem vytvořenou entitou.

## <a name="intent"></a>Záměr

[Záměr](luis-concept-intent.md) představuje úkol nebo akci, kterou chce uživatel provést. Jedná se o účel nebo cíl vyjádřený ve vstupu uživatele, jako je například rezervace letu nebo platba faktury. V LUIS je utterance jako celek klasifikován jako záměr, ale části utterance jsou extrahovány jako entity.

## <a name="labeling-examples"></a>Příklady popisků

Označení nebo označení je proces přidružení kladného nebo záporného příkladu k modelu.

### <a name="labeling-for-intents"></a>Označování pro záměry
V LUIS se záměry v rámci aplikace vzájemně vylučují. To znamená, že když přidáte utterance k záměru, je považován za _kladný_ příklad tohoto záměru a _negativní_ příklad pro všechny ostatní záměry. Záporné příklady by neměly být zaměňovány s záměrem None, který představuje projevy, které jsou mimo rozsah aplikace.

### <a name="labeling-for-entities"></a>Označování entit
V LUIS můžete [Označit](label-entity-example-utterance.md) slovo nebo frázi v příkladech záměru utterance s entitou jako _pozitivním_ příkladem. Označení ukazuje záměr, který by měl předpovědět pro tento utterance. Označení projevy slouží ke vzdělávání záměru.

## <a name="luis-app"></a>Aplikace LUIS

Viz definice [aplikace (aplikace)](#application-app).

## <a name="model"></a>Model

Model A (strojové učení) je funkce, která provádí předpověď na vstupní data. V LUIS odkazujeme na třídění záměrů a extraktory entit obecně jako na "modely" a odkazujeme na kolekci modelů, které jsou výukové, publikované a společně dotazované jako "aplikace".

## <a name="normalized-value"></a>Normalizovaná hodnota

Do entit [seznamu](#list-entity) přidáte hodnoty. Každá z těchto hodnot může mít seznam jednoho nebo více synonym. V odpovědi se vrátí jenom normalizovaná hodnota.

## <a name="overfitting"></a>Přeurčení (overfitting)

K překrytí dojde, když je model v konkrétních příkladech přizpůsobený a není schopný zobecnit správně.

## <a name="owner"></a>Vlastník

Každá aplikace má jednoho vlastníka, který je osoba, která aplikaci vytvořila. Vlastník spravuje oprávnění k aplikaci v Azure Portal.

## <a name="phrase-list"></a>Seznam frází

[Seznam frází](luis-concept-feature.md) je konkrétní typ funkce strojového učení, která zahrnuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se nacházet podobně (například názvy měst nebo produktů).

## <a name="prebuilt-model"></a>Předem sestavený model

[Předem sestavený model](luis-concept-prebuilt-model.md) je záměrem, entitou nebo kolekcí obou spolu s příklady s popisky. Tyto běžné předem připravené modely je možné do vaší aplikace přidat, aby se snížila činnost vývoje modelu požadovaná pro vaši aplikaci.

### <a name="prebuilt-domain"></a>Předem sestavená doména

Předem vytvořená doména je aplikace LUIS konfigurovaná pro konkrétní doménu, jako je například domácí automatizace (HomeAutomation) nebo rezervace restaurace (RestaurantReservation). Záměry, projevy a entity jsou nakonfigurovány pro tuto doménu.

### <a name="prebuilt-entity"></a>Předem vytvořená entita

Předem vytvořená entita je LUIS entit, která poskytuje běžné typy informací, jako je číslo, adresa URL a e-mail. Ty se vytvářejí na základě veřejných dat. Předem vytvořenou entitu si můžete přidat jako samostatnou entitu nebo jako funkci entity.

### <a name="prebuilt-intent"></a>Předem sestavený záměr

Předem sestavený záměr je záměrem, který LUIS poskytuje pro běžné typy informací a přináší vlastní popisky projevy.

## <a name="prediction"></a>Předpověď

Předpovědi je žádost REST ke službě předpovědi Azure LUIS, která přebírá nové údaje (uživatel utterance), a pro tato data používá vyškolenou a publikovanou aplikaci k určení, jakého záměru a entit se najde.

### <a name="prediction-key"></a>Klíč předpovědi

[Klíč předpovědi](luis-how-to-azure-subscription.md) (dříve označovaný jako klíč předplatného) je klíč přidružený ke službě Luis, kterou jste vytvořili v Azure, která slouží k autorizaci využití koncového bodu předpovědi.

Tento klíč není klíč pro vytváření. Pokud máte klíč koncového bodu předpovědi, měl by se místo vytváření klíče použít pro všechny požadavky na koncový bod. Aktuální klíč předpovědi si můžete prohlédnout v adrese URL koncového bodu na dolní části stránky prostředků Azure na webu LUIS. Je to hodnota dvojice název/hodnota klíče předplatného.

### <a name="prediction-resource"></a>Prostředek předpovědi

Prostředek předpovědi LUIS je spravovatelná položka, která je dostupná prostřednictvím Azure. Prostředek je váš přístup k související předpovědi služby Azure. Prostředek zahrnuje předpovědi.

Předpověď prostředků má "druh" Azure `LUIS` .

### <a name="prediction-score"></a>Skóre předpovědi

[Skóre](luis-concept-prediction-score.md) je číslo od 0 do 1, které představuje způsob, jakým systém zaznamená, že konkrétní vstupní utterance odpovídá konkrétnímu záměru. Skóre Blíže k hodnotě 1 znamená, že systém má velmi jistotu o jeho výstupu a skóre Blíže k hodnotě 0 znamená, že se v systému nachází jistotu, že se vstup neshoduje s konkrétním výstupem. Skóre ve střední polovině znamená, že systém nezpůsobí, jak učinit rozhodnutí.

Můžete například použít model, který určí, jestli některý z textů zákazníka zahrnuje pořadí potravin. Může dát skóre 1 pro "Jak můžu objednat jednu kávy" (systém je velmi jistý, že se jedná o objednávku) a skóre 0 pro "tým vyhrál poslední noc" (systém je velmi jistý, že se nejedná o objednávku). A může mít skóre 0,5 pro "Pojďme mít nějakou čaj" (nezáleží, jestli se jedná o objednávku nebo ne).

## <a name="programmatic-key"></a>Programový klíč

Přejmenováno na [klíč pro vytváření](#authoring-key).

## <a name="publish"></a>Publikovat

[Publikování](luis-how-to-publish-app.md) znamená, že Luis aktivní verze je dostupná na pracovním nebo produkčním [koncovém bodu](#endpoint).

## <a name="quota"></a>Kvóta

LUIS kvóta je omezením úrovně předplatného Azure. Kvóta LUIS může být omezená oběma požadavky za sekundu (stav HTTP 429) a celkovými požadavky v měsíci (stav HTTP 403).

## <a name="schema"></a>Schéma

Vaše schéma zahrnuje vaše záměry a entity spolu s podentitami. U schématu je zpočátku naplánováno iterace v průběhu času. Schéma neobsahuje nastavení aplikace, funkce nebo příklad projevy.

## <a name="sentiment-analysis"></a>Analýza mínění
Analýza mínění poskytuje kladné nebo záporné hodnoty projevy, které poskytuje [Analýza textu](../text-analytics/overview.md).

## <a name="speech-priming"></a>Neprojevení řeči

Dokládání řeči vylepšuje rozpoznávání mluvených slov a frází, které se ve vašem scénáři běžně používají se [službami Speech](../speech-service/overview.md). U aplikací podporujících rozpoznávání řeči se pro tuto konkrétní aplikaci využije všechny příklady LUIS s označením pro zlepšení přesnosti rozpoznávání řeči tím, že se vytvoří přizpůsobený model řeči. Například ve hře šachy chcete mít jistotu, že když uživatel uvede "Přesunový krok", není interpretován jako "Přesun noční". Aplikace LUIS by měla obsahovat příklady, ve kterých je "jezd" označen jako entita.

## <a name="starter-key"></a>Počáteční klíč

Bezplatný klíč, který se použije při prvním spuštění pomocí LUIS.

## <a name="synonyms"></a>Synonyma

V LUIS [seznam entit](reference-entity-list.md)můžete vytvořit normalizovanou hodnotu, která může obsahovat seznam synonym. Například pokud vytvoříte entitu velikosti, která má normalizované hodnoty malá, střední, Velká a velmi velká. Můžete vytvořit synonyma pro každou hodnotu, jako je tato:

|Hodnota Nomalized| Synonyma|
|--|--|
|Malý| trochu jedna, 8 unce|
|Střední| Regular, 12 unce|
|Velký| velký, 16 unce|
|Xtra velký| největší z nich, 24 unce|

Model Vrátí normalizovanou hodnotu pro entitu, pokud se ve vstupu objeví kterákoli z synonym.

## <a name="test"></a>Test

[Testování](luis-concept-test.md) aplikace Luis znamená zobrazení modelu předpovědi.

## <a name="timezone-offset"></a>Posun časového pásma

Koncový bod zahrnuje [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Toto je číslo v minutách, po které chcete přidat nebo odebrat z předdefinované entity datetimeV2. Například pokud je utterance "jaký čas je nyní?", vrácený datetimeV2 je aktuální čas pro požadavek klienta. Pokud žádost klienta pochází z robota nebo jiné aplikace, která není stejná jako uživatel vaší robota, měli byste předávat posun od robota a uživatele.

Podívejte se [na téma Změna časového pásma předem sestavené entity datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
[Token](luis-language-support.md#tokenization) je nejmenší jednotka textu, kterou může Luis rozeznat. To se mírně liší v různých jazycích.

Pro **angličtinu**je token souvislým rozsahem písmen a číslic (bez mezer nebo interpunkčních znamének). Prostor není token.

|Fráze|Počet tokenů|Vysvětlení|
|--|--|--|
|`Dog`|1|Jedno slovo bez interpunkce nebo mezer.|
|`RMT33W`|1|Číslo lokátoru záznamu. Může obsahovat čísla a písmena, ale nemá žádnou interpunkci.|
|`425-555-5555`|5|Telefonní číslo. Každé interpunkční znaménko je jeden token, takže  `425-555-5555` by to představovalo 5 tokenů:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Trénování

[Školení](luis-how-to-train.md) je proces výuky Luis o jakýchkoli změnách v aktivní verzi od posledního školení.

### <a name="training-data"></a>Data pro trénink

Školicí data jsou sada informací, které jsou potřeba k výuce modelu. To zahrnuje schéma, označené projevy, funkce a nastavení aplikace.

### <a name="training-errors"></a>Školení – chyby

Chyby školení se předpovědi na školicích datech, která neodpovídají jejich popiskům.

## <a name="utterance"></a>Promluva

[Utterance](luis-concept-utterance.md) je uživatelský vstup, který je krátkým textovým zástupcem věty v konverzaci. Jedná se o frázi přirozeného jazyka, jako jsou lístky Book 2 do Seattlu Next úterý. Příkladem projevy jsou přidané hodnoty pro výuku modelu a předpověď modelu na nové utterance za běhu.

## <a name="version"></a>Verze

[Verze](luis-how-to-manage-versions.md) Luis je konkrétní instance aplikace Luis přidružená k ID aplikace Luis a publikovanému koncovému bodu. Každá aplikace LUIS má alespoň jednu verzi.
