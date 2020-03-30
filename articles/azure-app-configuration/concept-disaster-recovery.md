---
title: Odolnost konfigurace aplikace Azure a zotavení po havárii
description: Osviďte se, jak implementovat odolnost proti chybám a zotavení po havárii pomocí konfigurace aplikací Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523760"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost a zotavení po havárii

V současné době Azure App Configuration je místní služba. Každé úložiště konfigurace se vytvoří v určité oblasti Azure. Výpadek v celé oblasti ovlivní všechny obchody v této oblasti. Konfigurace aplikace nenabízí automatické převzetí služeb při selhání do jiné oblasti. Tento článek obsahuje obecné pokyny, jak můžete použít více úložišť konfigurace v rámci oblastí Azure ke zvýšení geografické odolnosti vaší aplikace.

## <a name="high-availability-architecture"></a>Architektura s vysokou dostupností

Chcete-li realizovat redundanci mezi oblastmi, musíte vytvořit více obchodů konfigurace aplikací v různých oblastech. S tímto nastavením aplikace má alespoň jeden další úložiště konfigurace se vrátit zpět, pokud primární úložiště stane nepřístupné. Následující diagram znázorňuje topologii mezi aplikací a její primární a sekundární konfigurační úložiště:

![Geograficky redundantní obchody](./media/geo-redundant-app-configuration-stores.png)

Aplikace načte svou konfiguraci z primárního i sekundárního úložiště paralelně. Tím se zvyšuje pravděpodobnost úspěšného získání konfiguračních dat. Jste zodpovědní za synchronizaci dat v obou obchodech. V následujících částech je vysvětleno, jak můžete do aplikace vytvořit geografickou odolnost.

## <a name="failover-between-configuration-stores"></a>Převzetí služeb při selhání mezi úložišti konfigurace

Technicky vzato vaše aplikace není provádění převzetí služeb při selhání. Pokouší se načíst stejnou sadu konfiguračních dat ze dvou obchodů konfigurace aplikací současně. Uspořádejte kód tak, aby se nejprve načte z sekundárního úložiště a potom z primárního úložiště. Tento přístup zajišťuje, že konfigurační data v primárním úložišti mají přednost vždy, když jsou k dispozici. Následující fragment kódu ukazuje, jak můžete implementovat toto uspořádání v .NET Core:

#### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Všimněte `optional` si, `AddAzureAppConfiguration` že parametr předán do funkce. Pokud je `true`tato možnost nastavena na , zabrání tomu, aby aplikace nepokračovala, pokud funkce nemůže načíst konfigurační data.

## <a name="synchronization-between-configuration-stores"></a>Synchronizace mezi konfiguračními úložišti

Je důležité, aby vaše geograficky redundantní konfigurační úložiště měla stejnou sadu dat. Pomocí funkce **Export** v konfiguraci aplikace můžete kopírovat data z primárního úložiště do sekundárního na vyžádání. Tato funkce je dostupná prostřednictvím portálu Azure a rozhraní příkazového příkazu.

Z webu Azure Portal můžete nabízenou změnu do jiného úložiště konfigurace podle následujících kroků.

1. Přejděte na kartu **Import nebo export** a vyberte **Exportovat** > **cíl** > **konfigurace** > aplikace**Vyberte prostředek**.

1. V novém okně, které se otevře, zadejte předplatné, skupinu prostředků a název prostředku sekundárního úložiště a vyberte **použít**.

1. UI je aktualizován, takže si můžete vybrat, jaká konfigurační data chcete exportovat do sekundárního úložiště. Výchozí hodnotu času můžete ponechat tak, jak je, a nastavit **popisek Od** i **Popisek Na** na stejnou hodnotu. Vyberte **Použít**.

1. Opakujte předchozí kroky pro všechny změny konfigurace.

Chcete-li tento proces exportu automatizovat, použijte příkazové příkazové příkazy Azure. Následující příkaz ukazuje, jak exportovat jednu změnu konfigurace z primárního úložiště do sekundárního:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak rozšířit aplikaci k dosažení geografické odolnosti za běhu pro konfiguraci aplikace. Můžete také vložit konfigurační data z konfigurace aplikace v době sestavení nebo nasazení. Další informace naleznete v [tématu Integrace s kanálem CI/CD](./integrate-ci-cd-pipeline.md).
