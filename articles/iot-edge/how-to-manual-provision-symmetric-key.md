---
title: Zřízení s symetrickými klíči – Azure IoT Edge | Microsoft Docs
description: Po instalaci zřídí zařízení IoT Edge s symetrickými klíči pomocí připojovacího řetězce a ověří IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: f5371539c1b45c14b519729c7c07003bf74847a0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043863"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Nastavení zařízení Azure IoT Edge s ověřováním pomocí symetrického klíče

Tento článek popisuje postup, jak zaregistrovat nové zařízení IoT Edge v IoT Hub a nakonfigurovat zařízení tak, aby se ověřilo pomocí symetrických klíčů.

Kroky v tomto článku vás provedou procesem, který se nazývá ruční zřizování, kde se každé zařízení připojí ke službě IoT Hub ručně. Alternativa je Automatické zřizování pomocí IoT Hub Device Provisioning Service, což je užitečné, když máte mnoho zařízení, které je potřeba zřídit.

<!--TODO: Add auto-provision info/links-->

Pro ruční zřizování máte dvě možnosti ověřování IoT Edge zařízení:

* **Symetrický klíč**: když v IoT Hub vytvoříte novou identitu zařízení, služba vytvoří dva klíče. Do zařízení umístíte jeden z klíčů a při ověřování se zobrazí klíč, který se IoT Hub.

  Tato metoda ověřování je rychlejší, aby se dala začít, ale ne tak bezpečné.

* **X. 509 podepsaný svým držitelem**: vytvoříte dva certifikáty identit x. 509 a umístíte je na zařízení. Když v IoT Hub vytvoříte novou identitu zařízení, zadáváte kryptografické otisky z obou certifikátů. Když se zařízení ověřuje IoT Hub, prezentuje jeho certifikáty a IoT Hub může ověřit, jestli odpovídají kryptografickým otiskům.

  Tato metoda ověřování je bezpečnější a doporučuje se pro produkční scénáře.

