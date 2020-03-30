---
title: Registrace zařízení TPM do služby Azure Device Provisioning Service pomocí souboru Node.js
description: Úvodní příručka – registrace zařízení TPM do služby Azure IoT Hub Device Provisioning Service (DPS) pomocí sady SDK služby Node.js. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: e21aaa20edf6d3a2f690bf9f77e8c9973a7b1c52
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77604924"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Úvodní příručka: Registrace zařízení TPM do služby Zřizování zařízení služby IoT Hub pomocí sady SDK služby Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

V tomto rychlém startu programově vytvoříte individuální registraci pro zařízení TPM ve službě Azure IoT Hub Device Provisioning Service pomocí sady Node.js Service SDK a ukázkové aplikace Node.js. Volitelně můžete simulované zařízení TPM zaregistrovat do služby zřizování pomocí této položky jednotlivé registrace.

## <a name="prerequisites"></a>Požadavky

- Dokončení [nastavení služby Zřizování zařízení služby IoT Hub pomocí portálu Azure](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org). Tento rychlý start nainstaluje sada [SDK služby Node.js](https://github.com/Azure/azure-iot-sdk-node) níže.
- Ověřovací klíč (nepovinné). Postupujte podle kroků v [tématu Vytvoření a zřízení simulovaného zařízení,](quick-create-simulated-device.md) dokud nezískáte klíč. Nevytvářejte individuální registraci pomocí portálu Azure.

## <a name="create-the-individual-enrollment-sample"></a>Vytvoření ukázky jednotlivé registrace 

 
1. Z příkazového okna ve vaší pracovní složce spusťte:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Pomocí textového editoru ve své pracovní složce vytvořte soubor **create_individual_enrollment.js**. Přidejte do souboru následující kód a uložte ho:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Spuštění ukázky jednotlivé registrace
  
1. Ke spuštění ukázky potřebujete připojovací řetězec pro vaši službu zřizování. 
    1. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete službu Zřizování zařízení. 
    2. Vyberte **Zásady sdíleného přístupu**a vyberte zásady přístupu, které chcete použít k otevření jeho vlastností. V okně **Zásady přístupu** si zkopírujte a poznamenejte primární připojovací řetězec klíče. 

       ![Získání připojovacího řetězce služby zřizování z portálu](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Potřebujete také ověřovací klíč pro vaše zařízení. Pokud jste vytvořili simulované zařízení TPM pomocí postupu v rychlém startu [Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md), použijte klíč vytvořený pro toto zařízení. V opačném případě můžete vytvořit ukázkový individuální zápis pomocí následujícího ověřovacího klíče dodávaného se [sadou SDK služby Node.js](https://github.com/Azure/azure-iot-sdk-node):

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Pokud chcete vytvořit jednotlivou registraci pro vaše zařízení TPM, spusťte následující příkaz (včetně uvozovek okolo argumentů příkazu):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Po úspěšném vytvoření se v příkazovém okně zobrazí vlastnosti nové jednotlivé registrace.

    ![Vlastnosti registrace ve výstupu příkazu](./media/quick-enroll-device-tpm-node/output.png) 

4. Ověřte vytvoření jednotlivé registrace. Na webu Azure Portal v okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Vyberte kartu **Individuální zápisy** a vyberte novou položku zápisu (*první),* chcete-li ověřit klíč potvrzení a další vlastnosti položky.

    ![Vlastnosti registrace na portálu](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Právě jste vytvořili jednotlivou registraci pro zařízení TPM. Pokud chcete zaregistrovat simulované zařízení, můžete pokračovat ve zbývajících krocích v tématu [Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md). Nezapomeňte přeskočit kroky k vytvoření individuální registrace pomocí portálu Azure v tomto rychlém startu.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud máte v plánu prozkoumat ukázky služby Node.js, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Node.js.
1. Pokud jste vytvořili simulované zařízení TPM, zavřete okno simulátoru TPM.
2. Přejděte na azure portálu do **služby**Device Provisioning, vyberte Spravovat registrace a pak vyberte kartu **Individuální registrace.** Zaškrtněte políčko vedle *ID registrace* pro položku registrace, kterou jste vytvořili pomocí tohoto rychlého startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
 
## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste programově vytvořili individuální položku registrace pro zařízení TPM a volitelně jste ve vašem počítači vytvořili simulované zařízení TPM a zřídíte ho do vašeho centra IoT hub pomocí služby Azure IoT Hub Device Provisioning Service. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 
 
> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)