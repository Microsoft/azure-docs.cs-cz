---
title: Enroll TPM device to Azure Device Provisioning Service using Node.js
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Node.js service SDK. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 890ad28d99bfc53fa8a3fb40caf0469b31aeee61
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422990"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Node.js service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Tyto kroky ukazují, jak prostřednictvím kódu programu vytvořit jednotlivou registraci pro simulované zařízení TPM ve službě Azure IoT Hub Device Provisioning pomocí sady [SDK služby pro Node.js](https://github.com/Azure/azure-iot-sdk-node) a ukázkové aplikace v Node.js. Volitelně můžete simulované zařízení TPM zaregistrovat do služby zřizování pomocí této položky jednotlivé registrace. Přestože tento postup bude fungovat na počítačích s Windows i Linuxem, pro účely tohoto článku budeme používat vývojový počítač s Windows.

## <a name="prerequisites"></a>Předpoklady

- Než budete pokračovat, nezapomeňte dokončit kroky v tématu [Nastavení služby IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md). 
-  Ujistěte se, že na svém počítači máte nainstalované [Node.js v4.0 nebo novější](https://nodejs.org).
- If you want to enroll a simulated device at the end of this quickstart, follow the steps in [Create and provision a simulated device](quick-create-simulated-device.md) up until the step where you get an endorsement key for the device. Note down the endorsement key, you will use it later in this quickstart. **Neprovádějte kroky k vytvoření jednotlivé registrace pomocí webu Azure Portal.**
 
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
    1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service. 
    2. Select **Shared access policies**, then select the access policy you want to use to open its properties. V okně **Zásady přístupu** si zkopírujte a poznamenejte primární připojovací řetězec klíče. 

       ![Získání připojovacího řetězce služby zřizování z portálu](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Potřebujete také ověřovací klíč pro vaše zařízení. Pokud jste vytvořili simulované zařízení TPM pomocí postupu v rychlém startu [Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md), použijte klíč vytvořený pro toto zařízení. Jinak můžete k vytvoření ukázkové jednotlivé registrace použít následující ověřovací klíč, který je součástí sady SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Pokud chcete vytvořit jednotlivou registraci pro vaše zařízení TPM, spusťte následující příkaz (včetně uvozovek okolo argumentů příkazu):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Po úspěšném vytvoření se v příkazovém okně zobrazí vlastnosti nové jednotlivé registrace.

    ![Vlastnosti registrace ve výstupu příkazu](./media/quick-enroll-device-tpm-node/output.png) 

4. Ověřte vytvoření jednotlivé registrace. Na webu Azure Portal v okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Select the **Individual Enrollments** tab and select the new enrollment entry (*first*) to verify the endorsement key and other properties for the entry.

    ![Vlastnosti registrace na portálu](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Právě jste vytvořili jednotlivou registraci pro zařízení TPM. Pokud chcete zaregistrovat simulované zařízení, můžete pokračovat ve zbývajících krocích v tématu [Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md). Be sure to skip the steps to create an individual enrollment using the Azure portal in that quickstart.

## <a name="clean-up-resources"></a>Vyčištění prostředků
If you plan to explore the Node.js service samples, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Zavřete na svém počítači okno výstupu ukázky v Node.js.
1. Pokud jste vytvořili simulované zařízení TPM, zavřete okno simulátoru TPM.
2. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the check box next to the *Registration ID* for the enrollment entry you created using this quickstart, and press the **Delete** button at the top of the pane. 
 
## <a name="next-steps"></a>Další kroky
In this quickstart, you’ve programmatically created an individual enrollment entry for a TPM device, and, optionally, created a TPM simulated device on your machine and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 
 
> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)