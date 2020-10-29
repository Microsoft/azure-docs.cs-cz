---
title: Zřízení pomocí certifikátů X. 509 – Azure IoT Edge | Microsoft Docs
description: Po instalaci zřiďte zařízení IoT Edge pomocí certifikátů identit zařízení a ověří IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: abb3aa9ca7c9697fef1cf456964154249f0d69f3
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913972"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Nastavení zařízení Azure IoT Edge s ověřováním pomocí certifikátu X. 509

Tento článek popisuje postup, jak zaregistrovat nové zařízení IoT Edge v IoT Hub a nakonfigurovat zařízení tak, aby se ověřilo pomocí certifikátů X. 509.

Kroky v tomto článku vás provedou procesem, který se nazývá ruční zřizování, kde se každé zařízení připojí ke službě IoT Hub ručně. Alternativa je Automatické zřizování pomocí IoT Hub Device Provisioning Service, což je užitečné, když máte mnoho zařízení, které je potřeba zřídit.

<!--TODO: Add auto-provision info/links-->

Pro ruční zřizování máte dvě možnosti ověřování IoT Edge zařízení:

* **Symetrický klíč** : když v IoT Hub vytvoříte novou identitu zařízení, služba vytvoří dva klíče. Do zařízení umístíte jeden z klíčů a při ověřování se zobrazí klíč, který se IoT Hub.

  Tato metoda ověřování je rychlejší, aby se dala začít, ale ne tak bezpečné.

* **X. 509 podepsaný svým držitelem** : vytvoříte dva certifikáty identit x. 509 a umístíte je na zařízení. Když v IoT Hub vytvoříte novou identitu zařízení, zadáváte kryptografické otisky z obou certifikátů. Když se zařízení ověřuje IoT Hub, prezentuje jeho certifikáty a IoT Hub může ověřit, jestli odpovídají kryptografickým otiskům.

  Tato metoda ověřování je bezpečnější a doporučuje se pro produkční scénáře.

