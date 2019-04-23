---
title: Azure Data Box hraniční zařízení naprogramujete sledování | Dokumentace Microsoftu
description: Popisuje, jak pomocí webu Azure portal a místního webového uživatelského rozhraní pro monitorování Azure Data Box hranici.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002642"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorování Azure Data Box hranici

Tento článek popisuje, jak monitorovat Azure Data Box hranici. K monitorování zařízení, můžete použít portál Azure nebo místního webového uživatelského rozhraní. Pomocí webu Azure portal do zobrazení událostí zařízení, konfigurovat a spravovat výstrahy a zobrazení metrik. Použití místního webového uživatelského rozhraní na zařízení s fyzické zobrazení stavu hardwaru různých součástí zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Zobrazit odpovídající výstrahy a události zařízení
> * Zobrazení stavu hardwaru součástí zařízení
> * Zobrazit kapacita a transakce metriky pro vaše zařízení
> * Konfigurace a Správa výstrah

## <a name="view-device-events"></a>Zobrazení událostí modulu zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Zobrazení stavu hardwaru

Proveďte následující kroky v místním webovém uživatelském rozhraní pro zobrazení stavu hardwaru součástí zařízení.

1. Připojte se k místního webového uživatelského rozhraní vašeho zařízení.
2. Přejděte na **údržby > Stav hardwaru**. Můžete zobrazit stav různých součástí zařízení.

    ![Zobrazení stavu hardwaru](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Zobrazit metriky

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Správa upozornění

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Další postup 

Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).