---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0039c4eff3571a96cba1ab36136e0a588d78eb75
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159124"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Povolit protokolování pomocí nastavení diagnostiky

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT Hub.

2. Vyberte **nastavení diagnostiky**.

3. Vyberte **zapnout diagnostiku**.

   ![Zapnout diagnostiku](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Zadejte název nastavení diagnostiky.

5. Zvolte ve které chcete odeslat protokoly. Můžete vybrat libovolnou kombinaci těchto tří možností:

   * Archivovat v účtu úložiště
   * Streamovat do centra událostí
   * Odeslání do Log Analytics

6. Vyberte operace, které chcete monitorovat a povolení protokolů pro tyto operace. Operace, které může podávat nastavení diagnostiky se:

   * Připojení
   * Telemetrie zařízení
   * Zprávy typu cloud zařízení
   * Operace identity zařízení
   * Nahrání souborů
   * Směrování zpráv
   * Operace dvojčete typu cloud zařízení
   * Operace dvojčete zařízení cloud
   * Operace dvojčete
   * Operace úlohy
   * Přímé metody  

6. Uložte nové nastavení. 

Pokud chcete zapnout nastavení diagnostiky pomocí Powershellu, použijte následující kód:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nové nastavení se projeví během 10 minut. Potom protokolů se objeví v nakonfigurovaných archivace cílové na **nastavení diagnostiky** okno. Další informace o konfiguraci diagnostiky najdete v tématu [shromažďovat a zpracovávat data protokolu z vašich prostředků azure](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).