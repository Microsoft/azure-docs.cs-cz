---
title: Nasazení s nulovou dobou výpadku pro Durable Functions
description: Naučte se, jak povolit orchestraci Durable Functions pro nasazení s nulovými výpadky.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 2c96f2cc37c47c77b82ca86d5fd0295f0c66a896
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009479"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Nasazení s nulovou dobou výpadku pro Durable Functions

[Model spolehlivého spuštění](./durable-functions-orchestrations.md) Durable Functions vyžaduje, aby orchestrace byly deterministické, což vytvoří další výzvu, která se při nasazení aktualizací zváží. Pokud nasazení obsahuje změny signatur funkcí aktivity nebo logiky nástroje Orchestrator, instance orchestrace v rámci letu selžou. Tato situace je obzvláště problémem pro instance dlouhotrvajících orchestrací, což může představovat hodiny nebo dny v práci.

Aby nedošlo k těmto selháním, máte dvě možnosti: 
- Nastavte zpoždění nasazení, dokud se nedokončí všechny spuštěné instance orchestrace.
- Zajistěte, aby všechny spuštěné instance orchestrace používaly stávající verze vašich funkcí. 

Následující graf porovnává tři hlavní strategie, abyste dosáhli nasazení s nulovými výpadky pro Durable Functions: 

