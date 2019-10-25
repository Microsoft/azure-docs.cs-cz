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
ms.openlocfilehash: 863989f8e2cb90fe5ec0921ea6e080b61fc1b4ae
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808829"
---
[Směrování zpráv](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) umožňuje odesílat data telemetrie ze zařízení IoT do integrovaných koncových bodů kompatibilních s centrem událostí nebo vlastních koncových bodů, jako je BLOB storage, Service Bus fronty, Service Bus témata a Event Hubs. Chcete-li nakonfigurovat vlastní směrování zpráv, vytvořte [dotazy směrování](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) pro přizpůsobení trasy, která odpovídá určité podmínce. Po nastavení se příchozí data automaticky přesměrují na koncové body pomocí služby IoT Hub. Pokud zpráva neodpovídá žádnému z definovaných směrovacích dotazů, bude směrována do výchozího koncového bodu.

V tomto 2 kurzu se naučíte, jak nastavit a používat tyto vlastní dotazy směrování s IoT Hub. Zprávy ze zařízení IoT směrujete do jednoho z několika koncových bodů, včetně úložiště objektů BLOB a fronty Service Bus. Zprávy do fronty Service Bus vybírají aplikace logiky a odesílají se prostřednictvím e-mailu. Zprávy, které nemají definované vlastní směrování zpráv, se odešlou do výchozího koncového bodu a pak se vybírají Azure Stream Analytics a zobrazí se ve vizualizaci Power BI.

K dokončení částí 1 a 2 tohoto kurzu provedete následující úlohy:

**Část I: vytvoření prostředků, nastavení směrování zpráv**
> [!div class="checklist"]
> * Vytvoření prostředků – služby IoT Hub, účtu úložiště, fronty Service Bus a simulovaného zařízení. Můžete to udělat pomocí Azure Portal, Azure Resource Manager šablony, rozhraní příkazového řádku Azure nebo Azure PowerShell.
> * Konfigurace koncových bodů a směrování zpráv v IoT Hub pro účet úložiště a Service Bus fronty.

**Část II: odeslání zpráv do centra, zobrazení směrovaných výsledků**
> [!div class="checklist"]
> * Vytvoření aplikace logiky, která se aktivuje a odešle e-mail, kdykoli se ve frontě Service Bus objeví nová zpráva.
> * Stažení a spuštění aplikaci, která bude simulovat IoT zařízení odesílající zprávy do centra s různými možnosti směrování.
> * Vytvoření vizualizace Power BI pro data odesílaná výchozím koncovým bodem.
> * Zobrazení výsledků...
> * ...ve frontě Service Bus a v e-mailech.
> * ...v účtu úložiště.
> * ...ve vizualizaci Power BI.

## <a name="prerequisites"></a>Předpoklady

* V části 1 tohoto kurzu:
  - Mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* V části 2 tohoto kurzu:
  - Musíte mít hotovou část 1 tohoto kurzu a prostředky budou stále k dispozici.
  - Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/).
  - Mít přístup k účtu Power BI k analýze Stream Analytics výchozího koncového bodu. ([Vyzkoušejte službu Power BI zdarma](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Mít účet Office 365 pro odesílání e-mailů s oznámením.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
