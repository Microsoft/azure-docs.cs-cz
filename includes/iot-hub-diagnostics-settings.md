---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 3778ec2fac13aee29ce361402a535ca70fd56c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75750707"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Povolení protokolování pomocí nastavení diagnostiky

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do svého centra IoT hub.

2. Vyberte **Nastavení diagnostiky**.

3. Vyberte **možnost Zapnout diagnostiku**.

   ![Zapnout diagnostiku](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Pojmenujte diagnostická nastavení.

5. Zvolte, kam chcete protokoly odeslat. Můžete vybrat libovolnou kombinaci tří možností:

   * Archivovat v účtu úložiště
   * Streamovat do centra událostí
   * Odeslání do Log Analytics

6. Zvolte, které operace chcete sledovat, a povolte protokoly pro tyto operace. Operace, které mohou hlásit nastavení diagnostiky, jsou:

   * Připojení
   * Telemetrie zařízení
   * Zprávy z cloudu na zařízení
   * Operace identity zařízení
   * Nahrání souborů
   * Směrování zpráv
   * Operace s dvojčaty cloud-to-device
   * Operace dvojčete zařízení cloudu
   * Dvojité operace
   * Operace práce
   * Přímé metody  
   * Distribuované trasování (náhled)
   * Konfigurace
   * Datové proudy zařízení
   * Metriky zařízení

6. Uložte nové nastavení. 

Pokud chcete zapnout nastavení diagnostiky pomocí PowerShellu, použijte následující kód:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nové nastavení se projeví asi za 10 minut. Poté se protokoly zobrazí v nakonfigurovaném cíli archivace v okně **Nastavení diagnostiky.** Další informace o konfiguraci diagnostiky najdete v [tématu Shromažďování a využívání dat protokolu z prostředků Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