| Strategie |  Kdy je použít | Výhody | Nevýhody |
| -------- | ------------ | ---- | ---- |
| [Správa verzí](#versioning) |  Aplikace, u kterých nedochází k častým [změnám.](durable-functions-versioning.md) | Jednoduchá implementace. |  Zvýšení velikosti aplikace funkcí v paměti a počtu funkcí<br/>Duplikace kódu. |
| [Stavová kontrolu pomocí slotu](#status-check-with-slot) | Systém, který nemá dlouhodobě běžící orchestraci trvající více než 24 hodin nebo často překrývající orchestrace. | Základ jednoduchého kódu.<br/>Nevyžaduje další správu aplikací Function App. | Vyžaduje další účet úložiště nebo správu centra úloh.<br/>Vyžaduje časové období, kdy nejsou spuštěny žádné orchestrace. |
| [Směrování aplikace](#application-routing) | Systém, který nemá časová období, pokud orchestrace nejsou spuštěny, například tato časová období s orchestrací, která byla za posledních více než 24 hodin nebo často překrývající orchestrace. | Zpracovává nové verze systémů s nepřetržitým spouštěním orchestrace, které mají zásadní změny. | Vyžaduje inteligentní směrovač aplikace.<br/>Může se jednat o maximální počet aplikací Function App povolených vaším předplatným. Výchozí hodnota je 100. |

## <a name="versioning"></a>Správa verzí

Definujte nové verze vašich funkcí a ve své aplikaci Function App ponechte staré verze. Jak vidíte v diagramu, verze funkce se bude součástí svého názvu. Vzhledem k tomu, že předchozí verze funkcí jsou zachovány, mohou instance orchestrace v letadle i nadále na ně odkazovat. Mezitím požadavky na nové instance orchestrace volají na nejnovější verzi, kterou může vaše klientská funkce Orchestration odkazovat z nastavení aplikace.

![Strategie správy verzí](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

V této strategii je nutné zkopírovat každou funkci a její odkazy na jiné funkce musí být aktualizovány. To můžete usnadnit psaním skriptu. Tady je [ukázkový projekt](https://github.com/TsuyoshiUshio/DurableVersioning) se skriptem migrace.

>[!NOTE]
>Tato strategie využívá sloty nasazení, aby nedocházelo k výpadkům během nasazení. Podrobnější informace o tom, jak vytvořit a používat nové sloty nasazení, najdete v tématu [Azure Functions sloty nasazení](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Stavová kontrolu pomocí slotu

Zatímco aktuální verze vaší aplikace Function App běží v produkčním slotu, nasaďte novou verzi aplikace Function App do přípravného slotu. Než provedete prohození produkčních a přípravných slotů, zkontrolujte, jestli nejsou spuštěné instance orchestrace. Po dokončení všech instancí orchestrace můžete provést prohození. Tato strategie funguje, když máte předvídatelné doby, kdy se v letu neúčtují žádné instance orchestrace. Toto je nejlepší přístup, pokud vaše orchestrace nejsou dlouhotrvající a když se vaše orchestrace nemění často.

### <a name="function-app-configuration"></a>Konfigurace aplikace Function App

Tento scénář nastavíte pomocí následujícího postupu.

1. [Přidejte sloty nasazení](../functions-deployment-slots.md#add-a-slot) do aplikace Function App pro přípravu a výrobu.

1. U každého slotu nastavte [nastavení aplikace AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) na připojovací řetězec sdíleného účtu úložiště. Tento připojovací řetězec účtu úložiště používá modul runtime Azure Functions. Tento účet je používán modulem runtime Azure Functions a spravuje klíče funkce.

1. Pro každou slot vytvořte nové nastavení aplikace, například `DurableManagementStorage` . Nastavte jeho hodnotu na připojovací řetězec různých účtů úložiště. Tyto účty úložiště používá rozšíření Durable Functions pro [spolehlivé provádění](./durable-functions-orchestrations.md). Pro jednotlivé sloty použijte samostatný účet úložiště. Toto nastavení neoznačujte jako nastavení slotu nasazení.

1. V částihost.jsaplikace Function App [ na durableTask souboru](durable-functions-bindings.md#hostjson-settings)zadejte `connectionStringName` (odolné 2. x) nebo `azureStorageConnectionStringName` (odolné 1. x) jako název nastavení aplikace, které jste vytvořili v kroku 3.

Následující diagram znázorňuje popsanou konfiguraci slotů pro nasazení a účtů úložiště. V tomto potenciálním scénáři přednasazení je verze 2 aplikace Function App spuštěná v produkčním slotu, zatímco verze 1 zůstává v přípravném slotu.

![Sloty nasazení a účty úložiště](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.jsv příkladech

Následující fragmenty JSON jsou příklady nastavení připojovacího řetězce v *host.jsv* souboru.

#### <a name="functions-20"></a>Funkce 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
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

Nakonfigurujte svůj kanál CI/CD tak, aby se nasadil jenom v případě, že vaše aplikace Function App nemá žádné nedokončené nebo spuštěné instance orchestrace. Pokud používáte Azure Pipelines, můžete vytvořit funkci, která kontroluje tyto podmínky, jako v následujícím příkladu:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Dále nakonfigurujte pracovní bránu tak, aby čekala na neběhu žádné orchestrace. Další informace najdete v tématu [Správa nasazení vydaných verzí pomocí bran](/azure/devops/pipelines/release/approvals/gates?view=azure-devops) .

![Brána nasazení](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines kontroluje, jestli aplikace Function App běží na instancích orchestrace před zahájením nasazení.

![Brána nasazení (spuštěná)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Teď je potřeba nasadit novou verzi vaší aplikace Function App do přípravného slotu.

![Přípravný slot](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Nakonec Proměňte sloty. 

Nastavení aplikace, která nejsou označená jako nastavení slotu nasazení, se taky mění, takže aplikace verze 2 udržuje odkaz na účet úložiště A. Vzhledem k tomu, že je stav orchestrace sledován v účtu úložiště, všechny orchestrace běžící v aplikaci verze 2 i nadále běží na nové pozici bez přerušení.

![Slot nasazení](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Chcete-li pro oba sloty použít stejný účet úložiště, můžete změnit názvy Center úloh. V takovém případě musíte spravovat stav vašich slotů a nastavení HubName vaší aplikace. Další informace najdete v tématu [centra úloh v Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Směrování aplikace

Tato strategie je nejsložitější. Dá se ale použít pro aplikace Function App, které nemají čas mezi běžícími orchestrací.

Pro tuto strategii musíte před Durable Functions vytvořit *směrovač aplikace* . Tento směrovač se dá implementovat s Durable Functions. Směrovač má odpovědnost za:

* Nasaďte aplikaci Function App.
* Správa verze Durable Functions. 
* Žádosti orchestrace směrování na aplikace Function App.

Při prvním přijetí žádosti o orchestraci provede směrovač následující úlohy:

1. Vytvoří novou aplikaci Function App v Azure.
2. Nasadí kód aplikace Function App do nové aplikace Function App v Azure.
3. Předá požadavek orchestrace do nové aplikace.

Směrovač spravuje stav, ve kterém je nasazená verze kódu vaší aplikace, do které aplikace Function App v Azure pracuje.

![Směrování aplikací (první čas)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Směrovač nasměruje požadavky na nasazení a orchestraci na příslušnou aplikaci Function App na základě verze odeslané s požadavkem. Ignoruje verzi opravy.

Když nasadíte novou verzi aplikace bez zásadní změny, můžete zvýšit verzi opravy. Směrovač se nasadí do vaší stávající aplikace Function App a pošle žádosti o starou a novou verzi kódu, které jsou směrované do stejné aplikace Function App.

![Směrování aplikace (beze změny)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Když nasadíte novou verzi aplikace s zásadní změnou, můžete zvýšit hlavní nebo dílčí verzi. Pak směrovač aplikace vytvoří novou aplikaci Function App v Azure, nasadí ji do IT a směruje požadavky na novou verzi vaší aplikace. V následujícím diagramu jsou spuštěné orchestrace v 1.0.1 verzi aplikace i nadále spuštěné, ale požadavky na verzi 1.1.0 se směrují do nové aplikace Function App.

![Směrování aplikace (Průlomová změna)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Směrovač monitoruje stav orchestrací ve verzi 1.0.1 a odebírá aplikace po dokončení všech orchestrací. 

### <a name="tracking-store-settings"></a>Nastavení úložiště sledování

Každá aplikace Function app by měla používat samostatné fronty plánování, případně samostatné účty úložiště. Pokud chcete zadat dotaz na všechny instance orchestrace napříč všemi verzemi vaší aplikace, můžete sdílet tabulky instancí a historie napříč vašimi aplikacemi Function App. Tabulky můžete sdílet nakonfigurováním `trackingStoreConnectionStringName` `trackingStoreNamePrefix` nastavení a v souboru [host.jsv nastavení](durable-functions-bindings.md#host-json) tak, aby všechny používaly stejné hodnoty.

Další informace najdete v tématu [Správa instancí v Durable Functions v Azure](durable-functions-instance-management.md).

![Nastavení úložiště sledování](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Durable Functions správy verzí](durable-functions-versioning.md)
