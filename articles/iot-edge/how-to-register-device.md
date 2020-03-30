---
title: Registrace nového zařízení Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použití rozšíření IoT pro rozhraní příkazového nastavení Azure k registraci nového zařízení IoT Edge a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235731"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrace zařízení Azure IoT Edge

Než budete moci používat zařízení IoT s Azure IoT Edge, musíte je zaregistrovat ve svém centru IoT Hub. Po registraci zařízení můžete načíst připojovací řetězec a nastavit zařízení pro úlohy IoT Edge.

Registraci můžete mít k rozhodnutí pomocí jednoho z následujících nástrojů:

* [Zaregistrujte zařízení na webu Azure Portal,](#register-in-the-azure-portal) pokud dáváte přednost grafickému uživatelskému rozhraní pro vytváření, zobrazení a správu prostředků Azure.
* [Zaregistrujte zařízení s Visual Studio Code,](#register-with-visual-studio-code) pokud dáváte přednost správě prostředků Azure IoT na stejném místě, kde vyvíjíte řešení IoT.
* [Zaregistrujte zařízení pomocí rozhraní příkazového řádku Azure,](#register-with-the-azure-cli) pokud dáváte přednost nástrojům příkazového řádku pro správu prostředků Azure nebo máte v úmyslu automatizovat úlohy.

## <a name="register-in-the-azure-portal"></a>Registrace na webu Azure Portal

Všechny úlohy registrace můžete provádět na webu Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Předpoklady pro portál Azure

Bezplatné nebo standardní [centrum IoT v](../iot-hub/iot-hub-create-through-portal.md) rámci předplatného Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Vytvoření zařízení IoT Edge na webu Azure Portal

Ve vašem IoT Hubu na webu Azure Portal se zařízení IoT Edge vytvářejí a spravují odděleně od zařízení IOT, která nejsou povolená jako edge.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do svého centra IoT hub.
2. V levém podokně vyberte z nabídky **IoT Edge.**
3. Vyberte **Přidat zařízení IoT Edge**.
4. Zadejte popisné ID zařízení. Pomocí výchozího nastavení můžete automaticky generovat ověřovací klíče a připojit nové zařízení k rozbočovači.
5. Vyberte **Uložit**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Zobrazení zařízení IoT Edge na webu Azure Portal

Všechna zařízení s podporou okrajů, která se připojují k vašemu centru IoT hub, jsou uvedená na stránce **IoT Edge.**

![Zobrazení všech zařízení IoT Edge ve vašem centru IoT hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Načtení připojovacího řetězce na webu Azure Portal

Až budete připraveni nastavit zařízení, budete potřebovat připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou v centru IoT.

1. Na stránce **IoT Edge** na portálu klikněte na ID zařízení ze seznamu zařízení IoT Edge.
2. Zkopírujte hodnotu **primárního připojovacího řetězce** nebo **sekundárního připojovacího řetězce**.

## <a name="register-with-visual-studio-code"></a>Registrace pomocí kódu sady Visual Studio

Existuje několik způsobů, jak provádět většinu operací v Kódu VS. Tento článek používá Průzkumníka, ale můžete také použít paletu příkazů ke spuštění kroků.

### <a name="prerequisites-for-visual-studio-code"></a>Požadavky pro kód sady Visual Studio

* Centrum [IoT v](../iot-hub/iot-hub-create-through-portal.md) předplaceném Azure
* [Kód visual studia](https://code.visualstudio.com/)
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro kód Visual Studia

### <a name="sign-in-to-access-your-iot-hub"></a>Přihlášení pro přístup k centru IoT hub

Rozšíření Azure IoT pro Visual Studio Code můžete použít k provádění operací s iot hubem. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat ioT hub.

1. V kódu Visual Studia otevřete zobrazení **Průzkumníka.**
1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub.**

   ![Rozšíření části Zařízení služby Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klikněte na **...** v záhlaví oddílu **Azure IoT Hub.** Pokud tři tečky nevidíte, klikněte na záhlaví nebo na něj najeďte.
1. Zvolte **Vybrat centrum IoT .**
1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů, jak toho chcete učinit.
1. Vyberte své předplatné Azure.
1. Vyberte svůj ioT hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Vytvoření zařízení IoT Edge s kódem Visual Studia

1. V Průzkumníku kódů VS rozbalte část **Zařízení služby Azure IoT Hub.**
1. Klikněte na **...** v záhlaví oddílu **Zařízení Azure IoT Hub.** Pokud tři tečky nevidíte, klikněte na záhlaví nebo na něj najeďte.
1. Vyberte **Vytvořit zařízení IoT Edge**.
1. V textovém poli, které se otevře, přidejte zařízení ID.

Na výstupní obrazovce se zobrazí výsledek příkazu. Informace o zařízení se vytisknou, který zahrnuje **deviceId,** které jste zadali a **connectionString,** které můžete použít k připojení fyzického zařízení k centru IoT.

Na výstupní obrazovce se zobrazí výsledek příkazu. Informace o zařízení se vytisknou, který zahrnuje **deviceId,** které jste zadali a **connectionString,** které můžete použít k připojení fyzického zařízení k centru IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Zobrazení zařízení IoT Edge s kódem Visual Studia

Všechna zařízení, která se připojují k vašemu centru IoT hub, jsou uvedená v části **Azure IoT Hub** v Průzkumníku kódu Visual Studia. Zařízení IoT Edge se odlišují od zařízení jiných než Edge s jinou ikonou a skutečností, že **$edgeAgent** a **$edgeHub** moduly se nasazují do každého zařízení IoT Edge.

![Zobrazení všech zařízení IoT Edge ve vašem centru IoT hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Načtení připojovacího řetězce pomocí kódu sady Visual Studio

Až budete připraveni nastavit zařízení, budete potřebovat připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou v centru IoT.

1. Klikněte pravým tlačítkem na ID vašeho zařízení v části **Azure IoT Hub.**
1. Vyberte **možnost Kopírovat připojovací řetězec zařízení**.

   Připojovací řetězec se zkopíruje do schránky.

Můžete také vybrat **Získat informace o zařízení** z nabídky po kliknutí pravým tlačítkem myši a zobrazit všechny informace o zařízení, včetně připojovacího řetězce, ve výstupním okně.

## <a name="register-with-the-azure-cli"></a>Registrace pomocí příkazového příkazového příkazu Azure

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) je open source multiplatformní příkazový řádek nástroj pro správu prostředků Azure, jako je IoT Edge. Umožňuje spravovat prostředky Azure IoT Hub, instance služeb zřizování zařízení a propojené rozbočovače po vybalení. Rozšíření IoT obohacuje rozhraní příkazového odpovky Azure o funkce, jako je správa zařízení a úplná funkce IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Požadavky na azure cli

* Centrum [IoT v](../iot-hub/iot-hub-create-using-cli.md) předplacenéazure.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně vaše verze Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Vytvoření zařízení IoT Edge pomocí rozhraní příkazového příkazu Azure

Pomocí příkazu [az iot hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) pro vytvoření identity zařízení vytvořte novou identitu zařízení ve vašem centru IoT Hub. Například:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz obsahuje tři parametry:

* **id zařízení**: Zadejte popisný název, který je jedinečný pro váš ioT hub.
* **Název centra**: Zadejte název centra IoT hub.
* **edge-enabled**: Tento parametr deklaruje, že zařízení je pro použití s IoT Edge.

   ![az iot hub vytvoření identity zařízení](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Zobrazení zařízení IoT Edge pomocí příkazového příkazového příkazu k Azure

Pomocí příkazu [az iot hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) pro zobrazení všech zařízení ve vašem centru IoT Hub. Například:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Jakékoli zařízení, které je registrováno jako zařízení IoT Edge, bude mít vlastnost **capabilities.iotEdge** nastavenou na **hodnotu true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Načtení připojovacího řetězce pomocí příkazového příkazu k řešení Azure

Až budete připraveni nastavit zařízení, budete potřebovat připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou v centru IoT. Pomocí příkazu [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) vraťte připojovací řetězec pro jedno zařízení:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Hodnota parametru `device-id` rozlišuje malá a velká písmena. Nekopírujte uvozovky kolem připojovacího řetězce.

## <a name="next-steps"></a>Další kroky

Teď, když máte identitu zařízení registrovanou ve službě IoT hub, jste připraveni nainstalovat runtime IoT Edge na vaše zařízení. Nainstalujte runtime podle operačního systému zařízení:

* [Instalace Azure IoT Edge do Windows](how-to-install-iot-edge-windows.md)
* [Instalace runtime Azure IoT Edge na Linux](how-to-install-iot-edge-linux.md)
