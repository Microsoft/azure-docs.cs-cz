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
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706507"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost a zotavení po havárii

Konfigurace aplikací pro Azure v současné době je místní služba. Každé úložiště konfigurace se vytvoří v konkrétní oblasti Azure. K výpadku celé oblasti má vliv na všechna úložiště v dané oblasti. Konfigurace aplikace nenabízí automatické převzetí služeb při selhání do jiné oblasti. Tento článek poskytuje obecné pokyny k použití více úložišť konfigurací v různých oblastech Azure pro zvýšení geo odolnosti vaší aplikace.

## <a name="high-availability-architecture"></a>Architektura pro vysokou dostupnost

Jak začít využívat redundance mezi oblastmi, je potřeba vytvořit několik úložišť pro konfiguraci aplikací v různých oblastech. S tímto nastavením aplikace má alespoň jeden další konfiguraci úložiště, aby pokud primární úložiště přestane být přístupný. Následující diagram znázorňuje topologii mezi vaší aplikací a jeho konfiguraci primárního a sekundárního úložiště:

![Geograficky redundantní úložiště](./media/geo-redundant-app-configuration-stores.png)

Aplikace načte konfiguraci z primárního a sekundárního úložiště paralelně. To zvýší pravděpodobnost úspěšně získat konfigurační data. Jste odpovídá za pořízení data v obou úložiště synchronizované. Následující části popisují, jak se dají vytvářet geo-odolnost proti chybám do aplikace.

## <a name="failover-between-configuration-stores"></a>Převzetí služeb při selhání mezi úložišti konfigurace

Aplikace není technicky, provádění převzetí služeb při selhání. Se pokouší načíst stejnou sadu konfiguračních dat ze dvou úložišť konfigurace aplikace současně. Uspořádejte váš kód tak, aby nejdřív načte ze sekundárního úložiště a pak primární úložiště. Tento přístup zajišťuje, že konfigurační data ve primárního úložiště má přednost před pokaždé, když je k dispozici. Následující fragment kódu ukazuje, jak můžete implementovat toto uspořádání v rozhraní příkazového řádku .NET Core:

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

Všimněte si, že `optional` parametr předán `AddAzureAppConfiguration` funkce. Pokud je nastavena na `true`, tento parametr zabrání aplikaci v nemůže pokračovat, pokud funkci nelze načíst konfigurační data.

## <a name="synchronization-between-configuration-stores"></a>Synchronizace mezi úložišti konfigurace

Je důležité, že svá všechny konfigurace geograficky redundantní úložiště mají stejnou sadu data. Můžete použít **exportovat** funkce v konfiguraci aplikací ke kopírování dat z primárního úložiště do sekundární lokality na vyžádání. Tato funkce je dostupná prostřednictvím webu Azure portal a rozhraní příkazového řádku.

Na webu Azure Portal můžete vložit nějakou změnu jiné konfigurace úložiště pomocí následujících kroků.

1. Přejděte na **Import/Export** kartu a vyberte **exportovat** > **konfigurace aplikace** > **cílové**  >  **Vyberte prostředek**.

2. V novém okně, které se otevře, zadejte předplatné, skupinu prostředků a název prostředku sekundárního úložiště a pak vyberte **použít**.

3. Uživatelské rozhraní je aktualizován, takže můžete zvolit, jaké konfiguračních dat, kterou chcete exportovat do sekundárního úložiště. Můžete ponechat výchozí hodnotu čas a nastavte parametry **z popisku** a **popisku** na stejnou hodnotu. Vyberte **Použít**.

4. Opakujte předchozí kroky pro všechny změny konfigurace.

K automatizaci tohoto procesu exportu, pomocí Azure CLI. Následující příkaz ukazuje, jak exportovat změny konfigurací jedné z primárního úložiště do sekundární:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak posílit vaše aplikace, abyste dosáhli geograficky odolnost proti chybám za běhu pro konfiguraci aplikací. Můžete také vložit konfigurační data z konfigurace aplikace v době sestavení nebo nasazení. Další informace najdete v tématu [integrací se službami kanálu CI/CD](./integrate-ci-cd-pipeline.md).

