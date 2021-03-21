---
title: Nasazení aktualizace pomocí aktualizace zařízení pro Azure IoT Hub | Microsoft Docs
description: Nasazení aktualizace pomocí aktualizace zařízení pro Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679238"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Nasazení aktualizace pomocí aktualizace zařízení pro IoT Hub

Přečtěte si, jak nasadit aktualizaci do zařízení IoT pomocí aktualizace zařízení pro IoT Hub.

## <a name="prerequisites"></a>Předpoklady

* [Pro IoT Hub povolený přístup k IoT Hub s aktualizací zařízení](create-device-update-account.md). Doporučujeme pro IoT Hub použít úroveň S1 (Standard) nebo vyšší. 
* [Pro zřízené zařízení se úspěšně naimportovala aspoň jedna aktualizace.](import-update.md) 
* Zařízení IoT (nebo simulátor) zřízené pro aktualizaci zařízení v IoT Hub.
* [Zařízení IoT, které se pokoušíte aktualizovat, bylo přiřazeno klíčové slovo. Zařízení je součástí aspoň jedné skupiny aktualizací.](create-update-group.md)
* Podporované prohlížeče:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Nasazení aktualizace

1. Přejít na [Azure Portal](https://portal.azure.com)

2. Přejděte do okna aktualizace zařízení v IoT Hub.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. V horní části stránky vyberte kartu skupiny. [Přečtěte si další informace](device-update-groups.md) o skupinách zařízení. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Karta skupiny" lightbox="media/deploy-update/updated-view.png":::

4. Prohlédněte si seznam grafů a skupin pro aktualizaci kompatibility. Měla by se zobrazit nová aktualizace pro skupinu zařízení s odkazem na aktualizaci v části čekají na aktualizace (možná budete muset aktualizaci jednou aktualizovat). [Přečtěte si další informace o kompatibilitě aktualizací.](device-update-compliance.md) 

5. Vyberte dostupnou aktualizaci.

6. Potvrďte, že je jako cílová skupina vybraná správná skupina. Naplánujte nasazení a pak vyberte nasadit aktualizaci.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Vybrat aktualizaci" lightbox="media/deploy-update/select-update.png":::

7. Zobrazení grafu dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Probíhá aktualizace." lightbox="media/deploy-update/update-in-progress.png":::

8. Po úspěšné aktualizaci zařízení by se měl zobrazit graf dodržování předpisů a aktualizace podrobností nasazení, aby odrážely stejnou hodnotu. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Úspěšná aktualizace" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorování nasazení aktualizací

1. V horní části stránky vyberte kartu nasazení.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta nasazení" lightbox="media/deploy-update/deployments-tab.png":::

2. Vyberte nasazení, které jste vytvořili pro zobrazení podrobností o nasazení.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Podrobnosti o nasazení" lightbox="media/deploy-update/deployment-details.png":::

3. Pokud chcete zobrazit nejnovější podrobnosti o stavu, vyberte aktualizovat. Pokračujte v tomto procesu, dokud se stav nezmění na úspěšné.


## <a name="retry-an-update-deployment"></a>Opakovat nasazení aktualizace

Pokud se nasazení z nějakého důvodu nepovede, můžete opakovat nasazení u neúspěšných zařízení. 

1. Přejít na kartu nasazení a vyberte nasazení, které selhalo. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Podrobnosti o nasazení" lightbox="media/deploy-update/deployment-details.png":::

2. V podokně podrobné informace o nasazení klikněte na stav zařízení "neúspěšné".

3. Klikněte na zařízení opakovat neúspěšná a potvrďte oznámení o potvrzení. 

## <a name="next-steps"></a>Další kroky

[Řešení běžných potíží](troubleshoot-device-update.md)
