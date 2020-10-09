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
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792012"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Povolit protokolování s nastavením diagnostiky

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

2. Vyberte **nastavení diagnostiky**.

3. Vyberte **zapnout diagnostiku**.

   ![Zapnout diagnostiku](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Zadejte název pro nastavení diagnostiky.

5. Vyberte, kam chcete protokoly odeslat. Můžete vybrat libovolnou kombinaci tří možností:

   * Archivovat v účtu úložiště
   * Streamovat do centra událostí
   * Odeslání do Log Analytics

6. Vyberte operace, které chcete monitorovat, a pro tyto operace povolte protokoly. Operace, které nastavení diagnostiky mohou hlásit:

   * Připojení
   * Telemetrie zařízení
   * Zprávy z cloudu na zařízení
   * Operace identity zařízení
   * Nahrání souborů
   * Směrování zpráv
   * Zdvojené operace z cloudu na zařízení
   * Zdvojené operace mezi zařízeními a cloudem
   * Zdvojené operace
   * Operace úlohy
   * Přímé metody  
   * Distribuované trasování (Preview)
   * Konfigurace
   * Datové proudy zařízení
   * Metriky zařízení

6. Uložte nová nastavení. 

Pokud chcete zapnout nastavení diagnostiky pomocí PowerShellu, použijte následující kód:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nové nastavení se projeví přibližně po dobu 10 minut. Pak se protokoly zobrazí v okně **nastavení diagnostiky** v nakonfigurovaném cíli archivace. Další informace o konfiguraci diagnostiky najdete v tématu [shromažďování a využívání dat protokolů z prostředků Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
