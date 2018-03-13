---
title: "Zřízení simulovaného zařízení X.509 pro službu Azure IoT Hub pomocí Javy | Dokumentace Microsoftu"
description: "Rychlý start Azure – Vytvoření a zřízení simulovaného zařízení X.509 pomocí sady Java SDK pro zařízení pro službu IoT Hub Device Provisioning"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 7e4ad361df8a37d4a82c1bc50c6fb134a1ad5159
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Vytvoření a zřízení simulovaného zařízení X.509 pomocí sady Java SDK pro zařízení pro službu IoT Hub Device Provisioning
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Tyto kroky ukazují, jak na vývojovém počítači s operačním systémem Windows simulovat zařízení X.509 a pomocí vzorového kódu propojit toto simulované zařízení se službou Device Provisioning a centrem IoT. 

Než budete pokračovat, nezapomeňte dokončit kroky v tématu [Nastavení služby IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md).


## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že na svém počítači máte nainstalované prostředí [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

1. Stáhněte a nainstalujte [Maven](https://maven.apache.org/install.html).

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Otevřete příkazový řádek. Naklonujte úložiště GitHub se vzorovým kódem pro simulaci zařízení:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Přejděte k projektu generátoru certifikátů a sestavte ho. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Přejděte do cílové složky a spusťte vytvořený soubor jar.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. V závislosti na vašem nastavení vytvořte jedním z následujících způsobů informace o registraci:

    - **Jednotlivá registrace:**

        1. Po zobrazení text _Do you want to input common name_ (Chcete zadat běžný název) zadejte **N**. Zkopírujte do schránky výstup `Client Cert` začínající na *-----BEGIN CERTIFICATE-----* a končící na *-----END CERTIFICATE-----*.

            ![Generátor jednotlivých certifikátů](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Na svém počítači s Windows vytvořte soubor **_X509individual.pem_**, otevřete ho v libovolném editoru a zkopírujte do něj obsah schránky. Uložte soubor.

        1. Po zobrazení textu _Do you want to input Verification Code_ (Chcete zadat ověřovací kód) zadejte **N** a nechte výstup programu otevřený, protože ho použijete jako referenci v pozdější části tohoto rychlého startu. Poznamenejte si hodnoty _Client Cert_ (Klientský certifikát) a _Client Cert Private Key_ (Privátní klíč klientského certifikátu).
    
    - **Skupiny registrací:**

        1. Po zobrazení text _Do you want to input common name_ (Chcete zadat běžný název) zadejte **N**. Zkopírujte do schránky výstup `Root Cert` začínající na *-----BEGIN CERTIFICATE-----* a končící na *-----END CERTIFICATE-----*.

            ![Generátor certifikátů skupin](./media/java-quick-create-simulated-device-x509/group.png)

        1. Na svém počítači s Windows vytvořte soubor **_X509group.pem_**, otevřete ho v libovolném editoru a zkopírujte do něj obsah schránky. Uložte soubor.

        1. Po zobrazení textu _Do you want to input Verification Code_ (Chcete zadat ověřovací kód) zadejte **Y** a nechte program otevřený pro pozdější použití v rámci tohoto rychlého startu. Poznamenejte si hodnoty _Client Cert_ (Klientský certifikát), _Client Cert Private Key_ (Privátní klíč klientského certifikátu), _Signer Cert_ (Certifikát podpisovatele) a _Root Cert_ (Kořenový certifikát).

        > [!NOTE]
        > Výše uvedená hodnota `Root Cert` se týká pouze certifikátů vytvořených ve výstupu konzoly a není možné ji použít k podepisování dalších klientských certifikátů. Pokud potřebujete robustnější sadu testovacích certifikátů, podívejte se na [ukázku správy certifikátů webu](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).
        >

## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

1. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu zřizování.

1. V závislosti na vašem nastavení zadejte jedním z následujících způsobů informace o registraci:

    - **Jednotlivá registrace:** 

        1. V okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Vyberte kartu **Jednotlivé registrace** a klikněte na tlačítko **Přidat** v horní části. 

        1. V části **Přidat položku seznamu registrací** zadejte následující informace:
            - Jako *Mechanismus* ověření identity vyberte **X.509**.
            - V části *Soubor .pem nebo .cer certifikátu* vyberte soubor certifikátu **_X509individual.pem_** vytvořený v předchozích krocích pomocí widgetu *Průzkumník souborů*.
            - Volitelně můžete zadat následující informace:
                - Vyberte centrum IoT propojené s vaší zřizovací službou.
                - Zadejte jedinečné ID zařízení. Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. 
                - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
            - Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

        ![Zadání informací o registraci zařízení X.509 v okně portálu](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Po úspěšné registraci se vaše zařízení X.509 zobrazí jako **microsoftriotcore** ve sloupci *ID registrace* na kartě *Jednotlivé registrace*. 

    - **Skupiny registrací:** 

        1. V okně s přehledem služby Device Provisioning vyberte **Certifikáty** a klikněte na tlačítko **Přidat** v horní části.

        1. V části **Přidat certifikát** zadejte následující informace:
            - Zadejte jedinečný název certifikátu.
            - Vyberte soubor **_X509group.pem_**, který jste vytvořili dříve.
            - Jakmile budete hotovi, klikněte na tlačítko **Uložit**.

        ![Přidání certifikátu](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Vyberte nově vytvořený certifikát:
            - Klikněte na **Vygenerovat ověřovací kód**. Zkopírujte vygenerovaný kód.
            - Zadejte nebo klikněte pravým tlačítkem a vložte _ověřovací kód_ do spuštěného okna _provisioning-x509-cert-generator_.  Stiskněte **Enter**.
            - Zkopírujte do schránky výstup `Verification Cert` začínající na *-----BEGIN CERTIFICATE-----* a končící na *-----END CERTIFICATE-----*.
            
                ![Generátor ověření](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Na svém počítači s Windows vytvořte soubor **_X509validation.pem_**, otevřete ho v libovolném editoru a zkopírujte do něj obsah schránky. Uložte soubor.
            - Vyberte soubor **_X509validation.pem_** na webu Azure Portal. Klikněte na **Ověřit**.

            ![Ověření certifikátu](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Vyberte **Správa registrací**. Vyberte kartu **Skupiny registrací** a klikněte na tlačítko **Přidat** v horní části.
            - Zadejte jedinečný název skupiny.
            - Vyberte jedinečný název certifikátu, který jste vytvořili dříve.
            - Volitelně můžete zadat následující informace:
                - Vyberte centrum IoT propojené s vaší zřizovací službou.
                - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.

        ![Zadání informací o registraci skupiny X.509 v okně portálu](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        Po úspěšné registraci se vaše skupina zařízení X.509 zobrazí ve sloupci *Název skupiny* na kartě *Skupiny registrací*.


## <a name="simulate-the-device"></a>Simulace zařízení

1. V okně s přehledem služby Device Provisioning vyberte **Přehled** a poznamenejte si _Rozsah ID_ a _Globální koncový bod služby zřizování_.

    ![Informace o službě](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Otevřete příkazový řádek. Přejděte do složky s ukázkovým projektem.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. V závislosti na vašem nastavení zadejte jedním z následujících způsobů informace o registraci:

    - **Jednotlivá registrace:** 

        1. Upravte soubor `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` tak, aby zahrnoval váš _Rozsah ID_ a _Globální koncový bod služby zřizování_, které jste si poznamenali dříve. Zahrňte také _Klientský certifikát_ a _Privátní klíč klientského certifikátu_, které jste si poznamenali dříve.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Pro vložení certifikátu a klíče použijte následující formát:
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Skupiny registrací:** 

        1. Postupujte podle výše uvedených pokynů pro **Jednotlivou registraci**.

        1. Přidejte následující řádky kódu na začátek funkce `main`.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - Pro vložení certifikátů použijte následující formát:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Sestavte ukázku. Přejděte do cílové složky a spusťte vytvořený soubor jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. Na portálu přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **Device Explorer**. Po úspěšném zřízení simulovaného zařízení X.509 pro toto centrum se ID tohoto zařízení zobrazí v okně **Device Explorer** a jeho *STAV* bude **povoleno**. Poznámka: Pokud jste okno otevřeli už před spuštěním ukázkové aplikace zařízení, možná bude potřeba kliknout na tlačítko **Aktualizovat** v horní části. 

    ![Zařízení je zaregistrované u centra IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto ukázkou klienta zařízení a jejím prozkoumáváním, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. Otevřete okno **Správa registrací** pro vaši službu a pak klikněte na kartu **Jednotlivé registrace**. Vyberte *ID REGISTRACE* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části. 
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. Otevřete okno **Zařízení IoT** pro vaše centrum, vyberte *ID ZAŘÍZENÍ*, které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části.


## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste na svém počítači s Windows vytvořili simulované zařízení X.509 a pomocí služby Azure IoT Hub Device Provisioning na portálu jste ho zřídili pro své centrum IoT. Pokud chcete zjistit, jak zaregistrovat zařízení X.509 prostřednictvím kódu programu, pokračujte k rychlému startu pro registraci zařízení X.509 prostřednictvím kódu programu. 

> [!div class="nextstepaction"]
> [Rychlý start Azure – Registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
