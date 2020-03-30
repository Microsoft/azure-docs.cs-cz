---
title: Nasazení s nulovými prostoji pro trvanlivé funkce
description: Zjistěte, jak povolit orchestraci trvalých funkcí pro nasazení s nulovými prostoji.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231261"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Nasazení s nulovými prostoji pro trvanlivé funkce

[Model spolehlivého spuštění](durable-functions-checkpointing-and-replay.md) trvalé funkce vyžaduje, aby orchestrations být deterministický, což vytváří další výzvu, aby zvážila při nasazování aktualizací. Pokud nasazení obsahuje změny podpisů funkce aktivity nebo logiky orchestratoru, instance orchestrace za letu se nezdaří. Tato situace je zejména problém pro instance dlouhotrvající orchestrations, které mohou představovat hodiny nebo dny práce.

Chcete-li těmto selháním zabránit, máte dvě možnosti: 
- Zpozdit nasazení, dokud nebudou dokončeny všechny spuštěné instance orchestrace.
- Ujistěte se, že všechny spuštěné instance orchestrace používají existující verze vašich funkcí. 

> [!NOTE]
> Tento článek obsahuje pokyny pro aplikace funkcí, které cílí na trvalé funkce 1.x. Nebyla aktualizována, aby zohlednila změny zavedené v trvanlivé funkce 2.x. Další informace o rozdílech mezi verzemi rozšíření naleznete v [tématu Durable Functions versions](durable-functions-versions.md).

Následující graf porovnává tři hlavní strategie pro dosažení nasazení s nulovými prostoji pro trvalé funkce: 

