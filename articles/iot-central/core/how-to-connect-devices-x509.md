---
title: Připojení zařízení pomocí certifikátů X. 509 v aplikaci Azure IoT Central
description: Postup připojení zařízení pomocí certifikátů X. 509 pomocí sady SDK pro Node.js zařízení pro IoT Central aplikace
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: c2af331304decd7955892ef4911d1644518f57b8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427891"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Postup připojení zařízení pomocí certifikátů X. 509 pomocí sady SDK pro Node.js zařízení pro IoT Central aplikace

IoT Central podporuje certifikáty sdíleného přístupu (SAS) i X. 509 k zabezpečení komunikace mezi zařízením a vaší aplikací. V kurzu [Vytvoření a připojení klientské aplikace do Azure IoT Central aplikace se](./tutorial-connect-device-nodejs.md) používá SAS. V tomto článku se naučíte, jak upravit ukázku kódu pro použití X. 509.  V produkčním prostředí se doporučují certifikáty X. 509. Další informace najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md).

Tento článek ukazuje dva způsoby použití registrů X. 509- [Group](how-to-connect-devices-x509.md#use-a-group-enrollment) , které se obvykle používají v produkčním prostředí, a [jednotlivé registrace](how-to-connect-devices-x509.md#use-an-individual-enrollment) užitečné pro testování.

## <a name="prerequisites"></a>Požadavky

- Dokončení [Vytvoření a připojení klientské aplikace do kurzu Azure IoT Central aplikace (Node.js)](./tutorial-connect-device-nodejs.md) .
- [Git](https://git-scm.com/download/).
- Stáhněte a nainstalujte [OpenSSL](https://www.openssl.org/). Pokud používáte systém Windows, můžete použít binární soubory ze [stránky OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Použití skupinové registrace

V produkčním prostředí můžete použít certifikáty X. 509 se zápisem skupiny. V případě registrace skupiny přidáte do aplikace IoT Central kořenový nebo zprostředkující certifikát X. 509. Zařízení s listovým certifikátem odvozeným z kořenového nebo zprostředkujícího certifikátu se můžou připojit k vaší aplikaci.

## <a name="generate-root-and-device-cert"></a>Generovat kořenový certifikát a certifikát zařízení

V této části pomocí certifikátu X. 509 připojíte zařízení s certifikátem odvozeným z certifikátu skupiny registrací, který se může připojit k vaší aplikaci IoT Central.

> [!WARNING]
> Tento způsob generování certifikátů X. 509 slouží pouze k testování. V produkčním prostředí byste měli použít svůj oficiální a zabezpečený mechanismus pro generování certifikátů.

1. Otevřete příkazový řádek. Naklonujte úložiště GitHub pro skripty generování certifikátů:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Přejděte do skriptu generátoru certifikátů a nainstalujte požadované balíčky:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Vytvořte kořenový certifikát a potom odvodit certifikát zařízení spuštěním skriptu:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

    > [!TIP]
    > ID zařízení může obsahovat písmena, číslice a `-` znak.

Tyto příkazy vytváří tři soubory pro kořenový adresář a certifikát zařízení.

filename | obsah
-------- | --------
\<name\>_cert. pem | Veřejná část certifikátu x509
\<name\>_key. pem | Privátní klíč pro certifikát x509
\<name\>_fullchain. pem | Celý řetězec klíčů pro certifikát x509.

## <a name="create-a-group-enrollment"></a>Vytvoření registrace skupiny

1. Otevřete aplikaci IoT Central a v levém podokně přejděte do části **Správa**  a vyberte **připojení zařízení**.

1. Vyberte **+ vytvořit skupinu**registrací a vytvořte novou skupinu registrací s názvem _MyX509Group_ s typem ověření identity **certifikáty (X. 509)**.

1. Otevřete skupinu pro registraci, kterou jste vytvořili, a vyberte **Spravovat primární**.

1. Vyberte možnost soubor a nahrajte soubor kořenového certifikátu s názvem _mytestrootcert_cert. pem_ , který jste předtím vygenerovali:

    ![Nahrávání certifikátu](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Ověření dokončíte tak, že vygenerujete ověřovací kód, zkopírujete ho a pak ho použijete k vytvoření ověřovacího certifikátu X. 509 na příkazovém řádku:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Nahrajte podepsaný certifikát pro ověření _verification_cert. pem_ k dokončení ověření:

    ![Ověřený certifikát](./media/how-to-connect-devices-x509/verified.png)

Teď můžete připojit zařízení, která mají certifikát X. 509 odvozený od tohoto primárního kořenového certifikátu. Po uložení skupiny registrace si poznamenejte rozsah ID.

## <a name="run-sample-device-code"></a>Spustit ukázkový kód zařízení

1. V aplikaci Azure IoT Central vyberte **zařízení**a v šabloně zařízení **snímače prostředí** vytvořte nové zařízení s _mytestdevice_ jako **ID zařízení** .

1. Zkopírujte soubory _mytestdevice_key. pem_ a _mytestdevice_cert. pem_ do složky, která obsahuje aplikaci _environmentalSensor.js_ . Tuto aplikaci jste vytvořili, když jste dokončili [kurz připojení zařízení (Node.js)](./tutorial-connect-device-nodejs.md).

1. Přejděte do složky, která obsahuje aplikaci environmentalSensor.js a spusťte následující příkaz pro instalaci balíčku X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Upravte soubor **environmentalSensor.js** .
    - Nahraďte `idScope` hodnotu **rozsahem ID** , na které jste si poznamenali dříve.
    - Nahraďte `registrationId` hodnotu hodnotou `mytestdevice` .

1. Příkazy upravte takto `require` :

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Upravte oddíl, který vytváří klienta, následovně:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

1. Upravte část, která otevře připojení, následujícím způsobem:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. Spusťte skript a ověřte, jestli se zařízení úspěšně zřídilo:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Můžete také ověřit, že se telemetrie zobrazí na řídicím panelu.

    ![Ověření telemetrie zařízení](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Použít jednotlivou registraci

Pomocí certifikátů X. 509 s jednotlivými registracemi otestujete své zařízení a řešení. V rámci individuální registrace neexistuje v IoT Central aplikaci žádný kořenový nebo zprostředkovaný certifikát X. 509. Zařízení používají k připojení k vaší aplikaci certifikát X. 509 podepsaný svým držitelem.

## <a name="generate-self-signed-device-cert"></a>Generovat certifikát zařízení podepsaného svým držitelem

V této části použijete certifikát X. 509 podepsaný svým držitelem k připojení zařízení k jednotlivým zápisům, které se používají k registraci jednoho zařízení. Certifikáty podepsané svým držitelem jsou pouze pro testování.

Spuštěním skriptu vytvořte certifikát zařízení X. 509 podepsaný svým držitelem. Nezapomeňte použít pouze malé alfanumerické znaky a spojovníky pro název certifikátu:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Vytvořit jednotlivou registraci

1. V aplikaci Azure IoT Central vyberte **zařízení**a vytvořte nové zařízení s **ID zařízení** jako _mytestselfcertprimary_ ze šablony zařízení snímače prostředí. Poznamenejte si **Rozsah ID**a použijte ho později.

1. Otevřete zařízení, které jste vytvořili, a vyberte **připojit**.

1. Jako mechanismus vyberte **jednotlivé registrace** jako **metodu připojení** a **certifikáty (X. 509)** :

    ![Jednotlivé registrace](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. V části primární vyberte možnost soubor a nahrajte soubor certifikátu s názvem _mytestselfcertprimary_cert. pem_ , který jste předtím vygenerovali.

1. Vyberte možnost soubor pro sekundární certifikát a nahrajte soubor certifikátu s názvem _mytestselfcertsecondary_cert. pem._ Pak vyberte **Uložit**:

    ![Nahrání individuálního certifikátu zápisu](./media/how-to-connect-devices-x509/individual-enrollment.png)

Zařízení je teď zřízené pomocí certifikátu X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Spuštění ukázkového samostatného registračního zařízení

1. Zkopírujte soubory _mytestselfcertprimary_key. pem_ a _mytestselfcertprimary_cert. pem_ do složky, která obsahuje aplikaci environmentalSensor.js. Tuto aplikaci jste vytvořili, když jste dokončili [kurz připojení zařízení (Node.js)](./tutorial-connect-device-nodejs.md).

1. Upravte soubor **environmentalSensor.js** následujícím způsobem a uložte ho.
    - Nahraďte `idScope` hodnotu **rozsahem ID** , na které jste si poznamenali dříve.
    - Nahraďte `registrationId` hodnotu hodnotou `mytestselfcertprimary` .
    - Nahraďte **var deviceCert** jako:

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. Spusťte skript a ověřte, jestli se zařízení úspěšně zřídilo:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Můžete také ověřit, že se telemetrie zobrazí na řídicím panelu.

    ![Jednotlivá registrace telemetrie](./media/how-to-connect-devices-x509/telemetry-primary.png)

Výše uvedený postup můžete opakovat i pro _mytestselfcertsecondary_ certifikát.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili připojit zařízení pomocí certifikátů X. 509, je navržený další krok, kde se dozvíte, jak [monitorovat připojení zařízení pomocí Azure CLI](howto-monitor-devices-azure-cli.md) .
