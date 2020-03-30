---
title: Úvodní příručka – použití symetrického klíče k zřízení simulovaných zařízení do služby Azure IoT Hub pomocí Jazyka Java
description: V tomto rychlém startu použijete sadu SDK zařízení Java k vytvoření simulovaného zařízení, které používá symetrický klíč se službou Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76941399"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Rychlý start: Zřízení simulovaného zařízení se symetrickými klíči

V tomto rychlém startu se dozvíte, jak vytvořit a spustit simulátor zařízení na vývojovém počítači s Windows. Nakonfigurujete toto simulované zařízení tak, aby používalo symetrický klíč k ověření pomocí instance služby DPS (Device Provisioning Service) a bylo přiřazeno k centru IoT Hub. Ukázkový kód z [sad Microsoft Azure IoT SDK pro Javu](https://github.com/Azure/azure-iot-sdk-java) se použije k simulaci spouštěcí sekvence pro zařízení, které iniciuje zřizování. Zařízení bude rozpoznáno na základě individuální registrace s instancí služby DPS a přiřazeno k centru IoT.

I když tento článek ukazuje zřizování s jednotlivými registrace, můžete použít skupiny registrace. Existují určité rozdíly při použití skupin y zápisu. Například je nutné použít odvozený klíč zařízení s jedinečným ID registrace pro zařízení. Přestože skupiny registrací symetrického klíče nejsou omezené na starší verze zařízení, příklad skupiny registrací najdete v článku o [zřízení starší verze zařízení pomocí osvědčení symetrického klíče](how-to-legacy-device-symm-key.md). Další informace najdete v článku o [osvědčení symetrického klíče v části o skupinových registracích](concepts-symmetric-key-attestation.md#group-enrollments).

Pokud neznáte proces automatického zřizování, projděte si [koncepty automatického zřizování](concepts-auto-provisioning.md). 

Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). Tento rychlý start vyžaduje vytvořenou instanci služby Device Provisioning Service.

Tento článek je orientovaný na pracovní stanici s Windows. Stejným postupem se však můžete řídit i na Linuxu. Příklad pro Linux najdete v článku o [zřizování architektury s více tenanty](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že máte [java SE Development Kit 8](https://aka.ms/azure-jdks) nebo novější nainstalován na vašem počítači.

* Stáhněte a nainstalujte [Maven](https://maven.apache.org/install.html).

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Příprava prostředí Java SDK 

1. Ujistěte se, že je na vašem počítači nainstalovaný Git a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

2. Otevřete příkazový řádek. Naklonujte úložiště GitHub se vzorovým kódem pro simulaci zařízení:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Přejděte do `azure-iot-sdk-java` kořenového adresáře a vytvořte projekt ke stažení všech potřebných balíčků.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

1. Přihlaste se k [portálu Azure](https://portal.azure.com), vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete instanci služby Device Provisioning (DPS).

2. Vyberte kartu **Spravovat zápisy** a nahoře vyberte tlačítko **Přidat jednotlivé zápisy.** 

3. V panelu **Přidat zápis** zadejte následující informace a stiskněte tlačítko **Uložit.**

   - **Mechanismus:** Jako *Mechanismus* pro ověření identity vyberte **Symetrický klíč**.

   - **Automaticky generovat klíče**: Zaškrtněte toto políčko.

   - **ID registrace**: Zadejte ID registrace k identifikaci registrace. Použijte k tomu pouze malá písmena, číslice a spojovník („-“). Například **symm-key-java-device-007**.

   - **ID zařízení IoT Hubu**: Zadejte identifikátor zařízení – Například **java-device-007**.

     ![Přidání jednotlivé registrace pro ověření symetrického klíče na webu Azure Portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Po uložení registrace se primární **klíč** a sekundární klíč vygenerují a přidají do **položky** registrace. Registrace zařízení symetrického klíče se zobrazí jako **symm-key-java-device-007** ve *sloupci ID registrace* na kartě *Jednotlivé registrace.* 

    Otevřete registraci a zkopírujte hodnotu vygenerovaného **primárního klíče**. Tuto hodnotu klíče a **ID registrace** použijete později, až aktualizujete kód Javy pro zařízení.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Simulace spouštěcí sekvence zařízení

V této části aktualizujete ukázkový kód zařízení, abyste odeslali spouštěcí sekvenci zařízení do instance DPS. Tato spouštěcí sekvence způsobí, že zařízení bude rozpoznáno, ověřeno a přiřazeno k centru IoT propojenému s instancí DPS.

1. V nabídce Služba zřizování zařízení vyberte **Přehled** a poznamenejte si globální koncový bod _oboru ID_ a _zřizovací služby_.

    ![Informace o službě](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Otevřete ukázkový kód zařízení Java pro úpravy. Úplná cesta k ukázkovému kódu zařízení je:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Přidejte globální koncový bod _oboru ID_ a _zřizovací služby_ instance DPS. Zahrňte také primární symetrický klíč a ID registrace, které jste zvolili pro individuální zápis. Uložte provedené změny. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Otevřete příkazový řádek pro vytváření. Přejděte do ukázkové složky projektu zřizování úložiště Java SDK.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Vytvořte ukázku a `target` přejděte do složky a spusťte vytvořený soubor JAR.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. Očekávaný výstup by měl vypadat podobně jako následující:

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

6. Na webu Azure Portal přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **Device Explorer**. Po úspěšném zřizování simulované hospo- zařízení symetrického klíče do rozbočovače se jeho ID zařízení zobrazí v okně **Průzkumníka zařízení** s *funkcí STATUS* jako **povolenou**.  Možná budete muset stisknout tlačítko **Aktualizovat** v horní části, pokud jste již otevřeli okno před spuštěním ukázkové aplikace zařízení. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. Otevřete **Spravovat registrace** pro vaši službu a pak zaškrtněte kartu **Jednotlivé registrace.** Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. Otevřete **zařízení IoT** pro svůj rozbočovač, zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili simulované zařízení v počítači s Windows a zřídit ho do vašeho centra IoT pomocí symetrického klíče se službou Azure IoT Hub DeviceProvisioning Service na portálu. Chcete-li se dozvědět, jak zaregistrovat zařízení programově, pokračujte na úvodním startu pro programovou registraci zařízení X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
