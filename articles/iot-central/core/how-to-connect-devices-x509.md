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
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378328"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Postup připojení zařízení pomocí certifikátů X. 509 pomocí sady SDK pro Node.js zařízení pro IoT Central aplikace

IoT Central podporuje certifikáty sdíleného přístupu (SAS) i X. 509 k zabezpečení komunikace mezi zařízením a vaší aplikací. V kurzu [Vytvoření a připojení klientské aplikace do Azure IoT Central aplikace se](./tutorial-connect-device.md) používá SAS. V tomto článku se naučíte, jak upravit ukázku kódu pro použití X. 509.  V produkčním prostředí se doporučují certifikáty X. 509. Další informace najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md).

Tento článek ukazuje dva způsoby použití registrů X. 509- [Group](how-to-connect-devices-x509.md#use-a-group-enrollment) , které se obvykle používají v produkčním prostředí, a [jednotlivé registrace](how-to-connect-devices-x509.md#use-an-individual-enrollment) užitečné pro testování.

Fragmenty kódu v tomto článku používají JavaScript. Ukázky kódu v jiných jazycích najdete v těchto tématech:

- [R](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>Požadavky

- Dokončení [Vytvoření a připojení klientské aplikace do kurzu Azure IoT Central Application (JavaScript)](./tutorial-connect-device.md) .
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
    node create_test_cert.js device sample-device-01 mytestrootcert
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

1. Vyberte **+ vytvořit skupinu** registrací a vytvořte novou skupinu registrací s názvem _MyX509Group_ s typem ověření identity **certifikáty (X. 509)**.

1. Otevřete skupinu pro registraci, kterou jste vytvořili, a vyberte **Spravovat primární**.

1. Vyberte možnost soubor a nahrajte soubor kořenového certifikátu s názvem _mytestrootcert_cert. pem_ , který jste předtím vygenerovali:

    ![Nahrávání certifikátu](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Ověření dokončíte tak, že vygenerujete ověřovací kód, zkopírujete ho a pak ho použijete k vytvoření ověřovacího certifikátu X. 509 na příkazovém řádku:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Nahrajte podepsaný certifikát pro ověření _verification_cert. pem_ k dokončení ověření:

    ![Ověřený certifikát](./media/how-to-connect-devices-x509/verified.png)

Teď můžete připojit zařízení, která mají certifikát X. 509 odvozený od tohoto primárního kořenového certifikátu.

Po uložení skupiny registrace si poznamenejte rozsah ID.

## <a name="run-sample-device-code"></a>Spustit ukázkový kód zařízení

1. Zkopírujte soubory **sampleDevice01_key. pem** a **sampleDevice01_cert. pem** do složky _Azure-IoT-SDK-Node/Device/Samples/pnp_ , která obsahuje aplikaci **simple_thermostat.js** . Tuto aplikaci jste použili po dokončení [kurzu připojit zařízení (JavaScript)](./tutorial-connect-device.md).

1. Přejděte do složky _Azure-IoT-SDK-Node/Device/Samples/PNP_ , která obsahuje aplikaci **simple_thermostat.js** a spusťte následující příkaz pro instalaci balíčku X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Otevřete soubor **simple_thermostat.js** v textovém editoru.

1. Upravte `require` příkazy tak, aby obsahovaly následující:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Přidejte následující čtyři řádky do oddílu "informace o připojení ke službě DPS" pro inicializaci `deviceCert` proměnné:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Úpravou `provisionDevice` funkce, která vytvoří klienta, nahraďte první řádek následujícím:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Ve stejné funkci upravte řádek, který nastaví `deviceConnectionString` proměnnou následujícím způsobem:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Do `main` funkce přidejte následující řádek za řádek, který volá `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

1. Ve vašem prostředí prostředí nastavte následující dvě proměnné prostředí:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > Další požadované proměnné prostředí nastavíte po dokončení kurzu [Vytvoření a připojení klientské aplikace do aplikace Azure IoT Central](./tutorial-connect-device.md) .

1. Spusťte skript a ověřte, jestli se zařízení úspěšně zřídilo:

    ```cmd/sh
    node simple_thermostat.js
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

1. V aplikaci Azure IoT Central vyberte **zařízení** a vytvořte nové zařízení s **ID zařízení** jako _mytestselfcertprimary_ ze šablony termostatu zařízení. Poznamenejte si **Rozsah ID** a použijte ho později.

1. Otevřete zařízení, které jste vytvořili, a vyberte **připojit**.

1. Jako mechanismus vyberte **jednotlivé registrace** jako **metodu připojení** a **certifikáty (X. 509)** :

    ![Jednotlivé registrace](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. V části primární vyberte možnost soubor a nahrajte soubor certifikátu s názvem _mytestselfcertprimary_cert. pem_ , který jste předtím vygenerovali.

1. Vyberte možnost soubor pro sekundární certifikát a nahrajte soubor certifikátu s názvem _mytestselfcertsecondary_cert. pem._ Pak vyberte **Uložit**:

    ![Nahrání individuálního certifikátu zápisu](./media/how-to-connect-devices-x509/individual-enrollment.png)

Zařízení je teď zřízené pomocí certifikátu X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Spuštění ukázkového samostatného registračního zařízení

1. Zkopírujte soubory _mytestselfcertprimary_key. pem_ a _mytestselfcertprimary_cert. pem_ do složky _Azure-IoT-SDK-Node/Device/Samples/pnp_ , která obsahuje aplikaci **simple_thermostat.js** . Tuto aplikaci jste použili po dokončení [kurzu připojit zařízení (JavaScript)](./tutorial-connect-device.md).

1. Upravte proměnné prostředí, které jste použili v ukázce výše, takto:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
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
