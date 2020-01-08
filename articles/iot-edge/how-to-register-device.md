---
title: Registrovat nové zařízení Azure IoT Edge | Microsoft Docs
description: Použití rozšíření IoT pro rozhraní příkazového řádku Azure k registraci nového zařízení IoT Edge a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ab9c778adef29a8e531158e062e9d35d4e80ae4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434132"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrace zařízení Azure IoT Edge

Než budete moct zařízení IoT používat s Azure IoT Edge, musíte je zaregistrovat ve službě IoT Hub. Jakmile je zařízení zaregistrované, můžete načíst připojovací řetězec a nastavit zařízení pro IoT Edge úlohy.

Můžete si vybrat registraci pomocí jednoho z následujících nástrojů:

* [Azure Portal](https://portal.azure.com) poskytuje grafické uživatelské rozhraní pro vytváření, zobrazování a správu prostředků Azure.
* [Visual Studio Code](https://code.visualstudio.com/) je Editor zdrojového kódu. Rozšíření Azure IoT usnadňují správu prostředků IoT ze stejného nástroje, ve kterém vyvíjíte řešení IoT.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je nástroj příkazového řádku pro správu prostředků Azure. Příkazy, které lze opakovaně použít, jsou užitečné pro automatizaci úloh.

## <a name="register-in-the-azure-portal"></a>Zaregistrujte se do Azure Portal

Všechny úlohy registrace můžete provádět v Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Předpoklady pro Azure Portal

Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Vytvoření zařízení IoT Edge v Azure Portal

V IoT Hub v Azure Portal se zařízení IoT Edge vytváří a spravují odděleně od zařízení IOT, která nejsou povolená Edge.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
2. V levém podokně vyberte v nabídce možnost **IoT Edge** .
3. Vyberte **Přidat zařízení IoT Edge**.
4. Zadejte ID popisný zařízení. K automatickému generování ověřovacích klíčů a připojení nového zařízení k rozbočovači použijte výchozí nastavení.
5. Vyberte **Uložit**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Zobrazit IoT Edge zařízení v Azure Portal

Jsou uvedeny všechny povolené hraniční zařízení, která připojení ke službě IoT hub na **IoT Edge** stránky.

![Zobrazit všechna zařízení IoT Edge ve službě IoT hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Načtení připojovacího řetězce v Azure Portal

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub.

1. Na stránce **IoT Edge** na portálu klikněte v seznamu IoT Edge zařízení na ID zařízení.
2. Zkopírujte hodnotu buď **primárního připojovacího řetězce** , nebo **sekundárního připojovacího řetězce**.

## <a name="register-with-visual-studio-code"></a>Zaregistrovat s Visual Studio Code

Existuje více způsobů provádějí většinu operací v nástroji VS Code. Tento článek používá Průzkumníka nástroje, ale ke spuštění tohoto postupu můžete použít také paletu příkazů.

### <a name="prerequisites-for-visual-studio-code"></a>Předpoklady pro Visual Studio Code

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT hub

Pomocí rozšíření Azure IoT pro Visual Studio Code můžete provádět operace s IoT Hub. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat IoT Hub.

1. V sadě Visual Studio Code otevřete **Explorer** zobrazení.
1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

   ![Rozbalte část zařízení Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud nevidíte tři tečky, klikněte na nebo najeďte myší na záhlaví.
1. Zvolte **vyberte službu IoT Hub**.
1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů.
1. Vyberte své předplatné Azure.
1. Vyberte své Centrum IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Vytvoření zařízení IoT Edge pomocí Visual Studio Code

1. V Průzkumníku VS Code rozbalte **zařízení Azure IoT Hub** oddílu.
1. Klikněte na **...**  v **zařízení Azure IoT Hub** hlavičku oddílu. Pokud nevidíte tři tečky, klikněte na nebo najeďte myší na záhlaví.
1. Vyberte **vytvoření zařízení IoT Edge**.
1. V textovém poli, které se otevře zadejte zařízení identifikátor.

V okně výstupu se zobrazí výsledek příkazu. Vytisknout informace o zařízení, která obsahuje **deviceId** , které jste zadali a **connectionString** , můžete použít k připojení fyzických zařízení do služby IoT hub.

V okně výstupu se zobrazí výsledek příkazu. Vytisknout informace o zařízení, která obsahuje **deviceId** , které jste zadali a **connectionString** , můžete použít k připojení fyzických zařízení do služby IoT hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Zobrazit IoT Edge zařízení s Visual Studio Code

Všechna zařízení, která se připojují ke službě IoT Hub, jsou uvedená v části **Azure IoT Hub** v Průzkumníkovi Visual Studio Code. Zařízení IoT Edge lze odlišit od jiných než hraničních zařízení s jinou ikonou a skutečnost, že moduly **$edgeAgent** a **$edgeHub** jsou nasazeny do každého IoT Edge zařízení.

![Zobrazit všechna zařízení IoT Edge ve službě IoT hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Načtení připojovacího řetězce pomocí Visual Studio Code

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub.

1. V části **Azure IoT Hub** klikněte pravým tlačítkem na ID vašeho zařízení.
1. Vyberte **zkopírujte připojovací řetězec zařízení**.

   Připojovací řetězec je zkopírován do schránky.

Můžete také vybrat **získat informace o zařízení** v místní nabídce zobrazíte všechny informace o zařízení, včetně připojovací řetězec v okně výstup.

## <a name="register-with-the-azure-cli"></a>Registrace pomocí Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure, jako je IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služby device provisioning a propojená centra úprav. Nové rozšíření IoT vylepšuje Azure CLI s funkcemi, jako je Správa zařízení a úplné možnosti služby IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Předpoklady pro rozhraní příkazového řádku Azure

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně musí být vaše Azure CLI verze 2.0.24 nebo novější. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Vytvoření zařízení IoT Edge pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) vytvořte novou identitu zařízení ve službě IoT Hub. Příklad:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz zahrnuje tři parametry:

* **id zařízení**: Zadejte popisný název, který je jedinečný pro své Centrum IoT.
* **název centra**: Zadejte název služby IoT hub.
* **povolené Edge**: Tento parametr deklaruje, že zařízení je pro použití se službou IoT Edge.

   ![vytvořit výstup, az iot hub-identity zařízení](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Zobrazení IoT Edge zařízení pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) zobrazíte všechna zařízení ve službě IoT Hub. Příklad:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Jakékoli zařízení, které je zaregistrované jako zařízení IoT Edge bude mít vlastnost **capabilities.iotEdge** nastavena na **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Načtení připojovacího řetězce pomocí Azure CLI

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub. Pomocí příkazu [AZ IoT Hub Device-identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) vrátíte připojovací řetězec pro jedno zařízení:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Hodnota parametru `device-id` rozlišuje velká a malá písmena. Nekopírujte uvozovky kolem připojovací řetězec.

## <a name="next-steps"></a>Další kroky

Teď, když máte registrovanou identitu zařízení ve službě IoT Hub, jste připraveni nainstalovat IoT Edge modul runtime na vaše zařízení. Nainstalujte modul runtime podle operačního systému zařízení:

* [Instalace Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md)
* [Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)
