---
title: Kurz – zřizování simulovaného zařízení X. 509 do služby Azure IoT Hub pomocí jazyka Java a skupin registrace
description: V tomto kurzu vytvoříte a zřídíte simulované zařízení X. 509 pomocí sady Java Device and Service SDK a registrační skupiny pro IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 80efd92560bc7fb5c8bf16b524752e7dbfcdc74b
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526659"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Kurz: vytvoření a zřízení simulovaného zařízení X. 509 pomocí sady Java Device and Service SDK a registrace skupin pro IoT Hub Device Provisioning Service

Tyto kroky ukazují, jak na vývojovém počítači s operačním systémem Windows simulovat zařízení X.509 a pomocí vzorového kódu propojit toto simulované zařízení se službou Device Provisioning a centrem IoT s využitím skupin registrací. 

Než budete pokračovat, nezapomeňte dokončit kroky v tématu [Nastavení služby IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md).


## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že na svém počítači máte nainstalované prostředí [Java SE Development Kit 8](https://aka.ms/azure-jdks).

1. Stáhněte a nainstalujte [Maven](https://maven.apache.org/install.html).

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Použijte následující [Přehled certifikátů](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) k vytvoření testovacích certifikátů.

    > [!NOTE]
    > Tento krok vyžaduje [OpenSSL](https://www.openssl.org/), který můžete buď sestavit a nainstalovat ze zdroje nebo stáhnout a nainstalovat z [třetí strany](https://wiki.openssl.org/index.php/Binaries) , jako je [to](https://sourceforge.net/projects/openssl/). Pokud jste už vytvořili _kořenový_ a _zprostředkující_ certifikát a certifikát _zařízení_, můžete tento krok přeskočit.
    >

    1. Postupujte podle prvních dvou kroků a vytvořte _kořenový_ a _zprostředkující_ certifikát.

    1. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu zřizování.

        1. V okně s přehledem služby Device Provisioning vyberte **Certifikáty** a klikněte na tlačítko **Přidat** v horní části.

        1. V části **Přidat certifikát** zadejte následující informace:
            - Zadejte jedinečný název certifikátu.
            - Vyberte soubor **_rootca. pem_** , který jste vytvořili.
            - Jakmile budete hotovi, klikněte na tlačítko **Uložit**.

           ![Přidání certifikátu](./media/tutorial-group-enrollments/add-certificate.png)

        1. Vyberte nově vytvořený certifikát:
            - Klikněte na **Vygenerovat ověřovací kód**. Zkopírujte vygenerovaný kód.
            - Proveďte krok ověření. Zadejte nebo klikněte pravým tlačítkem a vložte _ověřovací kód_ do spuštěného okna PowerShellu.   Stiskněte **Enter**.
            - Na webu Azure Portal vyberte nově vytvořený soubor **_verifyCert4.pem_**. Klikněte na **Ověřit**.

              ![Ověření certifikátu](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Nakonec proveďte kroky pro vytvoření certifikátů zařízení a vyčištění prostředků.

       > [!NOTE]
       > Při vytváření certifikátů zařízení se ujistěte, že v názvu zařízení používáte pouze malé alfanumerické znaky a pomlčky.
       >


## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

1. Otevřete příkazový řádek. Naklonujte úložiště GitHub se vzorovými kódy pro sadu Java SDK:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Ve staženém zdrojovém kódu přejděte do složky s ukázkou **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Otevřete soubor **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** v libovolném editoru a přidejte následující podrobnosti:

    1. Následujícím způsobem přidejte `[Provisioning Connection String]` pro vaši službu zřizování z portálu:

        1. Přejděte k vaší službě zřizování na webu [Azure Portal](https://portal.azure.com).

        1. Otevřete **Zásady sdíleného přístupu** a vyberte zásadu s oprávněním *EnrollmentWrite*.

        1. Zkopírujte **Primární připojovací řetězec klíče**.

            ![Získání připojovacího řetězce pro zřizování z portálu](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. V souboru se vzorovým kódem **_ServiceEnrollmentGroupSample.java_** nahraďte `[Provisioning Connection String]` za **Primární připojovací řetězec klíče**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. V textovém editoru otevřete soubor zprostředkujícího podpisového certifikátu. Aktualizujte `PUBLIC_KEY_CERTIFICATE_STRING` hodnotu hodnotou svého zprostředkujícího podpisového certifikátu.

        Pokud jste vygenerovali certifikáty zařízení pomocí prostředí bash, soubor *./certs/Azure-IoT-test-Only.Intermediate.CERT.pem* obsahuje klíč zprostředkujícího certifikátu. Pokud se vaše certifikáty vygenerovaly pomocí PowerShellu, *./Intermediate1.pem* bude soubor zprostředkujícího certifikátu.

        ```java
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

    1. Na webu [Azure Portal](https://portal.azure.com) přejděte k centru IoT propojenému s vaší službou zřizování. Otevřete kartu **Přehled** tohoto centra a zkopírujte **Název hostitele**. Přiřaďte tento **Název hostitele** k parametru *IOTHUB_HOST_NAME* (NÁZEV_HOSTITELE_CENTRA_IOT).

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Prostudujte si vzorový kód. Tento kód vytvoří, aktualizuje, dotazuje a odstraní skupinovou registraci pro zařízení X.509. Pokud chcete ověřit úspěšnou registraci na portálu, dočasně okomentujte následující řádky kódu na konci souboru _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Uložte soubor _ServiceEnrollmentGroupSample.java_.

1. Otevřete příkazové okno a přejděte do složky **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Sestavte vzorový kód pomocí tohoto příkazu:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Spusťte ukázku pomocí těchto příkazů v příkazovém okně:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Sledujte v okně výstupu úspěšnou registraci.

    ![Úspěšná registrace](./media/tutorial-group-enrollments/enrollment.png) 

1. Přejděte k vaší službě zřizování na webu Azure Portal. Klikněte na **Správa registrací**. Všimněte si, že se na kartě **skupiny** registrací zobrazí vaše skupina zařízení X. 509 s automaticky vygenerovaným *názvem skupiny*.

## <a name="simulate-the-device"></a>Simulace zařízení

1. V okně s přehledem služby Device Provisioning vyberte **Přehled** a poznamenejte si _Rozsah ID_ a _globální koncový bod služby zřizování_.

    ![Informace o službě](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Otevřete příkazový řádek. Přejděte do složky s ukázkovým projektem.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Upravte `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` a zahrňte svůj _Rozsah ID_ a _globální koncový bod služby zřizování_ , které jste si poznamenali dříve.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Aktualizujte `leafPublicPem` `leafPrivateKey` proměnné a pomocí certifikátů veřejného a soukromého zařízení.

    Pokud jste vygenerovali certifikáty zařízení pomocí PowerShellu, soubory mojezařízení * obsahují veřejný klíč, privátní klíč a PFX pro zařízení.

    Pokud jste vygenerovali certifikáty zařízení pomocí prostředí bash,./certs/New-Device.CERT.pem obsahuje veřejný klíč. Privátní klíč zařízení bude v souboru./Private/New-Device.Key.pem.

    Otevřete soubor veřejného klíče a aktualizujte `leafPublicPem` proměnnou s touto hodnotou. Zkopírujte text z _-----zahajte-----privátního klíče_ , aby _-----konec-----privátního klíče_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Otevřete soubor privátního klíče a aktualizujte `leafPrivatePem` proměnnou s touto hodnotou. Zkopírujte text z _-----zahajte-----privátního klíče RSA_ , abyste _-----koncovým-----RSA privátního klíče_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Pro zprostředkující certifikát přidejte novou proměnnou hned níže `leafPrivateKey` . Pojmenujte tuto novou proměnnou `intermediateKey` . Zadejte hodnotu pro zprostředkující podpisový certifikát.

    Pokud jste vygenerovali certifikáty zařízení pomocí prostředí bash, soubor *./certs/Azure-IoT-test-Only.Intermediate.CERT.pem* obsahuje klíč zprostředkujícího certifikátu. Pokud se vaše certifikáty vygenerovaly pomocí PowerShellu, *./Intermediate1.pem* bude soubor zprostředkujícího certifikátu.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. Ve `main` funkci přidejte do `intermediateKey` `signerCertificates` kolekce před inicializací `securityProviderX509` .

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Uložte změny a sestavte ukázku. Přejděte do cílové složky a spusťte vytvořený soubor jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Úspěšná registrace](./media/tutorial-group-enrollments/registration.png)

1. Na portálu přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **Device Explorer**. Po úspěšném zřízení simulovaného zařízení X.509 pro toto centrum se ID tohoto zařízení zobrazí v okně **Device Explorer** a jeho *STAV* bude **povoleno**. Poznámka: Pokud jste okno otevřeli už před spuštěním ukázkové aplikace zařízení, možná bude potřeba kliknout na tlačítko **Aktualizovat** v horní části. 

    ![Zařízení je zaregistrované u centra IoT](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto ukázkou klienta zařízení a jejím prozkoumáváním, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. Otevřete okno **Správa** registrací pro vaši službu a pak klikněte na kartu **jednotlivé registrace** . Vyberte *ID registrace* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části. 
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. Otevřete okno **Zařízení IoT** pro vaše centrum, vyberte *ID ZAŘÍZENÍ*, které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste na svém počítači s Windows vytvořili simulované zařízení X.509 a pomocí služby Azure IoT Hub Device Provisioning a skupin registrací jste ho zřídili pro své centrum IoT. Další informace o vašem zařízení X.509 najdete v konceptech zařízení. 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service koncepty](concepts-service.md)
