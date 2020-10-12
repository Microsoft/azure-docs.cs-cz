---
title: Zpracování potíží s omezováním nebo chyby 429-příliš mnoho požadavků
description: Jak obejít problémy s omezováním nebo chyby HTTP 429 příliš mnoho požadavků, v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 495847d31682aff64fed3c81b1d5d68cf67dfd38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086434"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Zpracování potíží s omezením (429-počet chybných požadavků) v Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md)vaše aplikace logiky vrátí [chybu HTTP 429 s příliš velkým počtem požadavků](https://developer.mozilla.org/docs/Web/HTTP/Status/429) , pokud se omezuje, což nastane, když počet požadavků překročí rychlost, s jakou může cíl zvládnout určitou dobu. Omezování může vytvářet problémy, jako je opožděné zpracování dat, snížená rychlost výkonu a chyby, jako je například překročení zadaných zásad opakování.

![Omezování v konektoru SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Tady jsou některé běžné typy omezení, které vaše aplikace logiky může mít:

* [Aplikace logiky](#logic-app-throttling)
* [Připojovací](#connector-throttling)
* [Cílová služba nebo systém](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Omezení aplikace logiky

Služba Azure Logic Apps má vlastní [limity propustnosti](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Takže pokud vaše aplikace logiky překročí tato omezení, váš prostředek aplikace logiky se omezí, ne pouze konkrétní instance nebo spuštění.

Pokud chcete na této úrovni najít události omezování, podívejte se na podokno **metrik** vaší aplikace logiky v Azure Portal.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **monitorování**vyberte **metriky**.

1. V části **název grafu**vyberte **Přidat metriky** , abyste přidali další metriku do existující.

1. V prvním řádku metriky v seznamu **metrika** vyberte **Akce omezené události**. V druhém řádku metriky v seznamu **metrika** vyberte **aktivační události s omezením**.

Chcete-li na této úrovni zvládnout omezování, máte tyto možnosti:

* Omezte počet instancí aplikace logiky, které lze spustit současně.

  Ve výchozím nastavení platí, že pokud je podmínka triggeru vaší aplikace logiky splněna více než jednou, spustí se *souběžně nebo paralelně*více instancí triggeru pro vaši aplikaci logiky. Toto chování znamená, že každá instance triggeru je aktivována před dokončením předchozí instance pracovního postupu.

  I když výchozí počet instancí triggerů, které mohou být souběžně spuštěny, je [neomezený](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), můžete toto číslo omezit [zapnutím nastavení souběžnosti triggeru](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)a v případě potřeby vybrat jiný limit, než je výchozí hodnota.

* Povolí režim vysoké propustnosti.

  Aplikace logiky má [výchozí limit počtu akcí, které mohou běžet po dobu 5 minut](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Pokud chcete toto omezení zvýšit na maximální počet akcí, zapněte v aplikaci logiky [režim vysoké propustnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) .

* V aktivačních událostech zakažte chování při dedávkování pole (rozdělení na).

  Pokud aktivační událost vrátí pole pro zbývající akce pracovního postupu, které mají být zpracovány, rozdělení triggeru [ **při** nastavení](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) rozdělí položky pole a spustí instanci pracovního postupu pro každou položku pole. aplikace efektivně aktivuje více souběžných spuštění až do [ **rozdělení na** limit](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Chcete-li řídit omezování, vypněte chování **rozdělení** a aplikace logiky zpracuje celé pole jediným voláním, nikoli zpracování jedné položky za volání.

* Refaktorujte akce do menších aplikací logiky.

  Jak bylo zmíněno dříve, aplikace logiky je omezená na [výchozí počet akcí, které mohou běžet po dobu 5 minut](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). I když tento limit můžete zvýšit tím, že povolíte [režim vysoké propustnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), můžete také zvážit, jestli chcete rozdělit akce aplikace logiky na menší aplikace logiky, aby počet akcí, které se spouštějí v každé aplikaci logiky, zůstal v rámci limitu. Tímto způsobem snížíte zatížení jednoho prostředku aplikace logiky a rozšíříte zátěž napříč více aplikacemi logiky. Toto řešení funguje lépe pro akce, které zpracovávají velké datové sady, nebo se vytočí, takže mnoho souběžně běžících akcí, iterací smyčky nebo akcí uvnitř každé iterace smyčky, které překračují limit provádění akcí.

  Tato aplikace logiky například provede veškerou práci k získání tabulek z databáze SQL Server a získá řádky z každé tabulky. **Pro každou** smyčku souběžně projde každou tabulku tak, aby akce **získat řádky** vrátila řádky pro každou tabulku. V závislosti na množství dat v těchto tabulkách můžou tyto akce překročit limit provádění akcí.

  ![Refaktoring aplikace logiky "před refaktoringem"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Aplikace logiky je teď po refaktoring nadřazená a podřízená aplikace logiky. Nadřazený objekt získá tabulky z SQL Server a potom zavolá podřízenou aplikaci logiky pro každou tabulku, aby získala řádky:

  ![Vytvoření aplikace logiky pro jednu akci](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Tady je podřízená aplikace logiky, která je volána nadřazenou aplikací logiky, aby získala řádky pro každou tabulku:

  ![Vytvoření další aplikace logiky pro druhou akci](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Omezení konektoru

Každý konektor má své vlastní omezení omezení, které můžete najít na stránce technické reference pro konektor. Například [konektor Azure Service Bus](/connectors/servicebus/) má omezení omezování, které umožňuje až 6 000 volání za minutu, zatímco konektor SQL Server má omezení [, která se liší v závislosti na typu operace](/connectors/sql/).

Některé triggery a akce, jako je HTTP, mají ["zásady opakování"](../logic-apps/logic-apps-exception-handling.md#retry-policies) , které můžete přizpůsobit na základě [omezení zásad opakování](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) pro implementaci zpracování výjimek. Tato zásada určuje, jestli a jak často Trigger nebo akce opakuje požadavek, když původní požadavek vyprší nebo vyprší časový limit, a výsledkem je odpověď 408, 429 nebo 5xx. Takže když se omezení zahájí a vrátí 429, Logic Apps se podle podporované zásady opakování.

Pokud chcete zjistit, jestli aktivační událost nebo akce podporuje zásady opakování, podívejte se na nastavení triggeru nebo akce. Chcete-li zobrazit pokusy o opakování triggeru nebo akce, klikněte na historii spuštění vaší aplikace logiky, vyberte běh, který chcete zkontrolovat, a rozbalte tuto aktivační událost nebo akci, abyste zobrazili podrobnosti o vstupech, výstupech a všech opakovaných pokusech, například:

![Zobrazit historii spuštění akce, opakování, vstupy a výstupy](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

I když Historie opakování poskytuje informace o chybě, může se stát, že budete mít problémy rozdíl mezi omezením konektoru a [omezením cíle](#destination-throttling). V takovém případě může být nutné zkontrolovat podrobnosti odpovědi nebo provést několik výpočtů intervalu omezení pro identifikaci zdroje.

Pro Logic Apps v globální službě Azure Logic Apps služby pro více tenantů probíhá omezování na úrovni *připojení* . Například pro aplikace logiky, které běží v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), se k omezování stále dochází pro připojení bez ISE, protože běží v globální Logic Apps službě pro více tenantů. Připojení ISE, která jsou vytvořena pomocí konektorů ISE, nejsou omezena, protože jsou spouštěna v ISE.

Chcete-li na této úrovni zvládnout omezování, máte tyto možnosti:

* Nastavte více připojení pro jednu akci, aby aplikace logiky vydělí data pro zpracování.

  V případě této možnosti zvažte, jestli můžete distribuovat úlohy pomocí stejných přihlašovacích údajů, a to tak, že vydělíte požadavky akce mezi více připojení ke stejnému cíli.

  Předpokládejme například, že vaše aplikace logiky získá tabulky z SQL Server databáze a pak získá řádky z každé tabulky. Na základě počtu řádků, které je třeba zpracovat, můžete pro **každou** smyčku použít více připojení a násobek k rozdělení celkového počtu řádků na menší sady pro zpracování. Tento scénář používá dvě **pro každou** smyčku k rozdělení celkového počtu řádků na polovinu. První **pro každou** smyčku používá výraz, který získá první polovinu. Druhý **pro každou** smyčku používá jiný výraz, který získá druhou polovinu, například:<p>

    * Výraz 1: `take()` funkce získá přední část kolekce. Další informace najdete v tématu [ **`take()`** funkce](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Výraz 2: `skip()` funkce odebere přední stranu kolekce a vrátí všechny ostatní položky. Další informace najdete v tématu [ **`skip()`** funkce](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Tady je příklad vizuálního příkladu, který ukazuje, jak můžete tyto výrazy použít:

    ![Vytvoření a použití více připojení pro jednu akci](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Pro každou akci nastavte jiné připojení.

  V případě této možnosti zvažte, jestli můžete distribuovat úlohy tím, že rozšíříte žádosti o každou akci přes své vlastní připojení, a to i v případě, že se akce připojí ke stejné službě nebo systému a používají stejné přihlašovací údaje.

  Předpokládejme například, že vaše aplikace logiky získá tabulky z databáze SQL Server a získá každý řádek v každé tabulce. Můžete použít samostatná připojení, aby bylo možné tabulky získat pomocí jednoho připojení, zatímco při získávání každého řádku se používá jiné připojení.

  ![Vytvoření a použití různých připojení pro každou akci](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Změna souběžnosti nebo paralelismu na [smyčku For Each](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Ve výchozím nastavení se iterace smyčky for each spouští současně až do [limitu souběžnosti](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud máte konektor, který se omezuje v rámci smyčky for each, můžete snížit počet iterací cyklů, které běží paralelně. Další informace najdete v těchto tématech:
  
  * ["Pro každou" smyčku – změna souběžnosti nebo spuštění sekvenčně](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schéma jazyka definice pracovního postupu – pro každou smyčku](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schéma jazyka definice pracovního postupu – Změna pro každou souběžnou smyčku](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schéma jazyka definice pracovního postupu – spuštění smyčky for each postupně](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Cílová služba nebo omezení systému

I když má konektor své vlastní omezení omezování, může také cílová služba nebo systém, který je volaný konektorem, mít omezení omezování. Některá rozhraní API v systému Microsoft Exchange Server například mají přísnější omezení než konektor Office 365 Outlook.

Ve výchozím nastavení běží *paralelně*instance aplikace logiky a všechny smyčky nebo větve uvnitř těchto instancí. Toto chování znamená, že více instancí může volat stejný koncový bod současně. Každá instance neví o existenci druhé instance, takže pokus o opakování neúspěšných akcí může vytvořit [Konflikty časování](https://en.wikipedia.org/wiki/Race_condition) , ve kterých se vícenásobné volání snaží spustit současně, ale aby bylo úspěšné, musí tato volání dorazit do cílové služby nebo systému, než začne dojít k omezení.

Předpokládejme například, že máte pole, které má 100 položek. Použijte smyčku For Each k iterování v poli a zapněte řízení souběžnosti smyčky, abyste mohli omezit počet paralelních iterací na 20 nebo na [aktuální výchozí omezení](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). V rámci této smyčky akce vloží položku z pole do databáze SQL Server, což povoluje pouze 15 volání za sekundu. Výsledkem tohoto scénáře je problém s omezením, protože nevyřízené položky opakování sestavení a nikdy se nespouštějí.

Tato tabulka popisuje časovou osu, co se stane ve smyčce, když je interval opakování akce 1 sekunda:

| Bod v čase | Počet akcí, které se spustí | Počet akcí, které selžou | Počet čekajících opakování |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 s | 20 vložení | 5 selhání kvůli omezení SQL | 5 opakování |
| T + 0,5 sekund | 15 vkládaných z důvodu předchozích 5 pokusů čeká na vyřízení. | Všechna 15 selžou, protože předchozí omezení SQL je stále v platnosti pro jiné 0,5 sekund. | 20 opakování <br>(předchozí 5 + 15 novinek) |
| T + 1 sekunda | 20 vložení | 5 selhání plus předchozích 20 opakování z důvodu omezení SQL | 25 opakování (předchozí 20 + 5 novinek)
|||||

Chcete-li na této úrovni zvládnout omezování, máte tyto možnosti:

* Vytvářejte aplikace logiky tak, aby každý z nich vytáhl jednu operaci.

  * Pokračování v příkladu SQL Server scénáři v této části můžete vytvořit aplikaci logiky, která umístí položky pole do fronty, jako je například [fronta Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Pak vytvoříte další aplikaci logiky, která provede pouze operaci vložení pro každou položku v této frontě. Tímto způsobem se v jakémkoli čase spustí jenom jedna instance aplikace logiky, která buď dokončí operaci vložení, přesune se k další položce ve frontě, nebo instance získá 429 chyb, ale nepokusí se o nepokusné opakované pokusy.

  * Vytvořte nadřazenou aplikaci logiky, která pro každou akci volá podřízenou nebo vnořenou aplikaci logiky. Pokud nadřazený objekt potřebuje volat různé podřízené aplikace na základě výsledku nadřazeného objektu, můžete použít akci podmínky nebo přepnout akci, která určuje, kterou podřízenou aplikaci zavolat. Tento model vám může přispět k omezení počtu volání nebo operací.

    Předpokládejme například, že máte dvě aplikace logiky, každý s triggerem cyklického dotazování, který každou minutu kontroluje váš e-mailový účet pro konkrétní předmět, například "úspěch" nebo "selhání". Výsledkem tohoto nastavení je 120 volání za hodinu. Místo toho, pokud vytvoříte jednu nadřazenou aplikaci logiky, která se dotazuje každou minutu, ale volá podřízenou aplikaci logiky, která se spustí na základě toho, jestli je předmět "úspěch" nebo "selhání", můžete v tomto případě vyjmout počet kontrol cyklického dotazování na polovinu nebo 60.

* Nastavte dávkové zpracování.

  Pokud cílová služba podporuje operace služby Batch, můžete omezit omezování zpracováním položek ve skupinách nebo dávkách, nikoli jednotlivě. K implementaci řešení dávkového zpracování vytvoříte aplikaci logiky pro přijímače a aplikaci logiky Batch odesilatele. Odesílatel služby Batch shromáždí zprávy nebo položky, dokud nebudou zadaná kritéria splněna, a poté odesílá tyto zprávy nebo položky do jedné skupiny. Přijímač dávky přijímá tuto skupinu a zpracovává tyto zprávy nebo položky. Další informace najdete v tématu [dávkové zpracování zpráv ve skupinách](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Používejte verze Webhooku pro triggery a akce, nikoli verze cyklického dotazování.

  Proč? Aktivační událost cyklického dotazování stále kontroluje v určitých intervalech cílovou službu nebo systém. Velmi častý interval, například každou sekundu, může vytvářet problémy omezování. Trigger nebo akce Webhooku, jako je [Webhook http](../connectors/connectors-native-webhook.md), ale vytvoří jenom jedno volání do cílové služby nebo systému, ke kterému dojde v době odběru, a požadavky, které cíl upozorní na Trigger nebo akci jenom v případě, že dojde k události. Tímto způsobem nemusí Trigger nebo akce průběžně kontrolovat cíl.
  
  Takže pokud cílová služba nebo systém podporuje Webhooky nebo konektor, který má verzi Webhooku, tato možnost je lepší než použití verze dotazování. Chcete-li identifikovat triggery a akce Webhooku, potvrďte, že mají `ApiConnectionWebhook` typ nebo že nevyžadují, abyste určili opakování. Další informace najdete v tématu [Trigger vstupech apiconnectionwebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) a [Akce vstupech apiconnectionwebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Další kroky

* Další informace o [omezeních Logic Apps a konfiguraci](../logic-apps/logic-apps-limits-and-config.md)
