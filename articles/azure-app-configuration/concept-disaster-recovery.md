---
title: Odolnost konfigurace aplikace Azure a zotavení po havárii | Microsoft Docs
description: Přehled toho, jak implementovat odolnost a zotavení po havárii s využitím konfigurace aplikací Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: 291f6fe48d81397d293ab54a73e777831e25f6ea
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185277"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost a zotavení po havárii

V současné době je konfigurace aplikace Azure místní službou. Každé úložiště konfigurace se vytvoří v konkrétní oblasti Azure. Výpadek celého regionu ovlivňuje všechna úložiště v této oblasti. Konfigurace aplikace nenabízí automatické převzetí služeb při selhání do jiné oblasti. Tento článek obsahuje obecné pokyny k používání více úložišť konfigurací napříč oblastmi Azure ke zvýšení geografické odolnosti vaší aplikace.

## <a name="high-availability-architecture"></a>Architektura s vysokou dostupností

Pokud chcete realizovat redundanci mezi oblastmi, musíte v různých oblastech vytvořit několik úložišť konfigurací aplikace. V rámci tohoto nastavení má vaše aplikace ještě aspoň jedno další úložiště konfigurace, aby se mohlo vrátit, pokud se primární úložiště nezpřístupní. Následující diagram znázorňuje topologii mezi vaší aplikací a jejich primárními a sekundárními úložišti konfigurací:

![Geograficky redundantní obchody](./media/geo-redundant-app-configuration-stores.png)

Vaše aplikace načte svou konfiguraci z primárního i sekundárního úložiště současně. Tím se zvyšuje riziko úspěšného získání konfiguračních dat. Zodpovídáte za udržování dat v obou úložištích. V následujících částech se dozvíte, jak můžete do aplikace sestavit geografickou odolnost.

## <a name="failover-between-configuration-stores"></a>Převzetí služeb při selhání mezi úložišti konfigurací

Technicky, vaše aplikace neprovádí převzetí služeb při selhání. Probíhá pokus o načtení stejné sady konfiguračních dat ze dvou úložišť konfigurací aplikace současně. Uspořádejte svůj kód tak, aby nejprve načítal ze sekundárního úložiště a pak jako primární úložiště. Tento přístup zajišťuje, že konfigurační data v primárním úložišti mají přednost, kdykoli bude k dispozici. Následující fragment kódu ukazuje, jak můžete implementovat toto uspořádání do .NET Core CLI:

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
    }
```

Všimněte si, že parametr `optional` předaný do funkce `AddAzureAppConfiguration`. Když se nastaví na `true`, tento parametr brání aplikaci v neúspěšném pokračování, pokud funkce nemůže načíst konfigurační data.

## <a name="synchronization-between-configuration-stores"></a>Synchronizace mezi úložišti konfigurací

Je důležité, aby úložiště geograficky redundantních konfigurací měla stejnou sadu dat. Pomocí funkce **exportu** v konfiguraci aplikace můžete kopírovat data z primárního úložiště na sekundární na vyžádání. Tato funkce je k dispozici prostřednictvím Azure Portal i v rozhraní příkazového řádku.

Z Azure Portal můžete vložit změnu do jiného úložiště konfigurace pomocí následujících kroků.

1. Otevřete kartu **Import/export** a vyberte **exportovat** > **Konfigurace aplikace** > **cíli** > **Vybrat prostředek**.

2. V novém okně, které se otevře, zadejte předplatné, skupinu prostředků a název prostředku sekundárního úložiště a pak vyberte **použít**.

3. Uživatelské rozhraní je aktualizované, abyste si mohli vybrat, jaká konfigurační data chcete exportovat do sekundárního úložiště. Můžete ponechat výchozí hodnotu čas jako je a nastavit jak **z popisku** , tak na **popisek** na stejnou hodnotu. Vyberte **Použít**.

4. Předchozí kroky opakujte pro všechny změny konfigurace.

K automatizaci tohoto procesu exportu použijte rozhraní příkazového řádku Azure. Následující příkaz ukazuje, jak exportovat jednu změnu konfigurace z primárního úložiště do sekundárního:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak rozšířit svou aplikaci tak, aby při konfiguraci aplikace dosáhla geografické odolnosti za běhu. Můžete také vložit konfigurační data z konfigurace aplikace v době sestavení nebo nasazení. Další informace najdete v tématu [integrace s kanálem CI/CD](./integrate-ci-cd-pipeline.md).

