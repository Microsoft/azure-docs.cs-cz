---
title: Posílání korelačních zpráv v pořadí pomocí sekvenčního convoy
description: Odeslání souvisejících zpráv v pořadí pomocí sekvenčního vzoru convoy v Azure Logic Apps s Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048655"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Odeslání souvisejících zpráv v pořadí pomocí sekvenčního convoyu v Azure Logic Apps s Azure Service Bus

Pokud potřebujete odeslat korelační zprávy v určitém pořadí, můžete postupovat podle [ *sekvenčního vzoru convoy*](/azure/architecture/patterns/sequential-convoy) při použití [Azure Logic Apps](../logic-apps/logic-apps-overview.md) pomocí [konektoru Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Korelační zprávy mají vlastnost, která definuje vztah mezi těmito zprávami, jako je ID [relace](../service-bus-messaging/message-sessions.md) v Service Bus.

Předpokládejme například, že máte 10 zpráv pro relaci s názvem "relace 1" a máte 5 zpráv pro relaci s názvem "relace 2", které jsou všechny odesílány do stejné [Service Busové fronty](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Můžete vytvořit aplikaci logiky, která bude zpracovávat zprávy z fronty, aby všechny zprávy z "relace 1" byly zpracovávány jediným spuštěním triggeru a všechny zprávy z "session 2" jsou zpracovávány dalším spuštěním triggeru.

![Obecný model sekvenčního convoy](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

V tomto článku se dozvíte, jak vytvořit aplikaci logiky, která tento model implementuje pomocí šablony **relací služby Service Bus v rámci objednávky** . Tato šablona definuje pracovní postup aplikace logiky, který začíná konektorem Service Bus **při přijetí zprávy v aktivační události fronty (Náhled)** , která přijímá zprávy z [fronty Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Tady je postup vysoké úrovně, který tato aplikace logiky provádí:

* Inicializujte relaci na základě zprávy, kterou aktivační událost načítá z fronty Service Bus.

* Čtení a zpracování všech zpráv ze stejné relace ve frontě během aktuálního spuštění pracovního postupu.

Chcete-li zkontrolovat soubor JSON této šablony, přečtěte si [GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Další informace najdete v tématu [sekvenční convoy vzor – vzory návrhu cloudu architektury Azure](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Obor názvů Service Bus a [fronta Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), což je entita zasílání zpráv, kterou použijete ve vaší aplikaci logiky. Tyto položky a aplikace logiky potřebují použít stejné předplatné Azure. Ujistěte se, že jste při vytváření fronty vybrali možnost **Povolit relace** . Pokud tyto položky nemáte, přečtěte si, [jak vytvořit obor názvů Service Bus a frontu](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Základní znalosti o tom, jak vytvářet aplikace logiky Pokud Azure Logic Apps, vyzkoušejte si rychlý Start, [vytvořte svůj první automatizovaný pracovní postup](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Zkontroluje přístup k Service Bus oboru názvů.

Pokud si nejste jistí, jestli má aplikace logiky oprávnění pro přístup k vašemu oboru názvů Service Bus, potvrďte tato oprávnění.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte svůj *obor názvů* Service Bus.

1. V nabídce obor názvů v části **Nastavení** vyberte **zásady sdíleného přístupu**. V části **deklarace identity** ověřte, že máte oprávnění ke **správě** tohoto oboru názvů.

   ![Správa oprávnění pro Service Bus obor názvů](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Nyní Získejte připojovací řetězec pro váš obor názvů Service Bus. Tento řetězec můžete použít později při vytváření připojení k oboru názvů z aplikace logiky.

   1. V podokně **zásady sdíleného přístupu** vyberte v části **zásady** možnost **RootManageSharedAccessKey**.
   
   1. Vedle primárního připojovacího řetězce vyberte tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

      ![Zkopírování připojovacího řetězce oboru názvů Service Bus](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Pokud chcete ověřit, jestli je připojovací řetězec přidružený k vašemu oboru názvů Service Bus nebo entitě zasílání zpráv, jako je například fronta, vyhledejte v připojovacím řetězci `EntityPath` parametr. Pokud tento parametr vyhledáte, připojovací řetězec je pro konkrétní entitu a není správným řetězcem pro použití s vaší aplikací logiky.

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V této části vytvoříte aplikaci logiky pomocí šablony **relace služby Service Bus s využitím korelačního doručení** , která zahrnuje Trigger a akce pro implementaci tohoto vzoru pracovního postupu. Také vytvoříte připojení k oboru názvů Service Bus a určíte název fronty Service Bus, kterou chcete použít.

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky. Na domovské stránce Azure vyberte **vytvořit prostředek**  >  **Integration**  >  **Logic App**.

1. Po zobrazení galerie šablon přejděte do části video a běžné triggery. V části **šablony** vyberte šablonu s **korelačním doručením v daném pořadí pomocí relací služby Service Bus**.

   ![Výběr "korelačního doručení v pořadí pomocí šablony relací služby Service Bus"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Po zobrazení pole potvrzení vyberte **použít tuto šablonu**.

1. V návrháři aplikace logiky v **Service Busovém** tvaru vyberte **pokračovat** a potom vyberte znaménko plus ( **+** ), které se zobrazí v obrazci.

   ![Pokud se chcete připojit k Azure Service Bus, vyberte pokračovat.](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Nyní vytvořte připojení Service Bus volbou jedné z možností:

   * Pokud chcete použít připojovací řetězec, který jste zkopírovali z oboru názvů Service Bus, postupujte takto:

     1. Vyberte možnost **ručně zadat informace o připojení**.

     1. Jako **název připojení** zadejte název připojení. V případě **připojovacího řetězce** vložte připojovací řetězec oboru názvů a vyberte **vytvořit**, například:

        ![Zadejte název připojení a připojovací řetězec Service Bus.](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Pokud tento připojovací řetězec nemáte, zjistěte, jak [Najít a zkopírovat připojovací řetězec oboru názvů Service Bus](#permissions-connection-string).

   * Pokud chcete vybrat Service Bus obor názvů z aktuálního předplatného Azure, postupujte podle těchto kroků:

     1. Jako **název připojení** zadejte název připojení. Pro **Service Bus obor názvů** vyberte obor názvů Service Bus, například:

        ![Zadejte název připojení a vyberte Service Bus obor názvů.](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Po zobrazení dalšího podokna vyberte zásady Service Bus a vyberte **vytvořit**.

        ![Vyberte zásady Service Bus a pak "vytvořit".](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Až budete hotovi, vyberte **pokračovat**.

   Návrhář aplikace logiky teď zobrazuje **korelační doručování v pořadí pomocí šablony relací služby Service Bus** , která obsahuje předem vyplněný pracovní postup s triggerem a akcemi, včetně dvou oborů, které implementují zpracování chyb, které následují po `Try-Catch` vzorci.

Nyní si můžete přečíst další informace o triggeru a akcích v šabloně nebo přejít dopředu a [poskytnout hodnoty pro šablonu aplikace logiky](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Souhrn šablony

Tady je pracovní postup nejvyšší úrovně v rámci **korelačního doručování v pořadí pomocí šablony relací služby Service Bus** , když jsou podrobnosti sbalené:

![Pracovní postup nejvyšší úrovně šablony](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Název | Description |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | V závislosti na zadaném opakování Tato aktivační událost Service Bus zkontroluje všechny zprávy ve frontě Service Bus. Pokud ve frontě existuje zpráva, aktivuje se Trigger, který vytvoří a spustí instanci pracovního postupu. <p><p>Pojem *Náhled – zámek* znamená, že Trigger odesílá požadavek na načtení zprávy z fronty. Pokud zpráva existuje, aktivační událost tuto zprávu načte a zamkne, aby se v této zprávě nedošlo k žádnému dalšímu zpracování, dokud nevyprší doba platnosti zámku. Podrobnosti získáte [inicializací relace](#initialize-session). |
| **`Init isDone`** | Tato [Akce **inicializovat proměnnou**](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) vytvoří logickou proměnnou, která je nastavena na `false` a označuje, že jsou splněny následující podmínky: <p><p>-V relaci nejsou k dispozici žádné další zprávy, které by bylo možné číst. <br>– Zámek relace již není nutné obnovit, aby bylo možné dokončit aktuální instanci pracovního postupu. <p><p>Podrobnosti najdete v tématu [inicializace relace](#initialize-session). |
| **`Try`** | Tato [Akce **oboru**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) obsahuje akce, které se spouštějí ke zpracování zprávy. Pokud dojde k potížím v `Try` oboru, další akce `Catch` **rozsahu** tento problém zpracuje. Další informace najdete v [oboru "Try"](#try-scope). |
| **`Catch`**| Tato [Akce **oboru**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) obsahuje akce, které se spustí v případě, že dojde k potížím v předchozím `Try` oboru. Další informace najdete v [oboru "catch"](#catch-scope). |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Rozsah "Try"

Tady je tok nejvyšší úrovně v `Try` [akci oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) při sbalení podrobností:

![Pracovní postup akce "Try" oboru](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Název | Description |
|------|-------------|
| **`Send initial message to topic`** | Tuto akci můžete nahradit jakoukoliv akcí, kterou chcete zpracovat první zprávu z relace ve frontě. ID relace určuje relaci. <p><p>Pro tuto šablonu Service Bus akce odešle první zprávu do Service Bus tématu. Podrobnosti najdete v tématu [zpracování úvodní zprávy](#handle-initial-message). |
| (paralelní větev) | Tato [Akce paralelní větve](../logic-apps/logic-apps-control-flow-branches.md) vytvoří dvě cesty: <p><p>-Větvi #1: pokračuje ve zpracování zprávy. Další informace najdete v tématu [větev #1: dokončení počáteční zprávy ve frontě](#complete-initial-message). <p><p>-Větvi #2: Pokud se něco nepovede, ponecháte zprávu a vydáte k vyzvednutí další spuštění triggeru. Další informace najdete v tématu [větev #2: opuštění počáteční zprávy z fronty](#abandon-initial-message). <p><p>Obě cesty se připojí později v **relaci ukončení ve frontě a akce úspěšné** , které jsou popsané v dalším řádku. |
| **`Close a session in a queue and succeed`** | Tato akce Service Bus spojuje předchozí popsané větve a zavře relaci ve frontě po jedné z následujících událostí: <p><p>– Pracovní postup dokončí zpracování zpráv, které jsou ve frontě dostupné. <br>– Pracovní postup opustí úvodní zprávu, protože došlo k nějakému problému. <p><p>Podrobnosti najdete v tématu [uzavření relace ve frontě a úspěšné](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>#1 větve: dokončení počáteční zprávy ve frontě

| Název | Description |
|------|-------------|
| `Complete initial message in queue` | Tato akce Service Bus označí úspěšné načtení zprávy jako dokončenou a odebere zprávu z fronty, aby se zabránilo rezpracování. Podrobnosti najdete v tématu [zpracování úvodní zprávy](#handle-initial-message). |
| `While there are more messages for the session in the queue` | To, [ **dokud** smyčka](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nadále nezíská zprávy, zatímco existují zprávy nebo dokud neuplyne jedna hodina. Další informace o akcích v této smyčce najdete v části, [zatímco pro relaci ve frontě existuje více zpráv](#while-more-messages-for-session). |
| **`Set isDone = true`** | Pokud žádné další zprávy neexistují, tato akce sady [ **proměnných**](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) nastaví `isDone` na `true` . |
| **`Renew session lock until cancelled`** | Tato [smyčka **dokud**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nezajistí, že se zámek relace koná v této aplikaci logiky, když existují zprávy nebo dokud jedna hodina projde. Další informace o akcích v této smyčce najdete v tématu [obnovení zámku relace až do zrušení](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>#2 větve: opustit počáteční zprávu z fronty

Pokud akce, která zpracovává první zprávu, se nezdařila, akce Service Bus, **opustit počáteční zprávu z fronty**, uvolňuje zprávu pro další instanci pracovního postupu, která se spustí při vyzvednutí a zpracování. Podrobnosti najdete v tématu [zpracování úvodní zprávy](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Obor "catch"

Pokud akce v `Try` oboru selžou, aplikace logiky musí stále ukončit relaci. `Catch` [Akce oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) se spustí, když `Try` výsledkem akce oboru je stav, `Failed` , `Skipped` nebo `TimedOut` . Obor vrátí chybovou zprávu, která obsahuje ID relace, kde došlo k problému, a ukončí aplikaci logiky.

Tady je tok nejvyšší úrovně v `Catch` akci oboru při sbalení podrobností:

![Pracovní postup akce oboru catch](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Název | Description |
|------|-------------|
| **`Close a session in a queue and fail`** | Tato akce Service Bus zavře relaci ve frontě, aby zámek relace zůstal otevřený. Podrobnosti najdete v tématu [uzavření relace ve frontě a selhání](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Tato [Akce **pole filtru**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) vytvoří pole ze vstupů a výstupů ze všech akcí v `Try` oboru na základě zadaných kritérií. V tomto případě tato akce vrátí výstupy z akcí, které byly výsledkem `Failed` stavu. Podrobnosti najdete v tématu [vyhledání zprávy o selhání z bloku try](#find-failure-message). |
| **`Select error details`** | Tato [Akce **výběru**](../logic-apps/logic-apps-perform-data-operations.md#select-action) vytvoří pole, které obsahuje objekty JSON na základě zadaných kritérií. Tyto objekty JSON jsou sestaveny z hodnot v poli vytvořeném předchozí akcí, `Find failure msg from 'Try' block` . V tomto případě tato akce vrátí pole, které obsahuje objekt JSON vytvořený z podrobností o chybě vrácených z předchozí akce. Podrobnosti najdete v tématu [Výběr podrobností o chybě](#select-error-details). |
| **`Terminate`** | Tato [Akce **ukončení**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) zastaví běh pracovního postupu, zruší všechny probíhající akce, přeskočí všechny zbývající akce a vrátí zadaný stav, ID relace a výsledek chyby z `Select error details` akce. Podrobnosti najdete v tématu [ukončení aplikace logiky](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Dokončete šablonu.

Pokud chcete zadat hodnoty pro aktivační událost a akce v rámci **korelačního doručování s využitím šablony relací služby Service Bus** , postupujte podle těchto kroků. **\*** Než budete moct uložit aplikaci logiky, musíte zadat všechny požadované hodnoty, které jsou označené hvězdičkou ().

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Inicializovat relaci

* V případě, že **je zpráva přijata v aktivační události Queue-Lock (Náhled)** , zadejte tyto informace, aby šablona mohla inicializovat relaci pomocí vlastnosti **ID relace** , například:

  ![Podrobnosti o Service Bus triggeru pro "při přijetí zprávy ve frontě (prohlížení zámku)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Zpočátku je interval dotazování nastavený na tři minuty, aby aplikace logiky neběžela častěji, než očekáváte, a výsledkem jsou neočekávané poplatky za fakturaci. V ideálním případě nastavte interval a četnost na 30 sekund, aby se aplikace logiky spustila hned po přijetí zprávy.

  | Vlastnost | Vyžadováno pro tento scénář | Hodnota | Popis |
  |----------|----------------------------|-------|-------------|
  | **Název fronty** | Yes | <*název fronty*> | Název pro dříve vytvořenou frontu Service Bus. V tomto příkladu se používá "Fabrikam-Service-Bus-Queue". |
  | **Typ fronty** | Yes | **Hlavní** | Vaše primární Service Bus fronta |
  | **ID relace** | Yes | **Další k dispozici** | Tato možnost načte relaci každého spuštění triggeru na základě ID relace ze zprávy ve frontě Service Bus. Relace je taky zamčená, takže žádná jiná aplikace logiky ani jiný klient nemůže zpracovat zprávy, které se vztahují k této relaci. Následující akce pracovního postupu zpracovávají všechny zprávy, které jsou přidružené k této relaci, jak je popsáno dále v tomto článku. <p><p>Zde jsou další informace o možnostech dalších **ID relace** : <p>- **None**: výchozí možnost, která nemá žádné relace a nedá se použít pro implementaci sekvenčního vzoru convoy. <p>- **Zadejte vlastní hodnotu**: tuto možnost použijte, pokud znáte ID relace, které chcete použít, a pro ID relace vždy chcete spustit Trigger. <p>**Poznámka**: konektor Service Bus může současně uložit omezený počet jedinečných relací z Azure Service Bus do mezipaměti konektoru. Pokud počet relací překročí tento limit, staré relace budou odebrány z mezipaměti. Další informace najdete v tématu [zprávy Exchange v cloudu s Azure Logic Apps a Azure Service Bus](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Interval** | Yes | <*počet intervalů*> | Počet časových jednotek mezi opakováními před vrácením zprávy se změnami. |
  | **Frekvence** | Yes | **Sekundy**, **minuty**, **hodiny**, **den**, **týden** nebo **měsíc** | Jednotka času, kterou má opakování použít při kontrole zprávy <p>**Tip**: Pokud chcete přidat **časové pásmo** nebo **čas spuštění**, vyberte tyto vlastnosti ze seznamu **Přidat nový parametr** . |
  |||||

  Další informace o aktivačních událostech najdete [v tématu Service Bus – při přijetí zprávy ve frontě (prohlížení zámku)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). Aktivační událost výstupuje [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

Po inicializaci relace pracovní postup pomocí akce **inicializovat proměnnou** vytvoří logickou proměnnou, která zpočátku nastavila `false` a indikuje, jestli jsou splněné následující podmínky: 

* Žádné další zprávy v relaci nejsou k dispozici ke čtení.

* Zámek relace již není nutné obnovovat, aby bylo možné dokončit aktuální instanci pracovního postupu.

![Podrobnosti o akci "inicializovat proměnnou" pro "init"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

V dalším kroku pracovní  postup provede akce na první přečtené zprávě.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Zpracování úvodní zprávy

První akcí je zástupný symbol Service Bus akce **Odeslat úvodní zprávu do tématu**, které můžete nahradit jakoukoliv jinou akcí, kterou chcete zpracovat první zprávu z relace ve frontě. ID relace určuje relaci, ze které zpráva vznikla.

Zástupná akce Service Bus odešle první zprávu do Service Busho tématu zadaného vlastností **ID relace** . Díky tomu budou všechny zprávy, které jsou přidruženy ke konkrétní relaci, přejít ke stejnému tématu. Všechny vlastnosti **ID relace** pro následné akce v této šabloně používají stejnou hodnotu ID relace.

![Service Bus podrobnosti o akci pro odeslat úvodní zprávu do tématu](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. V Service Bus akci **dokončete počáteční zprávu ve frontě**, zadejte název pro vaši frontu Service Bus a zachovejte všechny ostatní výchozí hodnoty vlastností v akci.

   ![Podrobnosti o Service Bus akci pro "dokončení počáteční zprávy ve frontě"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. V Service Bus akci zrušte **počáteční zprávu z fronty**, zadejte název pro frontu Service Bus a zachovejte všechny ostatní výchozí hodnoty vlastností v akci.

   ![Service Bus podrobnosti o akci u možnosti "opustit počáteční zprávu z fronty"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

V dalším kroku zadáte potřebné informace pro akce, které následují po **dokončení úvodní zprávy ve frontě** . Začnete s akcemi v části, **zatímco existuje více zpráv pro relaci ve smyčce fronty** .

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>I když existuje více zpráv pro relaci ve frontě.

Tato akce [ **dokud** smyčka](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nespustí, když zprávy ve frontě existují nebo dokud neuplyne jedna hodina. Chcete-li změnit časový limit smyčky, upravte hodnotu vlastnosti **časový limit** smyčky.

* Získá další zprávy z fronty, zatímco existují zprávy.

* Zkontroluje počet zbývajících zpráv. Pokud zprávy stále existují, pokračujte ve zpracování zpráv. Pokud neexistují žádné další zprávy, pracovní postup nastaví `isDone` proměnnou na `true` a ukončí smyčku.

![Dokud se ve frontě nezpracovávají zprávy smyčky](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. V Service Bus akci **získat další zprávy z relace**, zadejte název pro vaši frontu Service Bus. V opačném případě ponechte všechny ostatní výchozí hodnoty vlastností v akci.

   > [!NOTE]
   > Ve výchozím nastavení je maximální počet zpráv nastaven na hodnotu `175` , ale toto omezení je ovlivněno vlastností velikost zprávy a maximální velikost zprávy v Service Bus. Další informace najdete v tématu [velikost zprávy pro frontu](../service-bus-messaging/service-bus-quotas.md).

   ![Service Bus akce – "získat další zprávy z relace"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   V dalším kroku se pracovní postup rozdělí do těchto paralelních větví:

   * Pokud dojde k chybě nebo selhání při kontrole dalších zpráv, nastavte `isDone` proměnnou na `true` .

   * **Proces zprávy, pokud jsme dostali nějakou** podmínku, zkontroluje, jestli počet zbývajících zpráv není nula. Pokud existuje hodnota false a další zprávy, pokračujte v zpracování. Pokud je nastaveno na true a žádné další zprávy neexistují, pracovní postup nastaví `isDone` proměnnou na `true` .

   ![Podmínka – zpracování zpráv](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   V části **if false** **každá smyčka zpracuje každou zprávu** v prvním, prvním a prvním pořadí (FIFO). V **Nastavení** smyčky je nastavení **řízení souběžnosti** nastaveno na `1` , takže je zpracována pouze jedna zpráva.

   ![Každou zprávu zpracuje každá zpráva každé z nich.](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. U Service Bus akcí **dokončete zprávu ve frontě** a zrušte **zprávu ve** frontě, zadejte název pro vaši frontu Service Bus.

   ![Service Bus akce – "dokončení zprávy ve frontě" a "opuštění zprávy ve frontě"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Až v **době, kdy je dokončeno více zpráv pro relaci ve frontě** , pracovní postup nastaví `isDone` proměnnou na `true` .

V dalším kroku zadáte potřebné informace pro akce v **zámku relace obnovení, dokud nebude zrušena** smyčka.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Prodloužit zámek relace až do zrušení

Tato [smyčka **dokud**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nezajistí, že se zámek relace koná v této aplikaci logiky, zatímco zprávy ve frontě nebo dokud jednu hodinu neprojde spuštěním těchto akcí. Chcete-li změnit časový limit smyčky, upravte hodnotu vlastnosti **časový limit** smyčky.

* Zpoždění po dobu 25 sekund nebo množství času, které je menší než časový limit zámku pro zpracovávanou frontu. Nejmenší doba uzamčení je 30 sekund, takže výchozí hodnota je dostatečná. Můžete ale optimalizovat počet spuštění smyčky úpravou odpovídajícím způsobem.

* Ověřte, zda `isDone` je proměnná nastavena na hodnotu `true` .

  * Pokud není `isDone` nastavená na `true` , pracovní postup stále zpracovává zprávy, takže pracovní postup obnoví zámek na relaci ve frontě a znovu zkontroluje podmínku smyčky.

    V akci Service Bus musíte zadat název vaší fronty Service Bus, [**obnovit zámek relace ve frontě**](#renew-lock-on-session).

  * Pokud `isDone` je nastaveno na `true` , pracovní postup neobnoví zámek v relaci ve frontě a ukončí smyčku.

  ![Dokud Loop – "obnovení zámku relace do zrušení"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Obnovit zámek relace ve frontě

Tato akce Service Bus obnoví zámek v relaci ve frontě, zatímco pracovní postup stále zpracovává zprávy.

* V Service Bus akci **obnovte zámek relace ve frontě**, zadejte název pro vaši frontu Service Bus.

  ![Akce Service Bus – "zámek při obnovení relace ve frontě"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

V dalším kroku poskytnete informace potřebné k Service Bus akci, **uzavření relace ve frontě a úspěšném dokončení**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Ukončení relace ve frontě a úspěšné

Tato akce Service Bus ukončí relaci ve frontě poté, co pracovní postup dokončí zpracování všech dostupných zpráv ve frontě, nebo pracovní postup opustí úvodní zprávu.

* V Service Bus akci **zavřete relaci ve frontě a** pojmenujte ji, zadejte název pro vaši frontu Service Bus.

  ![Service Bus akce – "ukončení relace ve frontě a úspěšné"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

Následující části popisují akce v `Catch` části, které zpracovávají chyby a výjimky, ke kterým dochází ve vašem pracovním postupu.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Ukončení relace ve frontě a selhání

Tato akce Service Bus se vždycky spouští jako první akce v `Catch` oboru a uzavírá relaci ve frontě.

* V Service Bus akci **zavřete relaci ve frontě a** požádejte o selhání, zadejte název pro vaši frontu Service Bus.

  ![Service Bus akce – "ukončení relace ve frontě a selhání"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

V dalším kroku pracovní postup vytvoří pole, které obsahuje vstupy a výstupy ze všech akcí v `Try` oboru, aby aplikace logiky měla přístup k informacím o chybě nebo selhání, ke kterému došlo.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Najít zprávu o selhání z bloku try

Tato [Akce **pole filtru**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) vytvoří pole, které obsahuje vstupy a výstupy ze všech akcí v `Try` oboru založeného na zadaných kritériích pomocí [ `result()` funkce](../logic-apps/workflow-definition-language-functions-reference.md#result). V tomto případě tato akce vrátí výstupy z akcí, které mají `Failed` stav pomocí [ `equals()` funkce](../logic-apps/workflow-definition-language-functions-reference.md#equals) a [ `item()` funkce](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Akce pole filtru – "hledání zprávy o chybě z bloku try"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Tady je definice JSON pro tuto akci:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

V dalším kroku pracovní postup vytvoří pole s objektem JSON, který obsahuje informace o chybě v poli vráceném z `Find failure msg from 'Try' block` akce.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Vybrat podrobnosti o chybě

Tato [Akce **výběru**](../logic-apps/logic-apps-perform-data-operations.md#select-action) vytvoří pole, které obsahuje objekty JSON založené na výstupu pole Input z předchozí akce, `Find failure msg from 'Try' block` . Konkrétně tato akce vrátí pole, které má pouze zadané vlastnosti pro každý objekt v poli. V tomto případě pole obsahuje název akce a vlastnosti výsledku chyby.

![Vybrat akci – "vybrat podrobnosti o chybě"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Tady je definice JSON pro tuto akci:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

V dalším kroku pracovní postup zastaví běh aplikace logiky a vrátí stav spuštění spolu s dalšími informacemi o chybě nebo selhání, ke kterému došlo.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Ukončení běhu aplikace logiky

Tato [Akce **ukončení**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) zastaví běh aplikace logiky a vrátí `Failed` jako stav spuštění aplikace logiky spolu s ID relace a výsledkem chyby z `Select error details` akce.

![Ukončit akci pro zastavení spuštění aplikace logiky](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Tady je definice JSON pro tuto akci:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Uložení a spuštění aplikace logiky

Po dokončení šablony teď můžete aplikaci logiky Uložit. Na panelu nástrojů návrháře vyberte **Uložit**.

Pokud chcete otestovat aplikaci logiky, odešlete zprávy do fronty Service Bus. 

## <a name="next-steps"></a>Další kroky

* Další informace o [triggerech a akcích konektoru Service Bus](/connectors/servicebus/)
