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
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "72808819"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Potřebujete směrovat zprávy do různých prostředků na základě vlastností, které ke zprávě připojilo simulované zařízení. Zprávy, které nejsou směrovány podle vlastních pravidel, se posílají na výchozí koncový bod (zprávy/události). V dalším kurzu odesíláte zprávy do centra IoT Hub a vidíte je směrované do různých cílů.

|Hodnota |Výsledek|
|------|------|
|level="storage" |Zapsat do úložiště Azure Storage.|
|level="critical" |Zapsat do fronty Service Bus. Aplikace logiky načte zprávu z fronty a pomocí Office 365 ji odešle e-mailem.|
|default |Zobrazte tato data pomocí Power BI.|

Prvním krokem je nastavení koncového bodu, do kterého budou data směrována. Druhým krokem je nastavení trasy zprávy, která používá tento koncový bod. Po nastavení směrování můžete zobrazit koncové body a trasy zpráv na portálu.