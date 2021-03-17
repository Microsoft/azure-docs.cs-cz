---
title: Testování pro DevOps pro aplikace LUIS
description: Postup testování aplikace Language Understanding (LUIS) v prostředí DevOps
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788447"
---
# <a name="testing-for-luis-devops"></a>Testování pro LUIS DevOps

Technici softwaru, kteří vyvíjí aplikaci Language Understanding (LUIS), mohou uplatnit DevOps postupy kolem [správy zdrojového kódu](luis-concept-devops-sourcecontrol.md), [automatizované sestavení](luis-concept-devops-automation.md), [testování](luis-concept-devops-testing.md)a [správy](luis-concept-devops-automation.md#release-management) vydaných verzí pomocí následujících pokynů.

V případě metodologie vývoje agilního softwaru hraje testování v budování kvalitního softwaru integrální roli. Každá významná změna aplikace LUIS by měla být doprovázena testy navrženými k otestování nových funkcí, které vývojář sestaví do aplikace. Tyto testy jsou zkontrolovány do vašeho úložiště zdrojového kódu společně se `.lu` zdrojem vaší aplikace Luis. Implementace změny je dokončena, když aplikace splňuje testy.

Testy jsou důležitou součástí [pracovních postupů CI/CD](luis-concept-devops-automation.md). Když se změny aplikace v LUIS navrhují v žádosti o přijetí změn (PR) nebo po sloučení změn do hlavní větve, pracovní postupy CI by měly spustit testy, aby ověřili, že aktualizace nezpůsobily žádné regrese.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Jak provádět testování částí a dávkové testování

Existují dva různé druhy testování aplikace LUIS, které je třeba provést v pracovních postupech kontinuální integrace:

- **Testování částí** – relativně jednoduché testy, které ověřují klíčové funkce vaší aplikace v Luis. Test jednotek projde při vrácení očekávaného záměru a očekávaných entit pro daný test utterance. Všechny testy jednotek musí být úspěšné, aby se testovací běh úspěšně dokončil.  
Tento druh testování je podobný [interaktivnímu testování](./luis-concept-test.md) , které můžete provádět na [portálu Luis](https://www.luis.ai/).

- **Dávkové testy** – dávkové testování je ucelený test na vašem aktuálním škole modelu pro měření výkonu. Na rozdíl od jednotkových testů není dávkové testování úspěšné | testování. Očekává se, že při testování dávky není každý test vracet očekávaný záměr a očekávané entity. Místo toho vám Batch test pomůže zobrazit přesnost jednotlivých záměrů a entit ve vaší aplikaci a pomůže vám v průběhu času porovnat při vylepšování.  
Tento druh testování je stejný jako [dávkové testování](./luis-how-to-batch-test.md) , které můžete provádět interaktivně na portálu Luis.

Můžete využít testování částí od začátku projektu. Dávkové testování je ve skutečnosti jenom tehdy, když jste vytvořili schéma aplikace LUIS a pracujete na vylepšení jeho přesnosti.

Pro testy jednotek i dávkové testy se ujistěte, že projevy testů jsou udržovány odděleně od projevy školení. Pokud testujete stejná data, která jste provedli, získáte falešnou dojem, že vaše aplikace bude fungovat dobře, když je pouze převedená na testovací data. Aby bylo možné otestovat, jak dobře se generalizuje, testy musí být v modelu nepřesné.

### <a name="writing-tests"></a>Zápis testů

Při psaní sady testů pro každý test, který je třeba definovat:

* Utterance testu
* Očekávaný záměr
* Očekávané entity.

Pomocí [syntaxe dávkového souboru](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) Luis definujte skupinu testů v souboru ve formátu JSON. Například:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Některé testovací nástroje, například [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) také podporuje testovací soubory ve formátu LUDown.

#### <a name="designing-unit-tests"></a>Navrhování testů jednotek

Testy jednotek by měly být navržené k otestování základních funkcí aplikace LUIS. V každé iteraci nebo sprintu vývoje vaší aplikace byste měli napsat dostatečný počet testů, abyste ověřili, že funkce klíče, kterou implementujete v této iteraci, funguje správně.

V každém testu jednotek pro daný test utterance můžete:

* Test, zda je vrácen správný záměr
* Otestujte, jestli jsou vraceny klíčové entity – ty, které jsou pro vaše řešení klíčové –.
* Otestujte, že [skóre předpovědi](./luis-concept-prediction-score.md) pro záměr a entity překročí prahovou hodnotu, kterou definujete. Například se můžete rozhodnout, že budete předpokládat, že pokud skóre předpovědi záměru a pro klíčové entity překročí 0,75, bude to mít za to, že test prošel.

V testování částí je vhodné otestovat, zda klíčové entity byly vráceny v reakci předpovědi, ale ignorovat všechny falešně pozitivní výsledky. *Falešně pozitivní* jsou entity, které se nacházejí v odpovědi předpovědi, ale které nejsou definovány v očekávané výsledky testu. Díky ignorování falešně pozitivních hodnot znamená, že je méně náročné na vytváření testů jednotek a stále vám umožní soustředit se na testování, že data, která jsou klíč pro vaše řešení, se vracejí v reakci na předpovědi.

> [!TIP]
> [NLU. Nástroj DevOps](https://github.com/microsoft/NLU.DevOps) podporuje všechny potřeby testování Luis. `compare`Příkaz při použití v [režimu testování částí](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) vyhodnotí, že všechny testy jsou průchody a bude ignorovat falešně pozitivní výsledky pro entity, které nejsou označeny jako očekávané výsledky.

#### <a name="designing-batch-tests"></a>Navrhování dávkových testů

Sady dávkových testů by měly obsahovat velký počet testových případů, které jsou navrženy k testování napříč všemi záměry a všemi entitami v aplikaci LUIS. Informace o definování sady dávkových testů najdete [v tématu testování dávky na portálu Luis](./luis-how-to-batch-test.md) .

### <a name="running-tests"></a>Spouštění testů

Portál LUIS nabízí funkce, které vám pomůžou s interaktivním testováním:

* [**Interaktivní testování**](./luis-concept-test.md) vám umožňuje odeslat ukázkovou utterance a získat odpověď na LUISé záměry a entity. Úspěšnost testu ověříte vizuální kontrolou.

* [**Dávkové testování**](./luis-how-to-batch-test.md) používá jako vstup soubor dávkového testu k ověření, jestli vaše aktivní vyškolená verze měří přesnost předpovědi. Batch test vám pomůže zobrazit přesnost každého záměru a entity v aktivní verzi a zobrazovat výsledky pomocí grafu.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Spouštění testů v procesu automatizovaného sestavení

Funkce interaktivního testování na portálu LUIS jsou užitečné, ale pro DevOps automatizované testování prováděné v pracovním postupu CI/CD přináší určité požadavky:

* Testovací nástroje musí běžet v kroku pracovního postupu na serveru sestavení. To znamená, že nástroje musí být schopné spustit na příkazovém řádku.
* Testovací nástroje musí být schopné spustit skupinu testů na koncový bod a automaticky ověřit očekávané výsledky na základě skutečných výsledků.
* Pokud testy selžou, testovací nástroje musí vrátit stavový kód pro zastavení pracovního postupu a "selhání sestavení".

LUIS nenabízí nástroj příkazového řádku ani rozhraní API na nejvyšší úrovni, které nabízí tyto funkce. Doporučujeme, abyste používali [NLU. Nástroj DevOps](https://github.com/microsoft/NLU.DevOps) pro spuštění testů a ověření výsledků na příkazovém řádku a při automatizovaném testování v rámci pracovního postupu CI/CD.

Možnosti testování, které jsou k dispozici na portálu LUIS, nevyžadují publikovaný koncový bod a jsou součástí možností vytváření LUIS. Při implementaci testování v pracovním postupu automatizovaného sestavení je nutné publikovat verzi aplikace LUIS, která má být testována na koncový bod tak, aby testovací nástroje jako NLU. DevOps může jako součást testování odesílat požadavky na předpovědi.

> [!TIP]
> * Pokud implementujete vlastní testovací řešení a napíšete kód pro odeslání projevy testu do koncového bodu, pamatujte, že pokud používáte klíč pro vytváření LUIS, je povolená četnost transakcí omezená na 5TPS. Buď omezte rychlost odesílání, nebo místo toho použijte klíč předpovědi.
> * Při odesílání testovacích dotazů na koncový bod nezapomeňte použít `log=false` v řetězci dotazu žádosti o předpověď. Tím zajistíte, že se testovací projevy neprotokoluje LUIS a skončí v seznamu kontroly projevy koncového bodu, který prezentuje funkce LUIS [Active Learning](./luis-concept-review-endpoint-utterances.md) , a v důsledku toho se nechtěně přidají do školicích projevy vaší aplikace.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Spouštění testů jednotek na příkazovém řádku a v pracovních postupech CI/CD

Můžete použít [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) balíček pro spuštění testů na příkazovém řádku:

* Použijte NLU. DevOps [test Command](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) pro odeslání testů z testovacího souboru do koncového bodu a zachycení skutečných výsledků předpovědi v souboru.
* Použijte NLU. DevOps [Compare příkaz](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) pro porovnání skutečných výsledků s očekávanými výsledky definovanými ve vstupním testovacím souboru. `compare`Příkaz vygeneruje výstup testu nunit a při použití v [režimu testování částí](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) pomocí `--unit-test` příznaku vyhodnotí, že všechny testy proběhnou.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Spouštění dávkových testů na příkazovém řádku a v pracovních postupech CI/CD

Můžete také použít NLU. DevOps balíček pro spouštění dávkových testů na příkazovém řádku.

* Použijte NLU. DevOps [test Command](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) pro odeslání testů z testovacího souboru do koncového bodu a zachycení skutečných výsledků předpovědi v souboru stejným způsobem jako u testů jednotek.
* Použijte NLU. DevOps [Compare – příkaz](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) v [režimu testu výkonnosti](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) pro měření výkonu aplikace můžete také porovnat výkon aplikace s srovnávacím testem výkonnosti, například výsledky z posledního potvrzení na hlavní nebo aktuální vydání. V režimu testu výkonu `compare` příkaz vygeneruje výstup testu nunit a [výsledky dávkových testů](./luis-glossary.md#batch-test) ve formátu JSON.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS nedeterministické školení a vliv na testování

Když LUIS prochází model, jako je záměr, potřebuje jak kladná data, tak s označením školení projevy, které jste zadali pro výuku aplikace pro model a záporná data-data, která *nejsou* platnými příklady využití tohoto modelu. Během školení LUIS sestaví záporná data jednoho modelu ze všech kladných dat, která jste zadali pro ostatní modely, ale v některých případech může způsobit nerovnováhu mezi daty. Aby se zabránilo této nedodržení, LUIS vyvzorkuje podmnožinu negativních dat nedeterministickým způsobem pro optimalizaci pro lepší školicí sadu, lepší výkon modelu a rychlejší školicí čas.

Výsledkem tohoto nedeterministického školení je, že můžete získat mírně [odlišnou předpověď mezi různými školicími cvičeními](./luis-concept-prediction-score.md), obvykle u záměrů a/nebo entit, kde [skóre předpovědi](./luis-concept-prediction-score.md) není vysoké.

Pokud chcete zakázat nedeterministické školení pro tyto verze aplikací LUIS, které vytváříte pro účely testování, použijte [rozhraní API pro nastavení verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s `UseAllTrainingData` nastavením nastaveným na `true` .

## <a name="next-steps"></a>Další kroky

* Další informace o [implementaci pracovních postupů CI/CD](luis-concept-devops-automation.md)
* Naučte se [implementovat DevOps pro Luis s GitHubem](luis-how-to-devops-with-github.md) .