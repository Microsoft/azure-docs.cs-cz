---
title: Monitorování zařízení Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Popisuje, jak používat portál Azure a místní webové uživatelské prostředí ke sledování azure data box edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756742"
---
# <a name="monitor-your-azure-data-box-edge"></a>Sledování azure data boxu Edge

Tento článek popisuje, jak sledovat azure data box edge. Ke sledování zařízení můžete použít portál Azure nebo místní webové uživatelské rozhraní. Na webu Azure Portal můžete zobrazit události zařízení, konfigurovat a spravovat výstrahy a zobrazovat metriky. Pomocí místního webového uživatelského rozhraní ve fyzickém zařízení můžete zobrazit stav hardwaru různých součástí zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Zobrazení událostí zařízení a odpovídajících výstrah
> * Zobrazení stavu hardwaru součástí zařízení
> * Zobrazení metrik kapacity a transakcí pro vaše zařízení
> * Konfigurace a správa výstrah

## <a name="view-device-events"></a>Zobrazit události zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Zobrazit stav hardwaru

Chcete-li zobrazit stav hardwaru součástí zařízení, postupujte v místním webovém uživatelském rozhraní následujícím postupem.

1. Připojte se k místnímu webovému uživatelskému rozhraní vašeho zařízení.
2. Přejděte na **stav údržby > hardware**. Můžete zobrazit stav různých součástí zařízení.

    ![Zobrazit stav hardwaru](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Zobrazit metriky

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Správa upozornění

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Další kroky 

Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).