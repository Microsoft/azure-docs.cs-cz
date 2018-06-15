---
title: Registrace zařízení X.509 do služby Azure Device Provisioning pomocí Node.js | Dokumentace Microsoftu
description: Rychlý start Azure – Registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning pomocí sady SDK služby pro Node.js
author: bryanla
ms.author: bryanla
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 207dcc4651a9f3e3712ad67fe1718bcbcd715e27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629928"
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Registrace zařízení X.509 do služby IoT Hub Device Provisioning pomocí sady SDK služby pro Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]


Tyto kroky ukazují, jak prostřednictvím kódu programu vytvořit skupinu registrací pro certifikát X.509 zprostředkující nebo kořenové certifikační autority pomocí sady [SDK služby pro Node.js](https://github.com/Azure/azure-iot-sdk-node) a ukázky v Node.js. Přestože tento postup funguje na počítačích s Windows i Linuxem, v tomto článku se používá vývojový počítač s Windows.
 

## <a name="prerequisites"></a>Požadavky

- Nezapomeňte dokončit kroky v tématu [Nastavení služby IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md). 

 
- Ujistěte se, že na svém počítači máte nainstalované [Node.js v4.0 nebo novější](https://nodejs.org).


- Potřebujete soubor .pem obsahující certifikát X.509 zprostředkující nebo kořenové certifikační autority, který je už uložený a ověřený ve vaší službě zřizování. Sada **Azure IoT c SDK** obsahuje nástroje, které vám můžou pomoci vytvořit řetěz certifikátů X.509, nahrát kořenový nebo zprostředkující certifikát z tohoto řetězu a ověřit certifikát provedením testování vlastnictví pomocí této služby. Pokud chcete využít tyto nástroje, naklonujte na svůj počítač sadu [Azure IoT c SDK](https://github.com/Azure/azure-iot-sdk-c) a postupujte podle kroků v souboru [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

## <a name="create-the-enrollment-group-sample"></a>Vytvoření ukázky skupiny registrací 

 
1. Z příkazového okna ve vaší pracovní složce spusťte:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Pomocí textového editoru ve své pracovní složce vytvořte soubor **create_enrollment_group.js**. Přidejte do souboru následující kód a uložte ho:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>Spuštění ukázky skupiny registrací
 
1. Ke spuštění ukázky potřebujete připojovací řetězec pro vaši službu zřizování. 
    1. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu Device Provisioning. 
    2. Klikněte na **Zásady sdíleného přístupu** a pak kliknutím na zásadu přístupu, kterou chcete použít, otevřete její vlastnosti. V okně **Zásady přístupu** si zkopírujte a poznamenejte primární připojovací řetězec klíče. 

    ![Získání připojovacího řetězce služby zřizování z portálu](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Jak je uvedeno v [Požadavcích](#prerequisites), potřebujete také soubor .pem obsahující certifikát X.509 zprostředkující nebo kořenové certifikační autority, který se už dříve uložil a ověřil ve vaší službě zřizování. Pokud chcete ověřit uložení a ověření vašeho certifikátu, na stránce s přehledem služby Device Provisioning na webu Azure Portal klikněte na **Certifikáty**. Vyhledejte certifikát, který chcete použít pro skupinovou registraci, a ujistěte se, že jeho stav má hodnotu *ověřeno*.

    ![Ověřený certifikát na portálu](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Pokud chcete vytvořit skupinu registrací pro váš certifikát, spusťte následující příkaz (včetně uvozovek okolo argumentů příkazu):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Po úspěšném vytvoření se v příkazovém okně zobrazí vlastnosti nové skupiny registrací.

    ![Vlastnosti registrace ve výstupu příkazu](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Ověřte vytvoření skupiny registrací. Na webu Azure Portal v okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Vyberte kartu **Skupiny registrací** a ověřte, jestli obsahuje novou položku registrace (*první*).

    ![Vlastnosti registrace na portálu](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete prozkoumat ukázky služeb v Node.js, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky Azure vytvořené tímto rychlým startem.
 
1. Zavřete na svém počítači okno výstupu ukázky v Node.js.
2. Přejděte k vaší službě Device Provisioning na webu Azure Portal, klikněte na **Správa registrací** a pak vyberte kartu **Skupiny registrací**. Vyberte *ID registrace* pro položku registrace, kterou jste vytvořili v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části okna.  
3. Ve vaší službě Device Provisioning na webu Azure Portal klikněte na **Certifikáty**, klikněte na certifikát, který jste uložili pro účely tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části okna **Podrobnosti o certifikátu**.  
 
## <a name="next-steps"></a>Další kroky
V rámci tohoto rychlého startu jste pomocí služby Azure IoT Hub Device Provisioning vytvořili skupinovou registraci pro certifikát X.509 zprostředkující nebo kořenové certifikační autority. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 
 
> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)