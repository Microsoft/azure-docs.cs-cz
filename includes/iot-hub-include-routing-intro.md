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
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85128193"
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

## <a name="prerequisites"></a>Požadavky

* V části 1 tohoto kurzu:
  - Mít předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* V části 2 tohoto kurzu:
  - Musíte mít hotovou část 1 tohoto kurzu a prostředky budou stále k dispozici.
  - Nainstalujte [Visual Studio](https://www.visualstudio.com/).
  - Mít přístup k účtu Power BI k analýze Stream Analytics výchozího koncového bodu. ([Vyzkoušejte službu Power BI zdarma](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Máte pracovní nebo školní účet pro odesílání e-mailů s oznámením.
  - Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
