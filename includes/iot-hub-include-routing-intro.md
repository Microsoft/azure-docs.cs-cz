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
ms.openlocfilehash: d0accd01926743d64fa4911dfe56806537170c2d
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271502"
---
[Směrování zpráv](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) povolí odesílání telemetrických dat ze zařízení IoT pro integrované koncové body kompatibilní s centrem událostí nebo vlastní koncové body, jako objekt blob úložiště, fronty Service Bus, témat Service Bus a Event Hubs. Pokud chcete nakonfigurovat vlastní zprávu směrování, vytvoříte [směrování dotazů](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) přizpůsobení trasy, která odpovídá určité podmínky. Po nastavení se příchozí data automaticky přesměrují na koncové body pomocí služby IoT Hub. Pokud zpráva neodpovídá žádné z definovaných směrování dotazů, přesměruje ho na výchozí koncový bod.

V tomto kurzu 2 části se dozvíte, jak nastavit a použití těchto vlastní směrování dotazů se službou IoT Hub. Směrování zpráv ze zařízení IoT k jednomu z více koncových bodů, včetně úložiště objektů blob a fronty služby Service Bus. Zprávy do fronty služby Service Bus se prodlouží aplikace logiky a odeslání e-mailem. Zprávy, které nemají vlastní zprávu směrování definované odeslání výchozí koncový bod, pak neexistoval, Azure Stream Analytics a zobrazit ve vizualizaci Power BI.

K dokončení části 1 a 2 v tomto kurzu jste provedli následující úlohy:

**Část I: Vytvoření prostředků, nastavte směrování zpráv**
> [!div class="checklist"]
> * Vytvoření prostředků – služby IoT hub, účet úložiště, fronty Service Bus a simulovaných zařízení. To lze provést pomocí portálu, rozhraní příkazového řádku Azure, Azure Powershellu nebo šablony Azure Resource Manageru.
> * Konfigurace koncových bodů a směrování zpráv ve službě IoT Hub pro účet úložiště a fronty služby Service Bus.

**Část II: Odesílání zpráv do centra, zobrazení směrované výsledků**
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
  - Mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* V části 2 tohoto kurzu:
  - Musíte mít dokončené části 1 tohoto kurzu a máte stále dostupné prostředky.
  - Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/).
  - Účet Power BI pro analýzu streamu výchozího koncového bodu. ([Vyzkoušejte službu Power BI zdarma](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Účet Office 365 pro odesílání e-mailových oznámení.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
