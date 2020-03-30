---
title: Úvodní příručka – zřízení simulovaných zařízení TPM do služby Azure IoT Hub pomocí souboru Node.js
description: Úvodní příručka – vytvoření a zřízení simulované zařízení TPM pomocí sady SDK zařízení Node.js pro službu DPS centra Azure IoT Hub. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 93e68246d1c978bdb1517922f0284524395c218a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605478"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Úvodní příručka: Vytvoření a zřízení simulovaného zařízení TPM pomocí sady SDK zařízení Node.js pro službu zřizování zařízení služby IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

V tomto rychlém startu vytvoříte simulované zařízení IoT v počítači se systémem Windows. Simulované zařízení obsahuje simulátor Čipu TPM jako modul hardwarového zabezpečení (HSM). Ke připojení tohoto simulovaného zařízení k centru IoT pomocí individuální registrace pomocí služby Device Provisioning Service (DPS) se používá ukázkový kód Node.js.

## <a name="prerequisites"></a>Požadavky

- Přehled [konceptů automatického zřizování](concepts-auto-provisioning.md).
- Dokončení [nastavení služby zřizování zařízení služby IoT Hub na webu Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že na svém počítači máte nainstalované [Node.js v4.0 nebo novější](https://nodejs.org).

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 


## <a name="simulate-a-tpm-device"></a>Simulace zařízení TPM

1. Otevřete příkazový řádek nebo Git Bash. Naklonujte úložiště GitHub `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Přejděte do kořenové složky GitHub a spusťte simulátor [TPM,](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) abyste byli pro simulované zařízení [hsm.](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) Ten naslouchá přes soket na portech 2321 a 2322. Nezavírejte toto příkazové okno. musíte udržovat tento simulátor spuštěn až do konce tohoto průvodce rychlým startem: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Vytvořte novou prázdnou složku s názvem **registerdevice**. Ve složce **registerdevice** vytvořte soubor package.json pomocí následujícího příkazu na příkazovém řádku. Nezapomeňte zodpovědět všechny otázky, které `npm` zobrazí, nebo přijměte výchozí hodnoty, pokud vám vyhovují:
   
    ```cmd/sh
    npm init
    ```

1. Nainstalujte následující balíčky prekurzorů:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Při instalaci výše uvedených balíčků dochází k několika známým problémům. Tyto problémy vyřešíte tak, že na příkazovém řádku v režimu **Spustit jako správce** spustíte příkaz `npm install --global --production windows-build-tools`, pak spustíte příkaz `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140`, ve kterém použijete cestu k vaší nainstalované verzi, a pak znovu spustíte výše uvedené příkazy pro instalaci.
    >

1. Nainstalujte následující balíčky, které obsahují komponenty používané během instalace:

   - Klient zabezpečení, který funguje s TPM: `azure-iot-security-tpm`
   - Přenos umožňující připojení zařízení ke službě Device Provisioning: `azure-iot-provisioning-device-http` nebo `azure-iot-provisioning-device-amqp`
   - Klient, který použije přenos a klienta zabezpečení: `azure-iot-provisioning-device`

     Jakmile bude zařízení zaregistrované, můžete pomocí obvyklých klientských balíčků zařízení IoT Hub připojit zařízení s použitím přihlašovacích údajů, které jste zadali při registraci. Budete potřebovat:

   - Klienta zařízení: `azure-iot-device`
   - Přenos: `azure-iot-device-amqp`, `azure-iot-device-mqtt` nebo `azure-iot-device-http`
   - Klienta zabezpečení, kterého jste už nainstalovali: `azure-iot-security-tpm`

     > [!NOTE]
     > V níže uvedených ukázkách se používají přenosy `azure-iot-provisioning-device-http` a `azure-iot-device-mqtt`.
     > 

     Všechny tyto balíčky můžete nainstalovat najednou spuštěním následujícího příkazu na příkazovém řádku ve složce **registerdevice**:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Pomocí textového editoru vytvořte ve složce **registerdevice** nový soubor **ExtractDevice.js**.

1. Na začátek souboru **ExtractDevice.js** přidejte následující příkazy `require`:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Přidejte následující funkci, která metodu implementuje:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Uložte a zavřete soubor **ExtractDevice.js**. Spusťte ukázku:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. Ve výstupním okně se zobrazí **_klíč potvrzení_** a **_ID registrace_** potřebné pro registraci zařízení. Poznamenejte si tyto hodnoty. 


## <a name="create-a-device-entry"></a>Vytvoření položky zařízení

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace](concepts-service.md#individual-enrollment): Používá se k registraci jednoho zařízení.

Tento článek ukazuje jednotlivé zápisy.

1. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete službu Zřizování zařízení.

1. V nabídce Služba zřizování zařízení vyberte **Spravovat registrace**. Vyberte **kartu Jednotlivé registrace** a nahoře vyberte tlačítko Přidat jednotlivé **zápisy.** 

1. V panelu **Přidat zápis** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **TPM**.
   - Zadejte *ID registrace* a *ověřovací klíč* pro zařízení TPM z hodnot, které jste si dříve poznamenali.
   - Vyberte centrum IoT propojené s vaší zřizovací službou.
   - Volitelně můžete zadat následující informace:
       - Zadejte jedinečné *ID zařízení*. Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. Pokud se rozhodnete neposkytnout jeden, id registrace se použije k identifikaci zařízení místo.
       - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení stiskněte tlačítko **Uložit.** 

     ![Zadání informací o registraci zařízení v okně portálu](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Po úspěšné registraci se *ID registrace* vašeho zařízení zobrazí v seznamu na kartě *Jednotlivé registrace*. 


## <a name="register-the-device"></a>Registrace zařízení

1. Na webu Azure Portal vyberte okno **Přehled** pro službu Zřizování zařízení a poznamenejte si hodnoty **_globálního koncového bodu zařízení_** a oboru **_ID._**

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Pomocí textového editoru vytvořte ve složce **registerdevice** nový soubor **RegisterDevice.js**.

1. Na začátek souboru **RegisterDevice.js** přidejte následující příkazy `require`:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > Sada **Azure IoT SDK pro Node.js** podporuje i další protokoly jako _AMQP_, _AMQP WS_ a _MQTT WS_.  Další příklady najdete v [ukázkách pro sadu SDK služby Device Provisioning pro Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Přidejte proměnné **globalDeviceEndpoint** a **idScope** a použijte je k vytvoření instance **ProvisioningDeviceClient**. Nahraďte **{globalDeviceEndpoint}** a **{idScope}** hodnotami **_globálního koncového bodu zařízení_** a **_rozsahu ID_** z **kroku 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Přidejte následující funkci, která metodu implementuje na zařízení:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Uložte a zavřete soubor **RegisterDevice.js**. Spusťte ukázku:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT. Při úspěšném zřizování simulovaného zařízení do centra IoT propojeného s vaší zřizovací službou se ID zařízení zobrazí v okně **zařízení IoT** centra. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete [v tématu Principy a používání dvojčat zařízení v centru IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. Otevřete okno **Spravovat registrace** pro vaši službu a pak zaškrtněte kartu **Jednotlivé registrace.** Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. Otevřete okno **zařízení IoT** pro váš rozbočovač, zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v počítači vytvořili simulované zařízení TPM a zřídíte ho do služby IoT hub pomocí služby Zřizování zařízení služby IoT Hub. Chcete-li se dozvědět, jak zaregistrovat zařízení Čipové čipové čipy programově, pokračujte na úvodním startu pro programovou registraci zařízení TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení TPM do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)
