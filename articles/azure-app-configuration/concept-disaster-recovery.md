---
title: Odolnost konfigurace aplikace Azure a zotavení po havárii
description: Vyštíhlá implementace odolnosti a zotavení po havárii s využitím konfigurace aplikací Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 5c62f10d67345d68cde27af7d0a7663b22d978a0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002421"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost a zotavení po havárii

V současné době je konfigurace aplikace Azure místní službou. Každé úložiště konfigurace se vytvoří v konkrétní oblasti Azure. Výpadek celého regionu ovlivňuje všechna úložiště v této oblasti. Konfigurace aplikace nenabízí automatické převzetí služeb při selhání do jiné oblasti. Tento článek obsahuje obecné pokyny k používání více úložišť konfigurací napříč oblastmi Azure ke zvýšení geografické odolnosti vaší aplikace.

## <a name="high-availability-architecture"></a>Architektura s vysokou dostupností

Pokud chcete realizovat redundanci mezi oblastmi, musíte v různých oblastech vytvořit několik úložišť konfigurací aplikace. V rámci tohoto nastavení má vaše aplikace ještě aspoň jedno další úložiště konfigurace, aby se mohlo vrátit, pokud se primární úložiště nezpřístupní. Následující diagram znázorňuje topologii mezi vaší aplikací a jejich primárními a sekundárními úložišti konfigurací:

![Geograficky redundantní obchody](./media/geo-redundant-app-configuration-stores.png)

Vaše aplikace načte svou konfiguraci z primárního i sekundárního úložiště současně. Tím se zvyšuje riziko úspěšného získání konfiguračních dat. Zodpovídáte za udržování dat v obou úložištích. V následujících částech se dozvíte, jak můžete do aplikace sestavit geografickou odolnost.

## <a name="failover-between-configuration-stores"></a>Převzetí služeb při selhání mezi úložišti konfigurací

Technicky, vaše aplikace neprovádí převzetí služeb při selhání. Probíhá pokus o načtení stejné sady konfiguračních dat ze dvou úložišť konfigurací aplikace současně. Uspořádejte svůj kód tak, aby nejprve načítal ze sekundárního úložiště a pak jako primární úložiště. Tento přístup zajišťuje, že konfigurační data v primárním úložišti mají přednost, kdykoli bude k dispozici. Následující fragment kódu ukazuje, jak můžete implementovat toto uspořádání v .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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

Všimněte si `optional` parametru předaného do `AddAzureAppConfiguration` funkce. Při nastavení na hodnotu `true` Tento parametr brání aplikaci v neúspěšném pokračování, pokud funkce nemůže načíst konfigurační data.

## <a name="synchronization-between-configuration-stores"></a>Synchronizace mezi úložišti konfigurací

Je důležité, aby úložiště geograficky redundantních konfigurací měla stejnou sadu dat. Toho můžete dosáhnout dvěma způsoby:

### <a name="backup-manually-using-the-export-function"></a>Ruční zálohování pomocí funkce exportu

Pomocí funkce **exportu** v konfiguraci aplikace můžete kopírovat data z primárního úložiště na sekundární na vyžádání. Tato funkce je k dispozici prostřednictvím Azure Portal i v rozhraní příkazového řádku.

Z Azure Portal můžete vložit změnu do jiného úložiště konfigurace pomocí následujících kroků.

1. Otevřete kartu **Import/export** a vyberte **exportovat**  >  **Cíl konfigurace aplikace**  >  **Target**  >  **Vybrat prostředek**.

1. V novém okně, které se otevře, zadejte předplatné, skupinu prostředků a název prostředku svého sekundárního úložiště a pak vyberte **použít**.

1. Uživatelské rozhraní je aktualizované, abyste si mohli vybrat, jaká konfigurační data chcete exportovat do sekundárního úložiště. Výchozí hodnotu čas můžete ponechat tak, jak je, a nastavit jak **z popisku** , tak i **popisku** na stejnou hodnotu. Vyberte **Apply** (Použít). Tento postup opakujte pro všechny popisky v primárním úložišti.

1. Předchozí kroky opakujte při každé změně konfigurace.

Proces exportu se dá dosáhnout taky pomocí Azure CLI. Následující příkaz ukazuje, jak exportovat všechny konfigurace z primárního úložiště do sekundárního:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Automatické zálohování pomocí Azure Functions

Proces zálohování můžete automatizovat pomocí Azure Functions. Využívá integraci s Azure Event Grid v konfiguraci aplikace. Po nastavení bude konfigurace aplikace publikovat události, aby se Event Grid všechny změny provedené v úložišti konfigurace na klíčových hodnotách. Proto aplikace Azure Functions může naslouchat těmto událostem a odpovídajícím způsobem zálohovat data. Podrobnosti najdete v tomto kurzu, [Jak automaticky zálohovat úložiště konfigurace aplikací](./howto-backup-config-store.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak rozšířit svou aplikaci tak, aby při konfiguraci aplikace dosáhla geografické odolnosti za běhu. Můžete také vložit konfigurační data z konfigurace aplikace v době sestavení nebo nasazení. Další informace najdete v tématu [integrace s kanálem CI/CD](./integrate-ci-cd-pipeline.md).