Tento článek vás provede procesem registrace a zřízení s ověřováním pomocí certifikátu X. 509. Pokud se chcete dozvědět, jak nastavit zařízení pomocí symetrických klíčů, přečtěte si téma [nastavení Azure IoT Edge zařízení s ověřováním pomocí symetrického klíče](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Předpoklady

Než budete postupovat podle kroků v tomto článku, měli byste mít nainstalované zařízení s modulem runtime IoT Edge. V takovém případě postupujte podle pokynů v části [instalace nebo odinstalace modulu runtime Azure IoT Edge](how-to-install-iot-edge.md).

Ruční zřizování s certifikáty X. 509 vyžaduje IoT Edge verze 1.0.10 nebo novější.

## <a name="create-certificates-and-thumbprints"></a>Vytváření certifikátů a kryptografických otisků

Certifikát identity zařízení je listový certifikát, který se připojuje prostřednictvím řetězu certifikátů s nejvyšším certifikátem certifikační autority (CA) X. 509. Certifikát identity zařízení musí mít svůj běžný název (CN) nastavený na ID zařízení, které má mít zařízení ve službě IoT Hub.

Certifikáty identit zařízení se používají jenom ke zřízení IoT Edge zařízení a ověřování zařízení s využitím Azure IoT Hub. Nepodepisují certifikáty, na rozdíl od certifikátů certifikační autority, které IoT Edge zařízení prezentuje modulům nebo listovým zařízením k ověření. Další informace najdete v tématu informace o [využití certifikátu Azure IoT Edge](iot-edge-certs.md).

Po vytvoření certifikátu identity zařízení byste měli mít dva soubory: soubor. cer nebo. pem, který obsahuje veřejnou část certifikátu, a soubor. cer nebo. pem s privátním klíčem certifikátu.

Pro ruční zřizování pomocí X. 509 potřebujete následující soubory:

* Dvě sady certifikátů identit zařízení a privátních klíčů. IoT Edge modulu runtime se poskytne jedna sada souborů certifikátu nebo klíče.
* Kryptografické otisky jsou pořízeny z certifikátů identit zařízení. Hodnoty kryptografických otisků jsou 40 hexadecimálních znaků pro hodnoty hash SHA-1 nebo 64-HEX znaků pro hodnoty hash SHA-256. Pro IoT Hub v době registrace zařízení jsou k dispozici oba kryptografické otisky.

Pokud nemáte k dispozici certifikáty, můžete [Vytvořit Ukázkové certifikáty pro testování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md). Podle pokynů v tomto článku nastavte skripty pro vytváření certifikátů, vytvořte certifikát kořenové certifikační autority a pak vytvořte dva IoT Edge certifikáty identit zařízení.

Jedním ze způsobů, jak načíst kryptografický otisk z certifikátu, je následující příkaz OpenSSL:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-a-new-device"></a>Registrace nového zařízení

Každé zařízení, které se připojuje k IoT Hub, má ID zařízení, které se používá ke sledování komunikace z cloudu na zařízení nebo ze zařízení do cloudu. Nakonfigurujete zařízení s informacemi o připojení, které zahrnují název hostitele IoT Hub, ID zařízení a informace, které zařízení používá k ověření IoT Hub.

Pro ověřování pomocí certifikátu X. 509 se tyto informace poskytují ve formě *kryptografických otisků* z certifikátů identit zařízení. Tyto kryptografické otisky se přidávají IoT Hub v době registrace zařízení, takže služba může zařízení rozpoznat při připojení.

Můžete použít několik nástrojů k registraci nového IoT Edge zařízení v IoT Hub a nahrání kryptografických otisků certifikátů.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Předpoklady pro Azure Portal

Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Vytvoření zařízení IoT Edge v Azure Portal

V IoT Hub v Azure Portal se zařízení IoT Edge vytváří a spravují odděleně od zařízení IOT, která nejsou povolená Edge.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V levém podokně vyberte v nabídce **IoT Edge** a pak vyberte **Přidat IoT Edge zařízení** .

   ![Přidat zařízení IoT Edge z Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na stránce **vytvořit zařízení** zadejte následující informace:

   * Vytvořte popisné ID zařízení. Poznamenejte si toto ID zařízení, jak ho budete používat v další části.
   * Jako typ ověřování vyberte **X. 509 podepsaný svým držitelem** .
   * Zadejte kryptografické otisky certifikátů primární a sekundární identity. Hodnoty kryptografických otisků jsou 40 hexadecimálních znaků pro hodnoty hash SHA-1 nebo 64-HEX znaků pro hodnoty hash SHA-256.

1. Vyberte **Uložit** .

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Zobrazit IoT Edge zařízení v Azure Portal

Všechna zařízení s povoleným okrajem, která se připojují ke službě IoT Hub, jsou uvedena na stránce **IoT Edge** .

![Zobrazit všechna IoT Edge zařízení ve službě IoT Hub](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Předpoklady pro rozhraní příkazového řádku Azure

* [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure.
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) ve vašem prostředí. Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az --version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack.
* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension)

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Vytvoření zařízení IoT Edge pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) vytvořte novou identitu zařízení ve službě IoT Hub. Příklad:

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

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Zobrazení IoT Edge zařízení pomocí Azure CLI

Pomocí příkazu [AZ IoT Hub Device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) zobrazíte všechna zařízení ve službě IoT Hub. Příklad:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Přidejte příznak `--edge-enabled` nebo `--ee` k vypsání IoT Edge zařízení ve službě IoT Hub.

Všechna zařízení, která jsou zaregistrovaná jako zařízení IoT Edge, budou mít **Možnosti vlastností. iotEdge** nastaveno na **hodnotu true** .

--- 

## <a name="configure-an-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Jakmile má IoT Edge zařízení v IoT Hub identitu, musíte zařízení nakonfigurovat s jeho cloudovou identitou a také s jeho certifikáty identit.

Na zařízení se systémem Linux zadáte tyto informace úpravou souboru config. yaml. Na zařízení s Windows zadáte tyto informace spuštěním skriptu PowerShellu.

# <a name="linux"></a>[Linux](#tab/linux)

1. Na zařízení IoT Edge otevřete konfigurační soubor.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. V souboru vyhledejte část konfigurace zřizování. 

1. Odkomentujte **ruční konfiguraci zřizování pomocí oddílu připojovacího řetězce** .

1. Odkomentujte **ruční konfiguraci zřizování pomocí oddílu certifikátu X. 509 identity** . Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Aktualizujte následující pole:

   * **iothub_hostname** : název hostitele IoT Hub, ke kterému se bude zařízení připojovat. Například, `{IoT hub name}.azure-devices.net`.
   * **device_ID** : ID, které jste zadali při registraci zařízení.
   * **identity_cert** : identifikátor URI certifikátu identity na zařízení. Například, `file:///path/identity_certificate.pem`.
   * **identity_pk** : identifikátor URI pro soubor privátního klíče pro poskytnutý certifikát identity. Například, `file:///path/identity_key.pem`.

1. Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

1. Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. Na zařízení IoT Edge spusťte PowerShell jako správce.

2. Pomocí příkazu [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) nakonfigurujte na svém počítači modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Pokud používáte kontejnery platformy Linux, přidejte `-ContainerOs` do příznaku parametr. Být konzistentní s možností kontejneru, kterou jste zvolili pomocí `Deploy-IoTEdge` příkazu, který jste předtím spustili.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Pokud jste do svého zařízení stáhli IoTEdgeSecurityDaemon.ps1 skript pro instalaci offline nebo konkrétní verze, nezapomeňte na místní kopii skriptu odkazovat.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po zobrazení výzvy zadejte následující informace:

   * **IotHubHostName** : název hostitele IoT Hub, ke kterému se bude zařízení připojovat. Například, `{IoT hub name}.azure-devices.net`.
   * **DeviceID** : ID, které jste zadali při registraci zařízení.
   * **X509IdentityCertificate** : absolutní cesta k certifikátu identity na zařízení. Například, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey** : absolutní cesta k souboru privátního klíče pro poskytnutý certifikát identity. Například, `C:\path\identity_key.pem`.

Při ručním zřizování zařízení můžete pomocí dalších parametrů upravit proces, včetně:

* Směrování provozu pomocí proxy server
* Deklarovat konkrétní image kontejneru edgeAgent a zadat přihlašovací údaje, pokud se nachází v privátním registru

Další informace o těchto dalších parametrech najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Další kroky

Pokračujte v [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) a Naučte se, jak nasadit moduly do svého zařízení.