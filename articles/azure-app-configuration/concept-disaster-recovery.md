---
title: Azure App konfigurace odolnosti a zotavení po havárii | Dokumentace Microsoftu
description: Přehled o tom, jak implementovat odolnost proti chybám a zotavení po havárii s konfigurací aplikace Azure.
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
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394024"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost a zotavení po havárii

Konfigurace aplikací pro Azure je aktuálně regionálních služeb. Každé úložiště konfigurace se vytvoří v konkrétní oblasti Azure. K výpadku celé oblasti ovlivní všechna úložiště v dané oblasti. Konfigurace aplikace nenabízí automatické převzetí služeb při selhání do jiné oblasti. Tento článek poskytuje obecné pokyny k použití více úložišť konfigurací v různých oblastech Azure pro zvýšení geo odolnosti vaší aplikace.

## <a name="high-availability-architecture"></a>Architektura pro vysokou dostupnost

Jak začít využívat redundance mezi oblastmi, je potřeba vytvořit několik úložišť pro konfiguraci aplikací v různých oblastech. S tímto nastavením bude aplikace mít aspoň jeden další konfiguraci úložiště, které chcete vrátit zpět na primární storu přestane být přístupný. Níže je diagram, který ilustruje topologii mezi vaší aplikací a jeho konfiguraci primárního a sekundárního úložiště.

![Geograficky redundantní úložiště](./media/geo-redundant-app-configuration-stores.png)

Aplikace načte konfiguraci z primárního a sekundárního úložiště paralelně. To zvyšuje šance úspěšně výrazně získávání konfigurační data. Jste odpovídá za pořízení data v obou úložiště synchronizované. Následující části popisují, jak se dají vytvářet geo-odolnost proti chybám do aplikace.

## <a name="failover-between-configuration-stores"></a>Převzetí služeb při selhání mezi úložišti konfigurace

Technicky aplikace není prováděna převzetí služeb při selhání. Se pokouší načíst stejnou sadu konfigurační data ze dvou konfigurace obchodů s aplikacemi současně. Váš kód by měl uspořádat tak, načte první ze sekundárního úložiště nejprve a potom primární úložiště. Tento přístup se zajistit, že konfigurační data ve primárního úložiště priorita, jsou k dispozici. Následující fragment kódu ukazuje, jak to můžete implementovat v .NET Core.

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

Všimněte si, že `optional` parametr předán `AddAzureAppConfiguration` funkce. Pokud je nastavena na `true`, tento parametr zabrání aplikaci nemůže pokračovat, pokud funkci nelze načíst konfigurační data.

## <a name="synchronization-between-configuration-stores"></a>Synchronizace mezi úložišti konfigurace

Je důležité, že svá všechny konfigurace geograficky redundantní úložiště mají stejnou sadu data. Můžete použít **exportovat** funkce v konfiguraci aplikací ke zkopírování dat z primárního úložiště do sekundární na vyžádání. Tato funkce je dostupná prostřednictvím webu Azure portal a rozhraní příkazového řádku.

Na webu Azure Portal můžete vložit nějakou změnu jiné konfigurace úložiště pomocí následujících kroků:

1. Přejděte do **Import/Export** kartu, vyberte možnost **exportovat**vyberte **konfigurace aplikace** jako **cílové** služby, klikněte na tlačítko  **Vyberte prostředek**.

2. V novém okně otevřel nahoru, určete předplatné, skupinu prostředků a název prostředku sekundárního úložiště a pak klikněte na **použít**.

3. Uživatelské rozhraní bude aktualizován tak, že zvolíte co konfiguračních dat, kterou chcete exportovat do sekundárního úložiště. Můžete ponechat výchozí hodnotu čas a nastavte parametry **z popisku** a **popisek** na stejnou hodnotu. Klikněte na tlačítko **Použít**.

4. Opakujte předchozí postup pro všechny změny konfigurace.

Můžete automatizovat to Export procesu pomocí Azure CLI. Následující příkaz ukazuje, jak exportovat změny konfigurací jedné z primárního úložiště do sekundární.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak posílit vaše aplikace, abyste dosáhli geograficky odolnost proti chybám za běhu pro konfiguraci aplikací. Alternativně můžete vložit konfigurační data z konfigurace aplikace v době sestavení nebo nasazení. Další informace najdete v tématu [integrací se službami kanálu CI/CD](./integrate-ci-cd-pipeline.md).

