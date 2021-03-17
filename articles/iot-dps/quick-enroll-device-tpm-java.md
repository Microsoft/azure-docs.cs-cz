---
title: Rychlý Start – registrace zařízení TPM do služby Azure Device Provisioning pomocí Java
description: Rychlý Start – registrace zařízení TPM do Azure IoT Hub Device Provisioning Service (DPS) pomocí sady SDK služby Java V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0a1f4ed46ab9e467a19cfa722a2d345284fdc94a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463059"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Rychlý Start: registrace zařízení TPM pro IoT Hub Device Provisioning Service pomocí sady SDK služby Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

V tomto rychlém startu jste programově vytvořili jednotlivou registraci pro simulované zařízení TPM v Azure IoT Hub Device Provisioning Service pomocí sady SDK služby Java s použitím ukázkové aplikace Java.

## <a name="prerequisites"></a>Předpoklady

- Dokončení [nastavení IoT Hub Device Provisioning Service pomocí Azure Portal](./quick-setup-auto-provision.md).
- Dokončování [šifrovacích klíčů pro čtení ze zařízení TPM](quick-create-simulated-device.md#simulatetpm).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java se Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). V tomto rychlém startu se nainstaluje [sada SDK služby Java](https://azure.github.io/azure-iot-sdk-java/master/service/) níže. Funguje na systémech Windows i Linux. V tomto rychlém startu se používá Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí 

1. Ujistěte se, že na svém počítači máte nainstalované prostředí [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). 

2. Nastavte proměnné prostředí pro vaši instalaci Javy. Proměnná `PATH` by měla obsahovat úplnou cestu k adresáři *jdk1.8.x\bin*. Pokud se jedná o první instalaci Javy na vašem počítači, vytvořte novou proměnnou prostředí `JAVA_HOME` a přidejte do ní odkaz na úplnou cestu k adresáři *jdk1.8.x*. Na počítači s Windows se tento adresář nachází ve složce *C:\\Program Files\\Java\\* a proměnné prostředí můžete vytvořit nebo upravit tak, že v **Ovládacích panelech** na počítači s Windows vyhledáte možnost **Upravit proměnné prostředí systému**. 

   Úspěšné nastavení Javy na vašem počítači můžete zkontrolovat spuštěním následujícího příkazu v příkazovém okně:

    ```cmd\sh
    java -version
    ```

3. Stáhněte a rozbalte na svém počítači [Maven 3](https://maven.apache.org/download.cgi). 

4. Upravte proměnnou prostředí `PATH` tak, aby odkazovala na složku *apache-maven-3.x.x\\bin* ve složce, kam se extrahoval Maven. Úspěšnou instalaci Mavenu můžete ověřit spuštěním tohoto příkazu v příkazovém okně:

    ```cmd\sh
    mvn --version
    ```

5. Ujistěte se, že je na vašem počítači nainstalovaný [git](https://git-scm.com/download/) a že je přidaný do proměnné prostředí `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Stažení a úprava vzorového kódu v Javě

Tato část ukazuje, jak do vzorového kódu přidat podrobnosti o zřizování vašeho zařízení TPM. 

1. Otevřete příkazový řádek. Naklonujte ukázku kódu registrace úložiště GitHubu pomocí [sady SDK služby Java](https://azure.github.io/azure-iot-sdk-java/master/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Ve staženém zdrojovém kódu přejděte do složky s ukázkou **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Otevřete soubor **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** v libovolném editoru a přidejte následující podrobnosti:

   1. Následujícím způsobem přidejte `[Provisioning Connection String]` pro vaši službu zřizování z portálu:
       1. Přejděte k vaší službě zřizování na webu [Azure Portal](https://portal.azure.com). 
       2. Otevřete **Zásady sdíleného přístupu** a vyberte zásadu s oprávněním *EnrollmentWrite*.
       3. Zkopírujte **Primární připojovací řetězec klíče**. 

           ![Získání připojovacího řetězce pro zřizování z portálu](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. V souboru se vzorovým kódem **_ServiceEnrollmentSample.java_** nahraďte `[Provisioning Connection String]` za **Primární připojovací řetězec klíče**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Přidejte podrobnosti o zařízení TPM:
       1. Podle postupu vedoucího k části [Simulace zařízení TPM](quick-create-simulated-device.md#simulatetpm) získejte *ID registrace* a *Ověřovací klíč TPM* pro simulaci zařízení TPM.
       2. Použijte **_ID registrace_** a **_Ověřovací klíč_** z výstupu předchozího kroku k nahrazení `[RegistrationId]` a `[TPM Endorsement Key]` v souboru se vzorovým kódem **_ServiceEnrollmentSample.java_**:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Volitelně můžete svou službu zřizování nakonfigurovat pomocí vzorového kódu:
      - Pokud chcete do ukázky přidat tuto konfiguraci, postupujte následovně:
        1. Na webu [Azure Portal](https://portal.azure.com) přejděte k centru IoT propojenému s vaší službou zřizování. Otevřete kartu **Přehled** tohoto centra a zkopírujte **Název hostitele**. Přiřaďte tento **Název hostitele** k parametru *IOTHUB_HOST_NAME* (NÁZEV_HOSTITELE_CENTRA_IOT).
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Parametru *DEVICE_ID* (ID_ZAŘÍZENÍ) přiřaďte popisný název a u parametru *PROVISIONING_STATUS* (STAV_ZŘIZOVÁNÍ) ponechte výchozí hodnotu *ENABLED* (POVOLENO). 
    
      - NEBO, pokud se rozhodnete svoji službu zřizování nekonfigurovat, nezapomeňte okomentovat nebo odstranit následující příkazy v souboru _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Prostudujte si vzorový kód. Tento kód vytvoří, aktualizuje, dotazuje a odstraní jednotlivou registraci zařízení TPM. Pokud chcete ověřit úspěšnou registraci na portálu, dočasně okomentujte následující řádky kódu na konci souboru _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Uložte soubor _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Sestavení a spuštění vzorového kódu v Javě

1. Otevřete příkazové okno a přejděte do složky **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Sestavte vzorový kód pomocí tohoto příkazu:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Tento příkaz stáhne balíček Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) do počítače. Tento balíček obsahuje binární soubory pro [sadu SDK služby Java](https://azure.github.io/azure-iot-sdk-java/master/service/), které musí sestavit ukázkový kód. 

3. Spusťte ukázku pomocí těchto příkazů v příkazovém okně:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Sledujte v okně výstupu úspěšnou registraci. 

5. Přejděte k vaší službě zřizování na webu Azure Portal. Vyberte možnost **spravovat registrace** a vyberte kartu **jednotlivé registrace** . Všimněte si, že je teď uvedený *ID registrace* simulovaného zařízení TPM. 

    ![Ověření úspěšné registrace TPM na portálu](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud plánujete prozkoumat ukázku služby Java, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Javě.
1. Zavřete okno simulátoru TPM, pokud jste ho otevřeli pro simulaci zařízení TPM.
1. V Azure Portal přejděte do vaší služby Device Provisioning, vyberte **spravovat registrace** a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* pro položku registrace, kterou jste vytvořili v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste do své služby Device Provisioning zaregistrovali simulované zařízení TPM. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)