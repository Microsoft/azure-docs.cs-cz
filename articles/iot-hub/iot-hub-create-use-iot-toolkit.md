---
title: Vytvoření služby Azure IoT Hub pomocí nástrojů Azure IoT Tools for VS Code | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí nástrojů Azure IoT pro Visual Studio Code vytvořit azure iot hub ve skupině prostředků.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912257"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Vytvoření centra IoT pomocí nástroje Azure IoT Nástroje pro Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek ukazuje, jak pomocí [nástrojů Azure IoT pro visual studio kód](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) k vytvoření centra Azure IoT. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li tento článek dokončit, potřebujete následující:

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

- [Kód visual studia](https://code.visualstudio.com/)

- [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro kód Visual Studia.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

1. V kódu Visual Studia otevřete zobrazení **Průzkumníka.**

2. V dolní části Průzkumníka rozbalte část **Zařízení služby Azure IoT Hub.** 

   ![Rozšíření zařízení Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klikněte na **...** v záhlaví oddílu **Zařízení Azure IoT Hub.** Pokud nevidíte tři tečky, najeďte nad záhlaví. 

4. Zvolte **Vytvořit centrum IoT Hub**.

5. V pravém dolním rohu se zobrazí vyskakovací okno, které vám umožní poprvé se přihlásit do Azure.

6. Vyberte předplatné Azure. 

7. Vyberte skupinu prostředků.

8. Vyberte umístění.

9. Vyberte cenovou úroveň.

10. Zadejte globálně jedinečný název vašeho IoT Hubu.

11. Počkejte několik minut, dokud se vytvoří centrum IoT Hub.

## <a name="next-steps"></a>Další kroky

Teď jste nasadili službu IoT hub pomocí nástrojů Azure IoT pro kód Visual Studia. Chcete-li prozkoumat další, podívejte se na následující články:

* [Pomocí nástrojů Azure IoT nástroje pro Visual Studio kód pro odesílání a přijímání zpráv mezi zařízením a služby IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Použití nástrojů Azure IoT tools for Visual Studio Code for Azure IoT Hub management](iot-hub-device-management-iot-toolkit.md)

* [Podívejte se na stránku wiki webu Azure IoT Hub for VS Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
