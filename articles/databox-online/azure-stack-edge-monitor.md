---
title: Monitorování zařízení Azure Stack Edge | Microsoft Docs
description: Popisuje, jak pomocí Azure Portal a místního webového uživatelského rozhraní monitorovat Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569430"
---
# <a name="monitor-your-azure-stack-edge"></a>Monitorování Azure Stack Edge

Tento článek popisuje, jak monitorovat Azure Stack Edge. Pokud chcete monitorovat své zařízení, můžete použít Azure Portal nebo místní webové uživatelské rozhraní. Pomocí Azure Portal můžete zobrazit události zařízení, konfigurovat a spravovat výstrahy a zobrazovat metriky. Pomocí místního webového uživatelského rozhraní na fyzickém zařízení můžete zobrazit stav hardwaru různých součástí zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Zobrazení událostí zařízení a odpovídajících výstrah
> * Zobrazit stav hardwaru pro součásti zařízení
> * Zobrazit kapacitu a metriky transakcí pro vaše zařízení
> * Konfigurace a správa výstrah

## <a name="view-device-events"></a>Zobrazit události zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Zobrazit stav hardwaru

Chcete-li zobrazit stav hardwaru komponent zařízení, proveďte následující kroky v místním webovém uživatelském rozhraní.

1. Připojte se k místnímu webovému uživatelskému rozhraní vašeho zařízení.
2. **> stav hardwaru**přejít na údržba. Můžete zobrazit stav různých součástí zařízení. 

    ![Zobrazit stav hardwaru](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Zobrazit metriky

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Správa upozornění

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Další kroky 

Další informace o [správě šířky pásma](azure-stack-edge-manage-bandwidth-schedules.md).