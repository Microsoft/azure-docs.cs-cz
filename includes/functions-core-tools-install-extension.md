---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131412"
---
Ujistěte se, všechny vazby publikované týmem Azure Functions k dispozici prostřednictvím nastavení v rozšíření sady *host.json* souboru. Pro místní vývoj, zajistěte, abyste měli nejnovější verzi [nástrojů Azure Functions Core](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Použití rozšíření sady, aktualizujte *host.json* souboru přidejte následující položku pro `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id` Vlastnost odkazuje na obor názvů pro rozšíření sady Microsoft Azure Functions.
- `version` Odkazuje na verzi sady.

Zvýšení verze sady prostředků jako balíčky v sadě změn. Hlavní verze změny dojít pouze při balíčky v sadě přesuňte hlavní verze. `version` Používá vlastnost [notation interval pro zadání rozsahu verzí](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Modul runtime služby Functions vždy vybere maximální povolenou verzi definovaný rozsah verzí nebo intervalu.

Když odkazujete sady rozšíření ve vašem projektu, všechny výchozí vazby jsou k dispozici vaše funkce. K dispozici v vazby [rozšíření sady](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) jsou:

|Balíček  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|