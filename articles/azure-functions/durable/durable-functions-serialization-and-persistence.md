---
title: Trvalost a serializace dat v Durable Functions – Azure
description: Informace o tom, jak rozšíření Durable Functions pro Azure Functions uchovává data
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: ea4aaa1cdbe10e2db9cf619452558d104a2293ab
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449369"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Trvalost a serializace dat v Durable Functions (Azure Functions)

Durable Functions automaticky ukládá parametry funkce, návratové hodnoty a další stav do trvalého back-endu, aby bylo možné zajistit spolehlivé spuštění. Množství a četnost dat trvalých do trvalého úložiště však mohou ovlivnit náklady na výkon aplikace a transakce úložiště. V závislosti na typu dat, která vaše aplikace ukládá, může být potřeba zvážit taky zásady uchovávání dat a ochrany osobních údajů.

## <a name="azure-storage"></a>Azure Storage

Ve výchozím nastavení Durable Functions ukládá data do front, tabulek a objektů BLOB v [Azure Storagem](https://azure.microsoft.com/services/storage/) účtu, který zadáte.

### <a name="queues"></a>Fronty

Durable Functions používá Azure Storage fronty k spolehlivému naplánování všech spuštění funkcí. Tyto zprávy fronty obsahují vstupy a výstupy funkcí v závislosti na tom, zda je zpráva používána k naplánování spuštění nebo vrácení hodnoty zpět do volající funkce. Tyto zprávy front obsahují také další metadata, která Durable Functions používá pro interní účely, jako je směrování a ucelená korelace. Po dokončení funkce v reakci na přijatou zprávu je tato zpráva odstraněna a výsledek spuštění může být také trvale uložen buď na Azure Storage tabulky, nebo na objekty Azure Storage BLOB.

V rámci jednoho [centra úloh](durable-functions-task-hubs.md)Durable Functions vytvoří a přidá zprávy do fronty *pracovních položek* s názvem `<taskhub>-workitem` pro funkce plánování aktivit a jedné nebo více *řídicích front* s názvem `<taskhub>-control-##` k naplánování nebo obnovení funkcí Orchestrator a entit. Počet front řízení se rovná počtu oddílů nakonfigurovaných pro vaši aplikaci. Další informace o frontách a oddílech najdete v [dokumentaci ke službě Performance and škálovatelnost](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Tabulky

Jakmile orchestrace úspěšně zpracuje zprávy, záznamy jejich výsledných akcí jsou trvale uložené v tabulce *Historie* s názvem `<taskhub>History` . Vstupy a výstupy Orchestrace a vlastní stavová data jsou také trvale zachovaná v tabulce *Instances* s názvem `<taskhub>Instances` .

### <a name="blobs"></a>Objekty blob

Ve většině případů Durable Functions nepoužívá objekty blob Azure Storage k uchování dat. Fronty a tabulky ale mají [omezení velikosti](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) , která můžou zabránit Durable Functions uchování všech požadovaných dat do řádku úložiště nebo do zprávy fronty. Například pokud je část dat, která musí být uložena do fronty, větší než 45 KB při serializaci, Durable Functions komprimuje data a uloží je do objektu BLOB. Pokud tímto způsobem trvalá data do úložiště objektů blob, trvalá funkce ukládá odkaz na tento objekt blob do řádku tabulky nebo zprávy ve frontě. Když Durable Functions potřebuje načíst data, automaticky se načte z objektu BLOB. Tyto objekty BLOB se ukládají do kontejneru objektů BLOB `<taskhub>-largemessages` .

> [!NOTE]
> Dodatečné kroky komprese a operace BLOB pro velké zprávy můžou být náročné na náklady na procesor a vstupně-výstupní latenci. Kromě toho Durable Functions potřebuje načíst trvalá data v paměti a může tak učinit i pro mnoho různých spuštění funkcí ve stejnou dobu. Výsledkem je, že zachování velkých datových vytížení může také způsobit vysoké využití paměti. Abyste minimalizovali nároky na paměť, zvažte ruční uchování velkých datových částí (například v úložišti objektů BLOB) a místo toho předejte odkazy na tato data. Tímto způsobem může váš kód načíst data pouze v případě potřeby, aby nedocházelo k nadbytečnému zatížení během [opakovaného přehrávání funkcí nástroje Orchestrator](durable-functions-orchestrations.md#reliability). Ukládání datových částí na disk se ale *nedoporučuje,* protože stav na disku není zaručený, protože funkce se můžou spouštět na různých virtuálních počítačích po celou dobu jejich životností.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Typy serializovaných a trvalých dat
Následuje seznam různých typů dat, které se budou serializovat a uchovávat při používání funkcí Durable Functions:

- Všechny vstupy a výstupy funkcí Orchestrator, Activity a entity, včetně všech ID a neošetřených výjimek
- Názvy funkcí Orchestrator, Activity a entity
- Externí názvy událostí a datové části
- Vlastní stavová datová část stavu orchestrace
- Ukončení orchestrace – zprávy
- Odolné datové části časovače
- Trvalá žádost HTTP a adresy URL odpovědí, hlavičky a datové části
- Volání entit a datové části signálu
- Datová část stavu entity

### <a name="working-with-sensitive-data"></a>Práce s citlivými daty
Při použití Azure Storage jsou všechna data automaticky šifrována v klidovém stavu. Kdokoli, kdo má přístup k účtu úložiště, ale může data přečíst v nešifrované podobě. Pokud potřebujete silnější ochranu citlivých dat, zvažte nejprve šifrování dat pomocí vlastních šifrovacích klíčů, aby Durable Functions v předem zašifrovaném tvaru dál používala data.

Alternativně mají uživatelé rozhraní .NET možnost implementovat vlastní zprostředkovatele serializace, kteří zajišťují automatické šifrování. Příklad vlastní serializace se šifrováním najdete v [této ukázce GitHubu](https://github.com/charleszipp/azure-durable-entities-encryption).

> [!NOTE]
> Pokud se rozhodnete implementovat šifrování na úrovni aplikace, uvědomte si, že Orchestrace a entity můžou existovat po neomezenou dobu. V takovém případě je čas k otočení šifrovacích klíčů, protože orchestrace nebo entity můžou běžet déle než vaše zásady pro rotaci klíčů. Pokud dojde k rotaci klíčů, klíč používaný k šifrování vašich dat už nemusí být k dispozici, aby ho dešifroval při příštím spuštění vaší orchestrace nebo subjektu. Šifrování zákazníků se proto doporučuje jenom v případě, že se očekává, že se Orchestrace a entity spouštějí v poměrně krátkých časových obdobích.

## <a name="customizing-serialization-and-deserialization"></a>Přizpůsobení serializace a deserializace

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Výchozí logika serializace

Durable Functions interně používá [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) k serializaci Orchestrace a dat entity do formátu JSON. Výchozí nastavení Durable Functions použití pro Json.NET jsou:

**Vstupy, výstupy a stavy:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Výjimek**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Přečtěte si podrobnější dokumentaci k tomuto `JsonSerializerSettings` [tématu](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Přizpůsobení serializace pomocí atributů rozhraní .NET

Při serializaci dat Json.NET vyhledá [různé atributy](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) tříd a vlastností, které řídí způsob, jakým jsou data serializována a deserializována z formátu JSON. Pokud vlastníte zdrojový kód pro datový typ předaný do Durable Functions rozhraní API, zvažte přidání těchto atributů do typu pro přizpůsobení serializace a deserializace.

## <a name="customizing-serialization-with-dependency-injection"></a>Přizpůsobení serializace pomocí injektáže závislosti

Aplikace Function App, které cílí na .NET a běží na modulu runtime Functions v3, můžou použít [vkládání závislostí (di)](../functions-dotnet-dependency-injection.md) k přizpůsobení způsobu serializace dat a výjimek. Vzorový kód ukazuje, jak použít DI k přepsání výchozího nastavení serializace Json.NET pomocí vlastních implementací `IMessageSerializerSettingsFactory` `IErrorSerializerSettingsFactory` rozhraní a.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Logika serializace a deserializace

Aplikace Azure Functions Node používají [ `JSON.stringify()` pro serializaci](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) a [ `JSON.Parse()` deserializaci](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). Většina typů by měla plynule serializovat a deserializovat. V případech, kdy výchozí logika není dostačující, definování `toJSON()` metody objektu způsobí zneužití logiky serializace. Nicméně pro deserializaci objektu neexistují žádné analogové.

Pro úplné přizpůsobení kanálu serializace/deserializace zvažte zpracování serializace a deserializace pomocí vlastního kódu a předávání dat jako řetězce.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Logika serializace a deserializace

Důrazně doporučujeme použít anotace typu k zajištění, Durable Functions serializace a deserializace vaše data správně. I když je řada vestavěných typů zpracovávána automaticky, některé předdefinované datové typy vyžadují anotace typu k zachování typu během deserializace.

Pro vlastní datové typy musíte definovat serializaci JSON a deserializaci datového typu exportováním statické `to_json` a `from_json` metody z vaší třídy.

---
