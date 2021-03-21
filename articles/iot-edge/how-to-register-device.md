---
title: Registrace nového zařízení – Azure IoT Edge | Microsoft Docs
description: Registrace jednoho IoT Edge zařízení v IoT Hub pro ruční zřizování pomocí symetrických klíčů nebo certifikátů X. 509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200719"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrace zařízení IoT Edge v IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Tento článek popisuje postup, jak zaregistrovat nové zařízení IoT Edge v IoT Hub.

Každé zařízení, které se připojuje ke IoT Hub, má ID zařízení, které se používá ke sledování komunikace z cloudu na zařízení nebo ze zařízení do cloudu. Zařízení nakonfigurujete s informacemi o připojení, které zahrnují název hostitele IoT Hub, ID zařízení a informace, které zařízení používá k ověření IoT Hub.

Kroky v tomto článku vás provedou procesem, který se nazývá ruční zřizování, kde připojíte jedno zařízení ke službě IoT Hub. Pro ruční zřizování máte dvě možnosti ověřování IoT Edge zařízení:

* **Symetrický klíč**: když v IoT Hub vytvoříte novou identitu zařízení, služba vytvoří dva klíče. Do zařízení umístíte jeden z klíčů a při ověřování se zobrazí klíč, který se IoT Hub.

  Tato metoda ověřování je rychlejší, aby se dala začít, ale ne tak bezpečné.

* **X. 509 podepsaný svým držitelem**: vytvoříte dva certifikáty identit x. 509 a umístíte je na zařízení. Když v IoT Hub vytvoříte novou identitu zařízení, zadáváte kryptografické otisky z obou certifikátů. Když se zařízení ověří IoT Hub, zobrazí jeden certifikát a IoT Hub ověří, že certifikát odpovídá jeho kryptografickému otisku.

  Tato metoda ověřování je bezpečnější a doporučuje se pro produkční scénáře.

Tento článek se zabývá oběma metodami ověřování.

Pokud máte k dispozici mnoho zařízení a nechcete je ručně zřídit, použijte jeden z následujících článků, abyste se dozvěděli, jak IoT Edge pracuje s IoT Hub Device Provisioning Service:

* [Vytváření a zřizování IoT Edgech zařízení pomocí certifikátů X. 509](how-to-auto-provision-x509-certs.md)
* [Vytvoření a zřízení IoT Edgech zařízení pomocí čipu TPM](how-to-auto-provision-simulated-device-linux.md)
* [Vytvoření a zřízení IoT Edge zařízení pomocí symetrických klíčů](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Předpoklady

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) v předplatném Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) ve vašem prostředí. Verze Azure CLI musí být minimálně 2.0.70 nebo novější. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Možnost 1: registrace pomocí symetrických klíčů

