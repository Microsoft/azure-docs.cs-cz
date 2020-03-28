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
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77111191"
---
[Směrování zpráv](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) umožňuje odesílání telemetrických dat z vašich zařízení IoT do integrovaných koncových bodů kompatibilních s centrem událostí nebo do vlastních koncových bodů, jako je úložiště objektů blob, fronty služby Service Bus, témata služby Service Bus a centra událostí. Chcete-li konfigurovat vlastní směrování zpráv, vytvořte [směrovací dotazy](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) pro přizpůsobení trasy, která odpovídá určité podmínce. Po nastavení se příchozí data automaticky přesměrují na koncové body pomocí služby IoT Hub. Pokud zpráva neodpovídá žádnému z definovaných směrovacích dotazů, je směrována do výchozího koncového bodu.

V tomto dvoudílném kurzu se dozvíte, jak nastavit a používat tyto vlastní směrovací dotazy pomocí služby IoT Hub. Směrujete zprávy ze zařízení IoT do jednoho z více koncových bodů, včetně úložiště objektů blob a fronty service bus. Zprávy do fronty Service Bus jsou vyzvednuty aplikací logiky a odesílány e-mailem. Zprávy, které nemají definované vlastní směrování zpráv, se posílají do výchozího koncového bodu, pak je vyzvedne Azure Stream Analytics a zobrazí se ve vizualizaci Power BI.

Chcete-li dokončit části 1 a 2 tohoto kurzu, proveďte následující úkoly:

**Část I: Vytvoření zdrojů, nastavení směrování zpráv**
> [!div class="checklist"]
> * Vytvořte prostředky – centrum IoT, účet úložiště, frontu service bus a simulované zařízení. To se dá dělat pomocí portálu Azure, šablony Azure Resource Manager, Azure CLI nebo Azure PowerShellu.
> * Nakonfigurujte koncové body a trasy zpráv v centru IoT Hub pro účet úložiště a frontu service bus.

**Část II: Odesílání zpráv do centra, zobrazení směrovaných výsledků**
> [!div class="checklist"]
> * Vytvoření aplikace logiky, která se aktivuje a odešle e-mail, kdykoli se ve frontě Service Bus objeví nová zpráva.
> * Stažení a spuštění aplikaci, která bude simulovat IoT zařízení odesílající zprávy do centra s různými možnosti směrování.
> * Vytvoření vizualizace Power BI pro data odesílaná výchozím koncovým bodem.
> * Zobrazení výsledků...
> * ...ve frontě Service Bus a v e-mailech.
> * ...v účtu úložiště.
> * ...ve vizualizaci Power BI.

## <a name="prerequisites"></a>Požadavky

* Pro část 1 tohoto kurzu:
  - Mít předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

* Pro část 2 tohoto kurzu:
  - Musíte mít dokončenou část 1 tohoto kurzu a mít prostředky stále k dispozici.
  - Nainstalujte [visual studio](https://www.visualstudio.com/).
  - Měj přístup k účtu Power BI a analyzujte analýzu streamu výchozího koncového bodu. ([Vyzkoušejte službu Power BI zdarma](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Měj účet Office 365 pro odesílání e-mailů s oznámením.
  - Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
