---
title: Rychlý Start – použití symetrického klíče ke zřízení simulovaného zařízení pro Azure IoT Hub pomocí jazyka Java
description: V tomto rychlém startu použijete sadu SDK pro zařízení Java k vytvoření simulovaného zařízení, které používá symetrický klíč s Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc, devx-track-java
ms.openlocfilehash: a32811b439a569a8f8f82fcc046e0f4b89e47a38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966696"
---
# <a name="quickstart-provision-a-simulated-device-to-iot-hub-with-symmetric-keys"></a>Rychlý Start: zřízení simulovaného zařízení pro IoT Hub s symetrickými klíči

V tomto rychlém startu se dozvíte, jak vytvořit a spustit simulátor zařízení na vývojovém počítači s Windows. Toto simulované zařízení nakonfigurujete tak, aby používalo symetrický klíč k ověřování pomocí instance služby Device Provisioning (DPS) a aby se přiřadila ke službě IoT Hub. Vzorový kód z [Microsoft Azure sady IoT SDK pro jazyk Java](https://github.com/Azure/azure-iot-sdk-java) bude použit k simulaci spouštěcí sekvence pro zařízení, které iniciuje zřizování. Zařízení se rozpozná na základě individuální registrace s instancí služby DPS a přiřazenou ke službě IoT Hub.

I když tento článek popisuje, jak zřídit jednotlivé registrace, můžete použít skupiny registrací. Při používání skupin registrací jsou některé rozdíly. Například je nutné použít odvozený klíč zařízení s jedinečným ID registrace pro zařízení. Přestože skupiny registrací symetrického klíče nejsou omezené na starší verze zařízení, příklad skupiny registrací najdete v článku o [zřízení starší verze zařízení pomocí osvědčení symetrického klíče](how-to-legacy-device-symm-key.md). Další informace najdete v článku o [osvědčení symetrického klíče v části o skupinových registracích](concepts-symmetric-key-attestation.md#group-enrollments).

Pokud nejste obeznámeni s procesem automatického zřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) . 

Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). Tento rychlý start vyžaduje vytvořenou instanci služby Device Provisioning Service.

Tento článek je orientovaný na pracovní stanici s Windows. Stejným postupem se však můžete řídit i na Linuxu. Příklad pro Linux najdete v článku o [zřizování architektury s více tenanty](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že na svém počítači máte nainstalovanou [Java se Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) nebo novější.

* Stáhněte a nainstalujte [Maven](https://maven.apache.org/install.html).

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Příprava prostředí Java SDK 

1. Ujistěte se, že je na vašem počítači nainstalovaný Git a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

2. Otevřete příkazový řádek. Naklonujte úložiště GitHub se vzorovým kódem pro simulaci zařízení:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Přejděte do kořenového `azure-iot-sdk-java` adresáře a sestavte projekt, aby se stáhly všechny potřebné balíčky.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com), v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete instanci služby Device Provisioning Service (DPS).

2. Vyberte kartu **spravovat registrace** a pak v horní části vyberte tlačítko **přidat jednotlivou registraci** . 

3. Na panelu **Přidat registraci** zadejte následující informace a stiskněte tlačítko **Uložit** .

   - **Mechanismus:** Jako *Mechanismus* pro ověření identity vyberte **Symetrický klíč**.

   - **Automaticky generovat klíče**: zaškrtněte toto políčko.

   - **ID registrace**: Zadejte ID registrace k identifikaci registrace. Použijte k tomu pouze malá písmena, číslice a spojovník („-“). Například **symm-Key-Java-Device-007**.

   - **ID zařízení IoT Hubu**: Zadejte identifikátor zařízení – Například **Java-Device-007**.

     ![Přidání jednotlivé registrace pro ověření symetrického klíče na webu Azure Portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Po uložení registrace se vygeneruje **primární klíč** a **sekundární klíč** a přidá se k položce registrace. Vaše registrace zařízení symetrického klíče se zobrazí ve sloupci *ID registrace* na kartě *jednotlivé registrace* jako **symm-Key-Java-Device-007** . 

    Otevřete registraci a zkopírujte hodnotu vygenerovaného **primárního klíče**. Tuto hodnotu klíče a **ID registrace** budete používat později, když aktualizujete kód Java pro zařízení.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Simulace spouštěcí sekvence zařízení

V této části provedete aktualizaci ukázkového kódu zařízení a odešlete spouštěcí sekvenci zařízení do instance DPS. Tato spouštěcí sekvence způsobí, že se zařízení rozpozná, ověří a přiřadí ke službě IoT Hub propojené s instancí DPS.

1. V nabídce služba Device Provisioning vyberte **Přehled** a poznamenejte si _Rozsah ID_ a _globální koncový bod služby zřizování_.

    ![Informace o službě](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Otevřete vzorový kód zařízení Java pro úpravy. Úplná cesta k ukázkovému kódu zařízení je:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Přidejte _obor ID_ a _globální koncový bod služby zřizování_ pro instanci DPS. Také zahrňte primární symetrický klíč a ID registrace, které jste zvolili pro jednotlivé registrace. Uložte provedené změny. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Otevřete příkazový řádek pro sestavení. Přejděte do složky ukázkový projekt zřizování v úložišti sady Java SDK.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Sestavte ukázku a potom přejděte do `target` složky a spusťte vytvořený soubor. jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. Očekávaný výstup by měl vypadat nějak takto:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. Na webu Azure Portal přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **Device Explorer**. Po úspěšném zřízení simulovaného zařízení symetrického klíče do centra se jeho ID zařízení zobrazí v okně **Device Explorer** s **povoleným** *stavem* .  Pokud jste okno už otevřeli před spuštěním ukázkové aplikace zařízení, možná budete muset stisknout tlačítko **aktualizovat** v horní části. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci a prozkoumat si ukázku klienta zařízení, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. Otevřete **spravovat registrace** pro vaši službu a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna. 
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. Otevřete **zařízení IoT** pro vaše centrum, zaškrtněte políčko vedle *ID zařízení* , které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste na svém počítači s Windows vytvořili simulované zařízení a prostřednictvím služby Azure IoT Hub Device Provisioning Service na portálu jste ho zřídili ve službě IoT Hub pomocí symetrického klíče. Pokud se chcete dozvědět, jak zařízení programově zaregistrovat, přejděte k rychlému startu pro programovou registraci zařízení X. 509. 

> [!div class="nextstepaction"]
> [Rychlý Start Azure – registrace zařízení X. 509 do Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)