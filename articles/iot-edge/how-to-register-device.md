---
title: Registrovat nové zařízení Azure IoT Edge | Microsoft Docs
description: Použití rozšíření IoT pro Azure CLI k registraci nového IoT Edge zařízení a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 85a5e2f6b28b9332e5ad3a38cdad3fb02bb3da87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450138"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrace zařízení Azure IoT Edge

Než budete moct zařízení IoT používat s Azure IoT Edge, musíte je zaregistrovat ve službě IoT Hub. Jakmile je zařízení zaregistrované, můžete načíst připojovací řetězec a nastavit zařízení pro IoT Edge úlohy.

Můžete si vybrat registraci pomocí jednoho z následujících nástrojů:

* Pokud dáváte přednost grafickému uživatelskému rozhraní, které umožňuje vytvářet, zobrazovat a spravovat prostředky Azure, [Zaregistrujte si zařízení v Azure Portal](#register-in-the-azure-portal) .
* Pokud upřednostňujete správu prostředků Azure IoT na stejném místě, kde budete vyvíjet řešení IoT, [Zaregistrujte zařízení v Visual Studio Code](#register-with-visual-studio-code) .
* Pokud dáváte přednost nástrojům příkazového řádku pro správu prostředků Azure, [Zaregistrujte zařízení pomocí Azure CLI](#register-with-the-azure-cli) , nebo v úmyslu automatizovat úlohy.

## <a name="register-in-the-azure-portal"></a>Zaregistrujte se do Azure Portal

Všechny úlohy registrace můžete provádět v Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Předpoklady pro Azure Portal

Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Vytvoření zařízení IoT Edge v Azure Portal

V IoT Hub v Azure Portal se zařízení IoT Edge vytváří a spravují odděleně od zařízení IOT, která nejsou povolená Edge.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
2. V levém podokně vyberte v nabídce možnost **IoT Edge** .
3. Vyberte **Přidat zařízení IoT Edge**.
4. Zadejte popisné ID zařízení. K automatickému generování ověřovacích klíčů a připojení nového zařízení k rozbočovači použijte výchozí nastavení.
5. Vyberte **Uložit**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Zobrazit IoT Edge zařízení v Azure Portal

Všechna zařízení s povoleným okrajem, která se připojují ke službě IoT Hub, jsou uvedena na stránce **IoT Edge** .

![Zobrazit všechna IoT Edge zařízení ve službě IoT Hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Načtení připojovacího řetězce v Azure Portal

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

1. Na stránce **IoT Edge** na portálu klikněte v seznamu IoT Edge zařízení na ID zařízení.
2. Zkopírujte hodnotu buď **primárního připojovacího řetězce** , nebo **sekundárního připojovacího řetězce**.

## <a name="register-with-visual-studio-code"></a>Zaregistrovat s Visual Studio Code

Většinu operací v VS Code lze provést několika způsoby. Tento článek používá Průzkumníka nástroje, ale ke spuštění tohoto postupu můžete použít také paletu příkazů.

### <a name="prerequisites-for-visual-studio-code"></a>Předpoklady pro Visual Studio Code

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se, abyste měli přístup ke službě IoT Hub.

Pomocí rozšíření Azure IoT pro Visual Studio Code můžete provádět operace s IoT Hub. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat IoT Hub.

1. V Visual Studio Code otevřete zobrazení **Průzkumníka** .
1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

   ![Část rozšíření zařízení Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud se tři tečky nevidí, klikněte nebo najeďte na záhlaví.
1. Zvolte **vybrat IoT Hub**.
1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů.
1. Vyberte své předplatné Azure.
1. Vyberte centrum IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Vytvoření zařízení IoT Edge pomocí Visual Studio Code

1. V Průzkumníku VS Code rozbalte část **zařízení Azure IoT Hub** .
1. V záhlaví oddílu **zařízení IoT Hub Azure** klikněte na **...** Pokud se tři tečky nevidí, klikněte nebo najeďte na záhlaví.
1. Vyberte **vytvořit IoT Edge zařízení**.
1. Do textového pole, které se otevře, zadejte ID zařízení.

Na obrazovce výstup vidíte výsledek příkazu. Budou vytištěny informace o zařízení, které obsahují **deviceId** , které jste zadali, a **připojovací řetězec** , který můžete použít k připojení fyzického zařízení k centru IoT.

Na obrazovce výstup vidíte výsledek příkazu. Budou vytištěny informace o zařízení, které obsahují **deviceId** , které jste zadali, a **připojovací řetězec** , který můžete použít k připojení fyzického zařízení k centru IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Zobrazit IoT Edge zařízení s Visual Studio Code

Všechna zařízení, která se připojují ke službě IoT Hub, jsou uvedená v části **Azure IoT Hub** v Průzkumníkovi Visual Studio Code. Zařízení IoT Edge lze odlišit od jiných než hraničních zařízení s jinou ikonou a skutečnost, že moduly **$edgeAgent** a **$edgeHub** jsou nasazeny do každého IoT Edge zařízení.

![Zobrazit všechna IoT Edge zařízení ve službě IoT Hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Načtení připojovacího řetězce pomocí Visual Studio Code

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

1. V části **Azure IoT Hub** klikněte pravým tlačítkem na ID vašeho zařízení.
1. Vyberte **Kopírovat připojovací řetězec zařízení**.

   Připojovací řetězec je zkopírován do schránky.

Můžete také vybrat možnost **získat informace o zařízení** z nabídky kliknutím pravým tlačítkem myši a zobrazit všechny informace o zařízení, včetně připojovacího řetězce, v okně výstup.

## <a name="register-with-the-azure-cli"></a>Registrace pomocí Azure CLI

[Azure CLI](/cli/azure) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby Device Provisioning a propojená centra. Rozšíření IoT rozšiřuje rozhraní příkazového řádku Azure pomocí funkcí, jako je Správa zařízení a plná IoT Edge funkce.

### <a name="prerequisites-for-the-azure-cli"></a>Předpoklady pro rozhraní příkazového řádku Azure

* [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) ve vašem prostředí. Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension)

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Vytvoření zařízení IoT Edge pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) vytvořte novou identitu zařízení ve službě IoT Hub. Například:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz obsahuje tři parametry:

* **ID zařízení**: zadejte popisný název, který je jedinečný pro Centrum IoT.
* **název centra**: zadejte název centra IoT.
* **Edge-povoleno**: Tento parametr deklaruje, že zařízení je pro použití s IoT Edge.

   ![AZ IoT Hub Device-identity Create Output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Zobrazení IoT Edge zařízení pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) zobrazíte všechna zařízení ve službě IoT Hub. Například:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Všechna zařízení, která jsou zaregistrovaná jako zařízení IoT Edge, budou mít **Možnosti vlastností. iotEdge** nastaveno na **hodnotu true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Načtení připojovacího řetězce pomocí Azure CLI

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub. K vrácení připojovacího řetězce pro jedno zařízení použijte příkaz [AZ IoT Hub Device-identity Connection-String show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) :

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

Hodnota `device-id` parametru rozlišuje velká a malá písmena. Nekopíruje uvozovky kolem připojovacího řetězce.

## <a name="next-steps"></a>Další kroky

Teď, když máte registrovanou identitu zařízení ve službě IoT Hub, jste připraveni nainstalovat IoT Edge modul runtime na vaše zařízení. Nainstalujte modul runtime podle operačního systému zařízení:

* [Instalace Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md)
* [Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)
