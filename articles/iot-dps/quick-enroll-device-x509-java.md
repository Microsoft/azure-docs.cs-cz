---
title: Rychlý Start – registrace zařízení X. 509 do služby Azure Device Provisioning pomocí jazyka Java
description: V tomto rychlém startu se používají skupinové i jednotlivé registrace. V tomto rychlém startu zaregistrujete zařízení X. 509 do Azure IoT Hub Device Provisioning Service (DPS) pomocí Java.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 924cf1e1b5bc155bfdbd2f5f766c5459d599fed5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276182"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>Rychlý start: Registrace zařízení X.509 do služby Device Provisioning Service pomocí Javy

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

V tomto rychlém startu použijete Java k programovému zápisu skupiny simulovaných zařízení X. 509 do Azure IoT Hub Device Provisioning Service. Zařízení se registrují do instance zřizovací služby vytvořením skupiny registrace nebo jednotlivé registrace. V tomto rychlém startu se dozvíte, jak vytvořit oba typy registrace pomocí sady SDK služby Java a ukázkové aplikace Java.

## <a name="prerequisites"></a>Požadavky

- Dokončení [nastavení IoT Hub Device Provisioning Service pomocí Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java se Development Kit 8](https://aka.ms/azure-jdks). V tomto rychlém startu se nainstaluje [sada SDK služby Java](https://azure.github.io/azure-iot-sdk-java/service/) níže. Funguje na systémech Windows i Linux. V tomto rychlém startu se používá Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Stažení a úprava vzorového kódu v Javě

V této části se používá certifikát X.509 podepsaný svým držitelem. Je důležité vzít v úvahu následující body:

* Certifikáty podepsané svým držitelem jsou určené jenom pro testování a neměly by se používat v produkčním prostředí.
* Výchozí datum vypršení platnosti certifikátu podepsaného svým držitelem je jeden rok.

Následující kroky ukazují, jak do vzorového kódu přidat podrobnosti o zřízení vašeho zařízení X.509. 

1. Otevřete příkazový řádek. Naklonujte ukázku kódu registrace úložiště GitHubu pomocí [sady SDK služby Java](https://azure.github.io/azure-iot-sdk-java/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Ve staženém zdrojovém kódu přejděte do složky s ukázkou **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Otevřete soubor **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** v libovolném editoru a přidejte následující podrobnosti:

    1. Následujícím způsobem přidejte `[Provisioning Connection String]` pro vaši službu zřizování z portálu:
        1. Přejděte k vaší službě zřizování na webu [Azure Portal](https://portal.azure.com). 
        2. Otevřete **zásady sdíleného přístupu**a vyberte zásadu, která má oprávnění *EnrollmentWrite* .
        3. Zkopírujte **Primární připojovací řetězec klíče**. 

            ![Získání připojovacího řetězce pro zřizování z portálu](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. V souboru se vzorovým kódem **_ServiceEnrollmentGroupSample.java_** nahraďte `[Provisioning Connection String]` za **Primární připojovací řetězec klíče**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Přidejte kořenový certifikát pro skupinu zařízení. Pokud potřebujete ukázkový kořenový certifikát, použijte nástroj _Generátor certifikátů X.509_ následujícím způsobem:
        1. V příkazovém okně přejděte do složky **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_**.
        2. Sestavte nástroj spuštěním následujícího příkazu:

            ```cmd\sh
            mvn clean install
            ```

        4. Spusťte nástroj pomocí následujících příkazů:

            ```cmd\sh
            cd target
            java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
            ```

        5. Po zobrazení výzvy můžete volitelně zadat _běžný název_ pro vaše certifikáty.
        6. Tento nástroj místně vygeneruje **Klientský certifikát** (Client Cert), **Privátní klíč klientského certifikátu** (Client Cert Private Key) a **Kořenový certifikát** (Root Cert).
        7. Zkopírujte **kořenový certifikát** včetně řádků **_-----BEGIN CERTIFICATE-----_** a **_-----END CERTIFICATE-----_**. 
        8. Přiřaďte hodnotu **kořenového certifikátu** k parametru **PUBLIC_KEY_CERTIFICATE_STRING**, jak je znázorněno níže:

            ```Java
            private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
            ```

        9. Zavřete příkazové okno nebo po zobrazení výzvy k zadání *ověřovacího kódu* zadejte **n**. 
 
    3. Volitelně můžete svou službu zřizování nakonfigurovat pomocí vzorového kódu:
        - Pokud chcete do ukázky přidat tuto konfiguraci, postupujte následovně:
            1. Na webu [Azure Portal](https://portal.azure.com) přejděte k centru IoT propojenému s vaší službou zřizování. Otevřete kartu **Přehled** tohoto centra a zkopírujte **Název hostitele**. Přiřaďte tento **Název hostitele** k parametru *IOTHUB_HOST_NAME* (NÁZEV_HOSTITELE_CENTRA_IOT).

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Parametru *DEVICE_ID* (ID_ZAŘÍZENÍ) přiřaďte popisný název a u parametru *PROVISIONING_STATUS* (STAV_ZŘIZOVÁNÍ) ponechte výchozí hodnotu *ENABLED* (POVOLENO). 

        - NEBO, pokud se rozhodnete svoji službu zřizování nekonfigurovat, nezapomeňte okomentovat nebo odstranit následující příkazy v souboru _ServiceEnrollmentGroupSample.java_:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Prostudujte si vzorový kód. Tento kód vytvoří, aktualizuje, dotazuje a odstraní skupinovou registraci pro zařízení X.509. Pokud chcete ověřit úspěšnou registraci na portálu, dočasně okomentujte následující řádky kódu na konci souboru _ServiceEnrollmentGroupSample.java_:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Uložte soubor _ServiceEnrollmentGroupSample.java_. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Sestavení a spuštění ukázkové skupinové registrace

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace](concepts-service.md#individual-enrollment): používá se k registraci jednoho zařízení.

Tato procedura používá skupinu pro zápis. V další části se používá individuální registrace.

1. Otevřete příkazové okno a přejděte do složky **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

2. Sestavte vzorový kód pomocí tohoto příkazu:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Tento příkaz stáhne balíček Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) do počítače. Tento balíček obsahuje binární soubory sady SDK služby pro Javu, které vzorový kód vyžaduje k sestavení. Pokud jste v předchozí části spustili nástroj _Generátor certifikátů X.509_, bude tento balíček na vašem počítači už stažený. 

3. Spusťte ukázku pomocí těchto příkazů v příkazovém okně:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Sledujte v okně výstupu úspěšnou registraci.

5. Přejděte k vaší službě zřizování na webu Azure Portal. Klikněte na **Správa registrací**. Všimněte si, že se na kartě **Skupiny registrací** zobrazí vaše skupina zařízení X.509 s automaticky vygenerovaným *NÁZVEM SKUPINY*. 

    ![Ověření úspěšné registrace X.509 na portálu](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Úpravy pro registraci jednotlivého zařízení X.509

Pokud chcete zaregistrovat jediné zařízení X.509, upravte následujícím způsobem vzorový kód pro *jednotlivou registraci* použitý v tématu [Registrace zařízení TPM do služby IoT Hub Device Provisioning pomocí sady SDK služby pro Javu](quick-enroll-device-tpm-java.md#javasample):

1. Zkopírujte do schránky *Běžný název* vašeho klientského certifikátu X.509. Pokud chcete použít nástroj _Generátor certifikátů X.509_, jak je znázorněno v [předchozí části věnované vzorovému kódu](#javasample), zadejte _Běžný název_ vašeho certifikátu nebo použijte výchozí **microsoftriotcore**. Tento **Běžný název** použijte jako hodnotu pro proměnnou *REGISTRATION_ID* (ID_REGISTRACE). 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Přejmenujte proměnnou *TPM_ENDORSEMENT_KEY* na *PUBLIC_KEY_CERTIFICATE_STRING*. Zkopírujte váš klientský certifikát nebo **klientský certifikát** z výstupu nástroje _Generátor certifikátů X.509_ jako hodnotu proměnné *PUBLIC_KEY_CERTIFICATE_STRING*. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```
3. Ve funkci **main** nahraďte řádek `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` následujícím řádkem, který zajistí používání klientského certifikátu X.509:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Uložte, sestavte a spusťte *jednotlivý* ukázkový soubor registrace, a to pomocí kroků v části [sestavení a spuštění ukázkového kódu pro jednotlivou registraci](quick-enroll-device-tpm-java.md#runjavasample).


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud plánujete prozkoumat ukázku služby Java, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Javě.
1. Zavřete na svém počítači okno _Generátoru certifikátů X.509_.
1. V Azure Portal přejděte do vaší služby Device Provisioning, vyberte **spravovat registrace**a pak vyberte kartu **skupiny** registrací. Zaškrtněte políčko vedle *názvu skupiny* pro zařízení X. 509, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna.  

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste do své služby Device Provisioning zaregistrovali simulovanou skupinu zařízení X. 509. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)
