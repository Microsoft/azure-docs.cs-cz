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
ms.openlocfilehash: 1c7f006c066a4f1505a642af04a1ef027fde0a44
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34666938"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Povolení protokolování se nastavení diagnostiky

1. Přihlaste se k [portál Azure] [ lnk-portal] a přejděte do služby IoT Hub.
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapněte diagnostiku**.

   ![Zapnout diagnostiku][1]

1. Zadejte nastavení pro diagnostiku název.
1. Vyberte, kam chcete odeslat protokoly. Můžete vybrat libovolnou kombinaci těchto tří možností:
   * Archivovat v účtu úložiště
   * Streamovat do centra událostí
   * Odeslání do Log Analytics
1. Zvolte operací, které chcete monitorovat a povolení protokolů pro tyto operace. Operace, které mohou vykazovat nastavení diagnostiky jsou:
   * Připojení
   * Zařízení telemetrie
   * Zprávy typu cloud zařízení
   * Operace identity zařízení
   * Nahrávání souborů
   * Směrování zpráv
   * Operace dvojče cloud zařízení
   * Operace twin zařízení cloud
   * Operace Twin
   * Operace úlohy
   * Přímé metody  
1. Uložte nové nastavení. 

Pokud chcete zapnout nastavení diagnostiky pomocí prostředí PowerShell, použijte následující kód:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nové nastavení se projeví ve přibližně 10 minut. Potom protokolů se objeví v nakonfigurovaných archivace cílové na **nastavení diagnostiky** okno. Další informace o konfiguraci diagnostiky najdete v tématu [shromažďovat a využívat data protokolu z vašich prostředků azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