Tento článek vás provede procesem registrace a zřízení s ověřováním pomocí symetrického klíče. Pokud se chcete dozvědět, jak nastavit zařízení pomocí certifikátů X. 509, přečtěte si téma [nastavení Azure IoT Edge zařízení s ověřováním pomocí certifikátu x. 509](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Požadované součásti

Než budete postupovat podle kroků v tomto článku, měli byste mít nainstalované zařízení s modulem runtime IoT Edge. V takovém případě postupujte podle pokynů v části [instalace nebo odinstalace modulu runtime Azure IoT Edge](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Registrace nového zařízení

Každé zařízení, které se připojuje k IoT Hub, má ID zařízení, které se používá ke sledování komunikace z cloudu na zařízení nebo ze zařízení do cloudu. Zařízení nakonfigurujete s informacemi o připojení, které zahrnují název hostitele IoT Hub, ID zařízení a informace, které zařízení používá k ověření IoT Hub.

Při ověřování pomocí symetrického klíče se tyto informace shromažďují v *připojovacím řetězci* , který můžete načíst z IoT Hub a pak umístit do IoT Edge zařízení.

Pomocí několika nástrojů můžete zaregistrovat nové IoT Edge zařízení v IoT Hub a načíst připojovací řetězec v závislosti na vaší předvolbách.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Předpoklady pro Azure Portal

Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Vytvoření zařízení IoT Edge v Azure Portal

V IoT Hub v Azure Portal se zařízení IoT Edge vytváří a spravují odděleně od zařízení IOT, která nejsou povolená Edge.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V levém podokně vyberte v nabídce **IoT Edge** a pak vyberte **Přidat IoT Edge zařízení**.

   ![Přidat zařízení IoT Edge z Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na stránce **vytvořit zařízení** zadejte následující informace:

   * Vytvořte popisné ID zařízení.
   * Jako typ ověřování vyberte **symetrický klíč** .
   * K automatickému generování ověřovacích klíčů a připojení nového zařízení k rozbočovači použijte výchozí nastavení.

1. Vyberte **Uložit**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Zobrazit IoT Edge zařízení v Azure Portal

Všechna zařízení s povoleným okrajem, která se připojují ke službě IoT Hub, jsou uvedena na stránce **IoT Edge** .

![Pomocí Azure Portal můžete zobrazit všechna IoT Edge zařízení ve službě IoT Hub.](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Načtení připojovacího řetězce v Azure Portal

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

1. Na stránce **IoT Edge** na portálu klikněte v seznamu IoT Edge zařízení na ID zařízení.
2. Zkopírujte hodnotu buď **primárního připojovacího řetězce** , nebo **sekundárního připojovacího řetězce**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Předpoklady pro Visual Studio Code

* Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) v předplatném Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se, abyste měli přístup ke službě IoT Hub.

Pomocí rozšíření Azure IoT pro Visual Studio Code můžete provádět operace s IoT Hub. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat IoT Hub.

1. V Visual Studio Code otevřete zobrazení **Průzkumníka** .
1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

   ![Část rozšíření zařízení Azure IoT Hub](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

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

![Pomocí VS Code můžete zobrazit všechna IoT Edge zařízení ve službě IoT Hub.](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Načtení připojovacího řetězce pomocí Visual Studio Code

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

1. V části **Azure IoT Hub** klikněte pravým tlačítkem na ID vašeho zařízení.
1. Vyberte **Kopírovat připojovací řetězec zařízení**.

   Připojovací řetězec je zkopírován do schránky.

Můžete také vybrat možnost **získat informace o zařízení** z nabídky kliknutím pravým tlačítkem myši a zobrazit všechny informace o zařízení, včetně připojovacího řetězce, v okně výstup.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Předpoklady pro rozhraní příkazového řádku Azure

* [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) ve vašem prostředí. Verze Azure CLI musí být minimálně 2.0.70 nebo novější. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension)

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Vytvoření zařízení IoT Edge pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) vytvořte novou identitu zařízení ve službě IoT Hub. Příklad:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz obsahuje tři parametry:

* `--device-id` nebo `-d` : zadejte popisný název, který je jedinečný pro vaše centrum IoT.
* `hub-name` nebo `-n` : zadejte název centra IoT.
* `--edge-enabled` nebo `--ee` : deklarujte, že se jedná o zařízení IoT Edge.

   ![AZ IoT Hub Device-identity Create Output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Zobrazení IoT Edge zařízení pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) zobrazíte všechna zařízení ve službě IoT Hub. Příklad:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Všechna zařízení, která jsou zaregistrovaná jako zařízení IoT Edge, budou mít **Možnosti vlastností. iotEdge** nastaveno na **hodnotu true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Načtení připojovacího řetězce pomocí Azure CLI

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub. Pomocí příkazu [AZ IoT Hub Device-identity show-Connection-String](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) vrátíte připojovací řetězec pro jedno zařízení:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Hodnota `device-id` parametru rozlišuje velká a malá písmena. Nekopíruje uvozovky kolem připojovacího řetězce.

---

## <a name="provision-an-iot-edge-device"></a>Zřízení zařízení IoT Edge

Jakmile má zařízení IoT Edge identitu v IoT Hub a připojovací řetězec, který může použít k ověřování, musíte zřídit samotné zařízení s těmito informacemi.

V zařízení se systémem Linux zadáte připojovací řetězec úpravou souboru config. yaml. Na zařízení s Windows můžete zadat připojovací řetězec spuštěním skriptu PowerShellu.

# <a name="linux"></a>[Linux](#tab/linux)

Na zařízení IoT Edge otevřete konfigurační soubor.

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte konfigurace zřizování souboru a zrušte komentář k **ruční konfiguraci zřizování pomocí oddílu připojovacího řetězce** . 

Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem ze zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

Vložení obsahu schránky do nano `Shift+Right Click` nebo stisknutím klávesy `Shift+Insert` .

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Na zařízení IoT Edge spusťte PowerShell jako správce.

2. Pomocí příkazu [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) nakonfigurujte na svém počítači modul runtime IoT Edge. Příkaz je standardně nastaven na ruční zřizování pomocí kontejnerů Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Pokud používáte kontejnery platformy Linux, přidejte `-ContainerOs` do příznaku parametr. Být konzistentní s možností kontejneru, kterou jste zvolili pomocí `Deploy-IoTEdge` příkazu, který jste předtím spustili.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Pokud jste do svého zařízení stáhli IoTEdgeSecurityDaemon.ps1 skript pro instalaci offline nebo konkrétní verze, nezapomeňte na místní kopii skriptu odkazovat.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po zobrazení výzvy zadejte připojovací řetězec zařízení, který jste získali v předchozí části. Připojovací řetězec zařízení přidružuje fyzické zařízení k ID zařízení v IoT Hub a poskytne ověřovací informace.

   Připojovací řetězec zařízení má následující formát a nesmí obsahovat uvozovky: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Při ručním zřizování zařízení můžete pomocí dalších parametrů upravit proces, včetně:

* Směrování provozu pomocí proxy server
* Deklarovat konkrétní image kontejneru edgeAgent a zadat přihlašovací údaje, pokud se nachází v privátním registru

Další informace o těchto dalších parametrech najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Další kroky

Pokračujte v [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) a Naučte se, jak nasadit moduly do svého zařízení.