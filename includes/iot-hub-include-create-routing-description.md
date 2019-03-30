---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630946"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Potřebujete směrovat zprávy do různých prostředků na základě vlastností, které ke zprávě připojilo simulované zařízení. Zprávy, které nejsou směrovány podle vlastních pravidel, se posílají na výchozí koncový bod (zprávy/události). V dalším kurzu odesílat zprávy do služby IoT Hub a vidět směrovat na různé cíle.

|hodnota |Výsledek|
|------|------|
|level="storage" |Zapsat do úložiště Azure Storage.|
|level="critical" |Zapsat do fronty Service Bus. Aplikace logiky načte zprávu z fronty a pomocí Office 365 ji odešle e-mailem.|
|default |Zobrazte tato data pomocí Power BI.|

Prvním krokem je nastavení koncového bodu, ke kterému se budou směrovat data. Druhým krokem je nastavení směrování zpráv, který používá tento koncový bod. Po nastavení směrování, můžete zobrazit na portálu pro koncové body a směrování zpráv.