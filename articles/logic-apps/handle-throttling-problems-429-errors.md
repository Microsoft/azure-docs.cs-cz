---
title: Zpracování problémů omezení nebo chyby 429 – příliš mnoho požadavků
description: Jak obejít omezení problémy nebo chyby "HTTP 429 příliš mnoho požadavků", v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272674"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Zpracování problémů omezení (429 – chyby "Příliš mnoho požadavků") v aplikacích Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vaše aplikace logiky vrátí chybu ["HTTP 429 příliš mnoho požadavků" při](https://developer.mozilla.org/docs/Web/HTTP/Status/429) zobrazení omezení, ke kterému dochází, když počet požadavků překročí rychlost, jakou cíl může zpracovat za určitou dobu. Omezení může způsobit problémy, jako je zpožděné zpracování dat, snížená rychlost výkonu a chyby, jako je překročení zadaných zásad opakování.

![Omezení v konektoru serveru SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Tady jsou některé běžné typy omezení, ke kterým může vaše aplikace logiky dojít:

* [Aplikace logiky](#logic-app-throttling)
* [Konektor](#connector-throttling)
* [Cílová služba nebo systém](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Omezení aplikace logiky

Služba Azure Logic Apps má vlastní [limity propustností](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Takže pokud vaše aplikace logiky překročí tato omezení, váš prostředek aplikace logiky získá omezení, nikoli pouze konkrétní instance nebo spustit.

Pokud chcete najít události omezení na této úrovni, podívejte se do podokna Metriky aplikace **logiky** na webu Azure Portal.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. V nabídce aplikace logiky vyberte v části **Sledování** **položku Metriky**.

1. V části **Název grafu**vyberte **Přidat metriku,** abyste přidali další metriku k existující.

1. V prvním panelu metriky vyberte ze seznamu **METRIKY** **události omezení akce**. Na druhém panelu metriky vyberte ze seznamu **METRIKA** **možnost Trigger Throttled Events**.

Chcete-li zpracovat omezení na této úrovni, máte tyto možnosti:

* Omezte počet instancí aplikace logiky, které lze spustit současně.

  Ve výchozím nastavení pokud je podmínka aktivační události vaší aplikace logiky splněna více než jednou současně, více instancí aktivační události pro aplikaci logiky spustit souběžně nebo *paralelně*. Toto chování znamená, že každá instance aktivační události se aktivuje před dokončením předchozí instance pracovního postupu.

  Přestože je výchozí počet instancí aktivační události, které lze souběžně spustit, [neomezený](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), můžete toto číslo omezit [zapnutím nastavení souběžnosti aktivační události](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)a v případě potřeby vybrat jiný limit než výchozí hodnotu.

* Povolte režim vysoké propustnosti.

  Aplikace logiky má [výchozí limit pro počet akcí, které lze spustit přes 5 minut klouzavý interval](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chcete-li zvýšit toto omezení na maximální počet akcí, zapněte [režim vysoké propustnostvosti](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) v aplikaci logiky.

* Zakažte chování debatování pole ("rozdělit na") chování v aktivačních událostech.

  Pokud aktivační událost vrátí pole pro zbývající akce pracovního postupu ke zpracování, nastavení [ **Rozdělení na** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) aktivační události rozdělí položky pole a spustí instanci pracovního postupu pro každou položku pole, což efektivně aktivuje více souběžných spuštění až do [limitu **Rozdělit** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Chcete-li řídit omezení, vypněte **chování Rozdělit na** a vaše aplikace logiky zpracovat celé pole s jedním voláním, spíše než zpracovat jednu položku na volání.

* Refaktorovat akce do menších aplikací logiky.

  Jak již bylo zmíněno dříve, aplikace logiky je omezena na [výchozí počet akcí, které lze spustit v průběhu 5 minut .](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) I když můžete zvýšit toto omezení povolením [režimu vysoké propustnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), můžete také zvážit, zda chcete rozdělit akce aplikace logiky do menších aplikací logiky tak, aby počet akcí, které běží v každé aplikaci logiky zůstane pod limit. Tímto způsobem snížíte zatížení jednoho prostředku aplikace logiky a distribuujete zatížení mezi více aplikací logiky. Toto řešení funguje lépe pro akce, které zpracovávají velké datové sady nebo spouštět tolik souběžně spuštěných akcí, opakování smyčky nebo akce uvnitř každé iterace smyčky, které překračují limit provádění akce.

  Například tato aplikace logiky provádí veškerou práci získat tabulky z databáze serveru SQL Server a získá řádky z každé tabulky. **Pro každou** smyčku současně iteruje přes každou tabulku tak, aby **get řádky** akce vrátí řádky pro každou tabulku. Na základě množství dat v těchto tabulkách mohou tyto akce překročit limit provádění akcí.

  ![Refaktoring aplikace logiky "před"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Po refaktoringu aplikace logiky je teď nadřazená a podřízená aplikace logiky. Nadřazený získá tabulky z SQL Server a pak volá podřízenou aplikaci logiky pro každou tabulku získat řádky:

  ![Vytvoření aplikace logiky pro jednu akci](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Tady je podřízená aplikace logiky, která se nazývá nadřazenou aplikací logiky, aby získala řádky pro každou tabulku:

  ![Vytvoření jiné aplikace logiky pro druhou akci](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Omezení konektoru

Každý konektor má své vlastní omezení omezení, které najdete na stránce technické reference konektoru. Například [konektor Azure Service Bus](https://docs.microsoft.com/connectors/servicebus/) má omezení omezení, které umožňuje až 6 000 volání za minutu, zatímco konektor SQL Server má omezení [omezení, které se liší v závislosti na typu operace](https://docs.microsoft.com/connectors/sql/).

Některé aktivační události a akce, například HTTP, mají [zásady opakování,](../logic-apps/logic-apps-exception-handling.md#retry-policies) které můžete přizpůsobit na základě [omezení zásad opakování](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) k implementaci zpracování výjimek. Tato zásada určuje, zda a jak často aktivační událost nebo akce opakuje požadavek při selhání původního požadavku nebo časový limit a výsledkem je odpověď 408, 429 nebo 5xx. Takže při omezení začíná a vrátí chybu 429, Logic Apps následuje zásady opakování, kde je podporován.

Chcete-li zjistit, zda aktivační událost nebo akce podporuje zásady opakování, zkontrolujte nastavení aktivační události nebo akce. Chcete-li zobrazit pokusy o opakování aktivační události nebo akce, přejděte do historie spuštění aplikace logiky, vyberte spuštění, které chcete zkontrolovat, a rozbalte tuto aktivační událost nebo akci, abyste zobrazili podrobnosti o vstupech, výstupech a všech opakovaných pokusech, například:

![Zobrazení historie spuštění akce, opakování, vstupů a výstupů](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Přestože historie opakování poskytuje informace o chybě, může mít potíže s rozlišováním mezi omezením konektoru a [omezením cíle](#destination-throttling). V takovém případě bude pravděpodobně nutné zkontrolovat podrobnosti odpovědi nebo provést některé výpočty intervalu omezení k identifikaci zdroje.

U aplikací logiky v globální službě Azure Logic Apps s více tenanty dochází k omezení na úrovni *připojení.* Takže například pro aplikace logiky, které běží v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), omezení stále dochází pro připojení bez ISE, protože běží v globální, víceklientské služby Logic Apps. Připojení ISE, které jsou vytvořeny konektory ISE, však nejsou omezeny, protože běží ve vašem ISE.

Chcete-li zpracovat omezení na této úrovni, máte tyto možnosti:

* Nastavte více připojení pro jednu akci tak, aby aplikace logiky rozdělí data pro zpracování.

  Pro tuto možnost zvažte, zda můžete distribuovat zatížení rozdělením požadavků akce mezi více připojení do stejného cíle pomocí stejných pověření.

  Předpokládejme například, že aplikace logiky získá tabulky z databáze serveru SQL Server a pak získá řádky z každé tabulky. Na základě počtu řádků, které musíte zpracovat, můžete použít více připojení a více **Pro každé** smyčky rozdělit celkový počet řádků do menších sad pro zpracování. Tento scénář používá dva **Pro každou** smyčku rozdělit celkový počet řádků na polovinu. První **Pro každou** smyčku používá výraz, který získá první polovinu. Druhý **Pro každou** smyčku používá jiný výraz, který získá druhou polovinu, například:<p>

    * Výraz 1: `take()` Funkce získá přední část kolekce. Další informace naleznete v [ **`take()`** části funkce](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Výraz 2: `skip()` Funkce odebere přední část kolekce a vrátí všechny ostatní položky. Další informace naleznete v [ **`skip()`** části funkce](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Zde je vizuální příklad, který ukazuje, jak můžete používat tyto výrazy:

    ![Vytvoření a použití více připojení pro jednu akci](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Nastavte pro každou akci jiné připojení.

  V případě této možnosti zvažte, zda můžete distribuovat zatížení rozprostřením požadavků jednotlivých akcí prostřednictvím vlastního připojení, i když se akce připojují ke stejné službě nebo systému a používají stejná pověření.

  Předpokládejme například, že aplikace logiky získá tabulky z databáze serveru SQL Server a získá každý řádek v každé tabulce. Můžete použít samostatná připojení tak, aby získávání tabulek používat jedno připojení, zatímco získávání každý řádek používá jiné připojení.

  ![Vytvoření a použití různých připojení pro každou akci](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Změňte souběžnost nebo paralelismus na [smyčku "Pro každý"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Ve výchozím nastavení "Pro každou" opakování smyčky spustit ve stejnou dobu až do [limitu souběžnosti](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud máte konektor, který je stále omezen uvnitř "Pro každý" smyčky, můžete snížit počet opakování smyčky, které běží paralelně. Další informace najdete v těchto tématech:
  
  * ["Pro každou" smyčky - změna souběžnosti nebo spustit postupně](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schéma jazyka definice pracovního postupu – pro každou smyčku](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schéma jazyka definice pracovního postupu – změna souběžnosti smyčky "Pro každý"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schéma jazyka definice pracovního postupu – postupně spouštějí smyčky "Pro každý"](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Omezení cílové služby nebo systému

Zatímco konektor má vlastní omezení omezení, cílová služba nebo systém, který je volán konektormůže mít také omezení omezení. Například některá rozhraní API na serveru Microsoft Exchange Server mají přísnější omezení než konektor outlooku Office 365.

Ve výchozím nastavení instance aplikace logiky a všechny smyčky nebo větve uvnitř těchto instancí spustit *paralelně*. Toto chování znamená, že více instancí může volat stejný koncový bod současně. Každá instance neví o existenci druhé, takže pokusy o opakování neúspěšných akcí mohou vytvořit [podmínky časování,](https://en.wikipedia.org/wiki/Race_condition) kde se více volání pokusí spustit současně, ale aby byla tato volání úspěšná, musí tato volání dorazit do cílové služby nebo systému, než začne dochází k omezení.

Předpokládejme například, že máte pole, které má 100 položek. Pomocí smyčky "pro každý" iterát prostřednictvím pole a zapnout řízení souběžnosti smyčky tak, aby můžete omezit počet paralelních iterací na 20 nebo [aktuální výchozí limit](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Uvnitř této smyčky, akce vloží položku z pole do databáze serveru SQL Server, který umožňuje pouze 15 volání za sekundu. Tento scénář má za následek problém omezení, protože nevyřízené položky opakování sestavení a nikdy získat ke spuštění.

Tato tabulka popisuje časovou osu pro to, co se děje ve smyčce, když je interval opakování akce 1 sekunda:

| Bod v čase | Počet spuštěných akcí | Počet akcí, které se nezdaří | Počet čekání na opakování |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 sekund | 20 vložek | 5 selhání, z důvodu limitu SQL | 5 opakování |
| T + 0,5 sekundy | 15 vložek, kvůli předchozím 5 opakováním čekání | Všech 15 selhání, z důvodu předchozího limitu SQL stále v platnosti po dobu dalších 0,5 sekundy | 20 opakování <br>(předchozích 5 + 15 nových) |
| T + 1 sekunda | 20 vložek | 5 selhání plus předchozích 20 opakování, kvůli limitu SQL | 25 opakování (předchozích 20 + 5 nových)
|||||

Chcete-li zpracovat omezení na této úrovni, máte tyto možnosti:

* Vytvořte aplikace logiky tak, aby každý zpracovává jednu operaci.

  * Pokračování s ukázkovým scénářem SERVERU SQL Server v této části můžete vytvořit aplikaci logiky, která umístí položky pole do fronty, jako je [například fronta Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Potom vytvoříte jinou aplikaci logiky, která provádí pouze operaci vložení pro každou položku v této frontě. Tímto způsobem pouze jedna instance aplikace logiky spustí v určitém okamžiku, který buď dokončí operaci vložení a přejde na další položku ve frontě, nebo instance získá 429 chyb, ale nepokusí se o neproduktivní opakování.

  * Vytvořte nadřazenou aplikaci logiky, která volá podřízenou nebo vnořenou aplikaci logiky pro každou akci. Pokud rodič potřebuje volat různé podřízené aplikace na základě výsledku rodiče, můžete použít akci podmínky nebo přepnout akci, která určuje, kterou podřízenou aplikaci chcete volat. Tento vzor vám může pomoci snížit počet volání nebo operací.

    Předpokládejme například, že máte dvě aplikace logiky, každá s aktivační událostí dotazování, která každou minutu kontroluje váš e-mailový účet pro konkrétní předmět, například "Úspěch" nebo "Selhání". Výsledkem tohoto nastavení je 120 hovorů za hodinu. Místo toho pokud vytvoříte jednu nadřazenou aplikaci logiky, která každou minutu dotazuje, ale volá podřízenou aplikaci logiky, která běží na základě toho, zda je předmět "Úspěch" nebo "Selhání", snížíte počet kontrol dotazování na polovinu nebo 60 v tomto případě.

* Nastavte dávkové zpracování.

  Pokud cílová služba podporuje dávkové operace, můžete řešit omezení zpracováním položek ve skupinách nebo dávkách, nikoli jednotlivě. Chcete-li implementovat řešení dávkového zpracování, vytvořte aplikaci logiky "dávkový příjemce" a aplikaci logiky "dávkového odesílatele". Odesinebožení dávky shromažďuje zprávy nebo položky, dokud nejsou splněna zadaná kritéria, a potom tyto zprávy nebo položky odešle do jedné skupiny. Dávkový příjemce přijme tuto skupinu a zpracuje tyto zprávy nebo položky. Další informace naleznete v [tématu Dávkové zpracování zpráv ve skupinách](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Použijte verze webhooku pro aktivační události a akce, nikoli pro verze dotazování.

  Proč? Aktivační událost dotazování pokračuje v kontrole cílové služby nebo systému v určitých intervalech. Velmi častý interval, například každou sekundu, může způsobit problémy omezení. Aktivační událost nebo akce webhooku, například [HTTP Webhook](../connectors/connectors-native-webhook.md), však vytvoří pouze jedno volání cílové služby nebo systému, ke kterému dojde v době předplatného a požaduje, aby cíl upozorní aktivační událost nebo akci pouze v případě, že dojde k události. Tímto způsobem aktivační událost nebo akce nemusí neustále kontrolovat cíl.
  
  Takže pokud cílová služba nebo systém podporuje webhooky nebo poskytuje konektor, který má webhookverzi verzi, tato možnost je lepší než pomocí verze dotazování. Chcete-li identifikovat webové hák aktivační události `ApiConnectionWebhook` a akce, zkontrolujte, že mají typ nebo že nevyžadují, abyste zadali opakování. Další informace naleznete v tématu [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a [APIConnectionWebhook akce](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Další kroky

* Další informace o [omezeních a konfiguraci aplikací Logic Apps](../logic-apps/logic-apps-limits-and-config.md)