| Strategie |  Kdy je použít | Výhody | Nevýhody |
| -------- | ------------ | ---- | ---- |
| [Správa verzí](#versioning) |  Aplikace, které nedochází k častým [změnám.](durable-functions-versioning.md) | Jednoduché implementace. |  Zvýšená velikost aplikace funkce v paměti a počet funkcí.<br/>Duplikace kódu. |
| [Kontrola stavu pomocí slotu](#status-check-with-slot) | Systém, který nemá dlouhotrvající orchestrace trvající více než 24 hodin nebo často překrývající orchestrace. | Jednoduchý základ kódu.<br/>Nevyžaduje další správu aplikací funkce. | Vyžaduje další správu účtu úložiště nebo centra úloh.<br/>Vyžaduje období času, kdy jsou spuštěny žádné orchestrations. |
| [Směrování aplikací](#application-routing) | Systém, který nemá období času, kdy orchestrations nejsou spuštěny, jako jsou například ty časové úseky s orchestrations, které trvají déle než 24 hodin nebo s často překrývající orchestrace. | Zpracovává nové verze systémů s neustále spuštěnými orchestracemi, které mají narušující změny. | Vyžaduje inteligentní aplikační směrovač.<br/>Mohl by max out počet aplikací funkcí povolených vašeho předplatného. Výchozí hodnota je 100. |

## <a name="versioning"></a>Správa verzí

Definujte nové verze funkcí a ponechte staré verze ve své aplikaci funkce. Jak můžete vidět v diagramu, verze funkce se stane součástí jejího názvu. Vzhledem k tomu, že předchozí verze funkcí jsou zachovány, instanci orchestrace za letu můžete i nadále odkazovat na ně. Mezitím požadavky na nové instance orchestrace volání pro nejnovější verzi, které vaše funkce klienta orchestrace můžete odkazovat z nastavení aplikace.

![Strategie správy verzí](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

V této strategii musí být zkopírovány všechny funkce a její odkazy na jiné funkce musí být aktualizovány. Můžete to usnadnit napsáním skriptu. Tady je [ukázkový projekt](https://github.com/TsuyoshiUshio/DurableVersioning) se skriptem pro migraci.

>[!NOTE]
>Tato strategie používá sloty nasazení, aby se zabránilo prostojům během nasazení. Podrobnější informace o tom, jak vytvářet a používat nové sloty nasazení, najdete v [tématu sloty pro nasazení funkce Azure](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Kontrola stavu pomocí slotu

Zatímco aktuální verze aplikace funkce běží ve vašem produkčním slotu, nasaďte novou verzi aplikace funkce do pracovního slotu. Před výměnou produkčních a pracovních slotů zkontrolujte, zda existují nějaké spuštěné instance orchestrace. Po dokončení všech instancí orchestrace můžete provést prohození. Tato strategie funguje, pokud máte předvídatelné období, kdy žádné instance orchestrace jsou v letu. Toto je nejlepší přístup, když orchestrations nejsou dlouhotrvající a při spuštění orchestrace často překrývají.

### <a name="function-app-configuration"></a>Konfigurace aplikace funkce

K nastavení tohoto scénáře použijte následující postup.

1. [Přidejte sloty nasazení](../functions-deployment-slots.md#add-a-slot) do aplikace funkce pro pracovní a produkční prostředí.

1. Pro každý slot nastavte [nastavení aplikace AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) na připojovací řetězec účtu sdíleného úložiště. Tento připojovací řetězec účtu úložiště se používá v době runtime Funkce Azure. Tento účet používá za běhu Azure Functions a spravuje klíče funkce.

1. Pro každý slot vytvořte nové nastavení `DurableManagementStorage`aplikace, například . Nastavte jeho hodnotu na připojovací řetězec různých účtů úložiště. Tyto účty úložiště jsou používány rozšíření trvalé funkce pro [spolehlivé provádění](durable-functions-checkpointing-and-replay.md). Pro každý slot použijte samostatný účet úložiště. Toto nastavení neoznačte jako nastavení slotu pro nasazení.

1. V [části durableTask souboru host.json aplikace](durable-functions-bindings.md#hostjson-settings)funkce `azureStorageConnectionStringName` zadejte jako název nastavení aplikace, které jste vytvořili v kroku 3.

Následující diagram znázorňuje popsanou konfiguraci slotů nasazení a účtů úložiště. V tomto scénáři potenciální předběžné nasazení verze 2 aplikace funkce běží v produkčním slotu, zatímco verze 1 zůstane v pracovní patici.

![Nasazení slotů a účtů úložiště](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>příklady hostitele.json

Následující fragmenty JSON jsou příklady nastavení připojovacího řetězce v souboru *host.json.*

#### <a name="functions-20"></a>Funkce 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Konfigurace kanálu CI/CD

Nakonfigurujte kanál CI/CD pro nasazení pouze v případě, že vaše aplikace funkcí nemá žádné čekající nebo spuštěné instance orchestrace. Když používáte Azure Pipelines, můžete vytvořit funkci, která kontroluje tyto podmínky, jako v následujícím příkladu:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Dále nakonfigurujte pracovní bránu tak, aby počkala, dokud nebudou spuštěny žádné orchestrations. Další informace naleznete v [tématu Release deployment control using gates](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Brána nasazení](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines zkontroluje vaši aplikaci funkcí pro spuštění instancí orchestrace před zahájením nasazení.

![Brána nasazení (spuštěna)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Nyní by měla být nová verze aplikace funkce nasazena do pracovního slotu.

![Pracovní slot](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Nakonec vyměňte sloty. 

Nastavení aplikace, které nejsou označeny jako nastavení slotu nasazení jsou také prohozeny, takže aplikace verze 2 udržuje svůj odkaz na účet úložiště A. Vzhledem k tomu, že stav orchestrace je sledován v účtu úložiště, všechny orchestrations spuštěné v aplikaci verze 2 nadále běžet v novém slotu bez přerušení.

![Slot nasazení](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Chcete-li použít stejný účet úložiště pro oba sloty, můžete změnit názvy rozbočovačů úloh. V takovém případě je třeba spravovat stav slotů a nastavení HubName aplikace. Další informace najdete [v tématu Centra úloh v tématu Trvalé funkce](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Směrování aplikací

Tato strategie je nejsložitější. Lze jej však použít pro aplikace funkcí, které nemají čas mezi spuštěním orchestrations.

Pro tuto strategii je nutné vytvořit *směrovač aplikace* před trvalými funkcemi. Tento router lze implementovat pomocí odolných funkcí. Router nese odpovědnost za:

* Nasaďte aplikaci funkce.
* Spravujte verzi durable functions. 
* Požadavky na orchestraci směrujte do funkčních aplikací.

Při prvním přijetí požadavku orchestrace směrovač provádí následující úkoly:

1. Vytvoří novou aplikaci funkcí v Azure.
2. Nasazuje kód aplikace funkce do nové aplikace funkcí v Azure.
3. Předá požadavek orchestrace na novou aplikaci.

Směrovač spravuje stav, která verze kódu vaší aplikace se nasazuje do které aplikace funkce v Azure.

![Směrování aplikací (poprvé)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Směrovač směruje požadavky na nasazení a orchestraci na příslušnou aplikaci funkcí na základě verze odeslané s požadavkem. Ignoruje verzi opravy.

Když nasadíte novou verzi aplikace bez změny, můžete ji navýšit. Směrovač nasadí do vaší existující aplikace funkce a odešle požadavky na staré a nové verze kódu, které jsou směrovány do stejné aplikace funkce.

![Směrování aplikace (bez změny přerušení)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Když nasadíte novou verzi aplikace s nejnovější změnou, můžete zintálčit hlavní nebo dílčí verzi. Pak směrovač aplikace vytvoří novou aplikaci funkcí v Azure, nasadí se do ní a směruje požadavky na novou verzi vaší aplikace. V následujícím diagramu běží orchestrations na verzi 1.0.1 aplikace stále běží, ale požadavky na verzi 1.1.0 jsou směrovány do nové aplikace funkce.

![Směrování aplikace (změna přerušení)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Směrovač monitoruje stav orchestraations ve verzi 1.0.1 a odebere aplikace po dokončení všech orchestrací. 

### <a name="tracking-store-settings"></a>Sledování nastavení obchodu

Každá aplikace funkce by měla používat samostatné plánovací fronty, případně v samostatných účtech úložiště. Pokud chcete dotazovat všechny instance orchestrations ve všech verzích vaší aplikace, můžete sdílet tabulky instancí a historie v aplikacích funkcí. Tabulky můžete sdílet konfigurací `trackingStoreConnectionStringName` `trackingStoreNamePrefix` nastavení a v souboru [nastavení host.json](durable-functions-bindings.md#host-json) tak, aby všechny používaly stejné hodnoty.

Další informace najdete [v tématu Správa instancí v trvalé funkce v Azure](durable-functions-instance-management.md).

![Sledování nastavení obchodu](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa verzí Odolná funkce](durable-functions-versioning.md)