Pomocí několika nástrojů můžete zaregistrovat nové IoT Edge zařízení v IoT Hub a načíst připojovací řetězec v závislosti na vaší předvolbách.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Ve službě IoT Hub v Azure Portal jsou IoT Edgeá zařízení vytvořená a spravovaná odděleně od zařízení IoT, která nejsou povolená přes Edge.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V levém podokně vyberte v nabídce **IoT Edge** a pak vyberte **Přidat IoT Edge zařízení**.

   ![Přidat zařízení IoT Edge z Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na stránce **vytvořit zařízení** zadejte následující informace:

   * Vytvořte popisné ID zařízení.
   * Jako typ ověřování vyberte **symetrický klíč** .
   * K automatickému generování ověřovacích klíčů a připojení nového zařízení k rozbočovači použijte výchozí nastavení.

1. Vyberte **Uložit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se, abyste měli přístup ke službě IoT Hub.

Pomocí rozšíření Azure IoT pro Visual Studio Code můžete provádět operace se službou IoT Hub. Aby tyto operace fungovaly, musíte se přihlásit ke svému účtu Azure a vybrat své centrum.

1. V Visual Studio Code otevřete zobrazení **Průzkumníka** .
1. V dolní části Průzkumníka rozbalte část **Azure IoT Hub** .

   ![Část rozšíření zařízení Azure IoT Hub](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud se tři tečky nevidí, klikněte nebo najeďte na záhlaví.
1. Zvolte **vybrat IoT Hub**.
1. Pokud nejste přihlášení ke svému účtu Azure, postupujte podle pokynů.
1. Vyberte své předplatné Azure.
1. Vyberte centrum IoT.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrace nového zařízení s Visual Studio Code

1. V Průzkumníku Visual Studio Code rozbalte část **Azure IoT Hub** .
1. Klikněte na **...** v hlavičce oddílu **Azure IoT Hub** . Pokud se tři tečky nevidí, klikněte nebo najeďte na záhlaví.
1. Vyberte **vytvořit IoT Edge zařízení**.
1. Do textového pole, které se otevře, zadejte ID zařízení.

Na obrazovce výstup vidíte výsledek příkazu. Budou vytištěny informace o zařízení, které obsahují **deviceId** , které jste zadali, a **připojovací řetězec** , který můžete použít k připojení fyzického zařízení k centru IoT.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ IoT Hub Device-identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) vytvořte novou identitu zařízení ve službě IoT Hub. Například:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tento příkaz obsahuje tři parametry:

* `--device-id` nebo `-d` : zadejte popisný název, který je jedinečný v rámci služby IoT Hub.
* `--hub-name` nebo `-n` : zadejte název centra IoT.
* `--edge-enabled` nebo `--ee` : deklarujte, že se jedná o zařízení IoT Edge.

   ![AZ IoT Hub Device-identity Create Output](./media/how-to-register-device/create-edge-device-cli.png)

---

Teď, když máte zařízení zaregistrované v IoT Hub, načtěte připojovací řetězec, který použijete k dokončení instalace a zřízení IoT Edge runtime. Podle kroků dále v tomto článku můžete [Zobrazit registrovaná zařízení a načíst připojovací řetězce](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Možnost 2: registrace pomocí certifikátů X. 509

Ruční zřizování s certifikáty X. 509 vyžaduje IoT Edge verze 1.0.10 nebo novější.

Pro ověřování pomocí certifikátu X. 509 jsou informace o ověřování každého zařízení k dispozici ve formě *kryptografických otisků* z certifikátů identit zařízení. Tyto kryptografické otisky se přidávají IoT Hub v době registrace zařízení, takže služba může zařízení rozpoznat při připojení.

### <a name="create-certificates-and-thumbprints"></a>Vytváření certifikátů a kryptografických otisků

Když zřídíte zařízení IoT Edge s certifikáty X. 509, použijete to, co se nazývá *certifikát identity zařízení*. Tento certifikát se používá jenom ke zřízení IoT Edge zařízení a ověřování zařízení s využitím Azure IoT Hub. Jedná se o listový certifikát, který nepodepisuje jiné certifikáty. Certifikát identity zařízení je oddělený od certifikátů certifikační autority (CA), které IoT Edge zařízení prezentuje modulům nebo zařízením pro ověřování. Další informace o tom, jak se certifikáty certifikační autority používají v zařízeních IoT Edge, najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

Pro ruční zřizování pomocí X. 509 potřebujete následující soubory:

* Dva certifikáty identity zařízení s odpovídajícími certifikáty privátních klíčů ve formátech. cer nebo. pem.

  IoT Edge modulu runtime se poskytne jedna sada souborů certifikátu nebo klíče. Při vytváření certifikátů identit zařízení nastavte běžný název certifikátu (CN) s ID zařízení, které má mít zařízení ve službě IoT Hub.

* Kryptografické otisky jsou pořízeny z certifikátů identit zařízení.

  Hodnoty kryptografických otisků jsou 40 hexadecimálních znaků pro hodnoty hash SHA-1 nebo 64-HEX znaků pro hodnoty hash SHA-256. Pro IoT Hub v době registrace zařízení jsou k dispozici oba kryptografické otisky.

Pokud nemáte k dispozici certifikáty, můžete [Vytvořit Ukázkové certifikáty pro testování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md). Podle pokynů v tomto článku nastavte skripty pro vytváření certifikátů, vytvořte certifikát kořenové certifikační autority a pak vytvořte dva IoT Edge certifikáty identit zařízení.

Jedním ze způsobů, jak načíst kryptografický otisk z certifikátu, je následující příkaz OpenSSL:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrace nového zařízení

Můžete použít několik nástrojů k registraci nového IoT Edge zařízení v IoT Hub a nahrání kryptografických otisků certifikátů.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Ve službě IoT Hub v Azure Portal jsou IoT Edgeá zařízení vytvořená a spravovaná odděleně od zařízení IoT, která nejsou povolená přes Edge.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V levém podokně vyberte v nabídce **IoT Edge** a pak vyberte **Přidat IoT Edge zařízení**.

   ![Přidat zařízení IoT Edge z Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na stránce **vytvořit zařízení** zadejte následující informace:

   * Vytvořte popisné ID zařízení. Poznamenejte si toto ID zařízení, jak ho budete používat v další části.
   * Jako typ ověřování vyberte **X. 509 podepsaný svým držitelem** .
   * Zadejte kryptografické otisky certifikátů primární a sekundární identity. Hodnoty kryptografických otisků jsou 40 hexadecimálních znaků pro hodnoty hash SHA-1 nebo 64-HEX znaků pro hodnoty hash SHA-256.

1. Vyberte **Uložit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Rozšíření Azure IoT pro Visual Studio Code v současné době nepodporuje registraci zařízení pomocí certifikátů X. 509.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ IoT Hub Device-identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) vytvořte novou identitu zařízení ve službě IoT Hub. Například:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Tento příkaz obsahuje několik parametrů:

* `--device-id` nebo `-d` : zadejte popisný název, který je jedinečný pro vaše centrum IoT. Poznamenejte si toto ID zařízení, jak ho budete používat v další části.
* `hub-name` nebo `-n` : zadejte název centra IoT.
* `--edge-enabled` nebo `--ee` : deklarujte, že se jedná o zařízení IoT Edge.
* `--auth-method` nebo `--am` : deklarujte typ autorizace, který zařízení bude používat. V tomto případě používáme kryptografické otisky certifikátů X. 509.
* `--primary-thumbprint` nebo `--ptp` : zadejte kryptografický otisk certifikátu X. 509, který se použije jako primární klíč.
* `--secondary-thumbprint` nebo `--stp` : zadejte kryptografický otisk certifikátu X. 509, který se použije jako sekundární klíč.

---

Teď, když máte zařízení zaregistrované v IoT Hub, jste připraveni nainstalovat a zřídit modul IoT Edge runtime na svém zařízení. IoT Edge zařízení, která se ověřují pomocí certifikátů X. 509, nepoužívají připojovací řetězce, takže můžete pokračovat k dalšímu kroku:

* [Instalace nebo odinstalace Azure IoT Edge pro Linux](how-to-install-iot-edge.md)
* [Instalace nebo odinstalace Azure IoT Edge pro Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Zobrazení registrovaných zařízení a načtení připojovacích řetězců

Zařízení, která používají ověřování pomocí symetrického klíče, potřebují připojovací řetězce k dokončení instalace a zřízení IoT Edge runtime.

Zařízení, která používají ověřování pomocí certifikátu X. 509, nepotřebují připojovací řetězce. Místo toho pro dokončení instalace a zřízení IoT Edge modulu runtime potřebují tato zařízení název svého centra IoT, jeho název a soubory certifikátů.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Všechna zařízení s povoleným okrajem, která se připojují ke službě IoT Hub, jsou uvedena na stránce **IoT Edge** .

![Pomocí Azure Portal můžete zobrazit všechna IoT Edge zařízení ve službě IoT Hub.](./media/how-to-register-device/portal-view-devices.png)

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

U zařízení, která se ověřují pomocí symetrických klíčů, jsou jejich připojovací řetězce dostupné ke kopírování na portálu.

1. Na stránce **IoT Edge** na portálu klikněte v seznamu IoT Edge zařízení na ID zařízení.
2. Zkopírujte hodnotu buď **primárního připojovacího řetězce** , nebo **sekundárního připojovacího řetězce**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Zobrazit IoT Edge zařízení s Visual Studio Code

Všechna zařízení, která se připojují ke službě IoT Hub, jsou uvedená v části **Azure IoT Hub** v Průzkumníkovi Visual Studio Code. Zařízení IoT Edge lze odlišit od jiných než hraničních zařízení s jinou ikonou a skutečnost, že moduly **$edgeAgent** a **$edgeHub** jsou nasazeny do každého IoT Edge zařízení.

![Pomocí VS Code můžete zobrazit všechna IoT Edge zařízení ve službě IoT Hub.](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Načtení připojovacího řetězce pomocí Visual Studio Code

Až budete připraveni k nastavení zařízení, potřebujete připojovací řetězec, který propojuje vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

1. V části **Azure IoT Hub** klikněte pravým tlačítkem na ID vašeho zařízení.
1. Vyberte **Kopírovat připojovací řetězec zařízení**.

   Připojovací řetězec je zkopírován do schránky.

Můžete také vybrat možnost **získat informace o zařízení** z nabídky kliknutím pravým tlačítkem myši a zobrazit všechny informace o zařízení, včetně připojovacího řetězce, v okně výstup.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

>[!TIP]
>`connection-string show`Příkaz se představil ve verzi 0.9.8 rozšíření Azure IoT a nahrazuje nepoužívané `show-connection-string` příkazy. Pokud se zobrazí chyba při spuštění tohoto příkazu, ujistěte se, že je vaše verze rozšíření aktualizována na 0.9.8 nebo novější. Další informace a nejnovější aktualizace najdete v tématu [Microsoft Azure rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Hodnota `device-id` parametru rozlišuje velká a malá písmena.

Při kopírování připojovacího řetězce, který se má použít v zařízení, nepoužívejte uvozovky kolem připojovacího řetězce.

---

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení zaregistrované v IoT Hub, jste připraveni nainstalovat a zřídit modul IoT Edge runtime na svém zařízení.

* [Instalace nebo odinstalace Azure IoT Edge pro Linux](how-to-install-iot-edge.md)
* [Instalace nebo odinstalace Azure IoT Edge pro Windows](how-to-install-iot-edge-windows-on-windows.md)