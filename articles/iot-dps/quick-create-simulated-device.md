---
title: 'Úvodní příručka: Zřízení simulovaného zařízení TPM do služby Azure IoT Hub pomocí C'
description: V tomto rychlém startu se používají jednotlivé registrace. V tomto rychlém startu vytvoříte a zřídíte simulované zařízení TPM pomocí sady SDK zařízení C pro službu DPS služby Azure IoT Hub Device Provisioning Service.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d41c4757f0b81312cefa580c3a3263f87bccffa9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241700"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Rychlé zprovoznění: Zřízení simulovaného zařízení TPM s využitím sady Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

V tomto rychlém zprovoznění se dozvíte, jak vytvořit a spustit simulátor zařízení TPM (Trusted Platform Module) na vývojovém počítači s Windows. Toto simulované zařízení připojíte k IoT Hubu pomocí instance služby Device Provisioning. Ukázkový kód ze sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) se použije při registraci zařízení s využitím služby Device Provisioning a simulaci spouštěcí sekvence pro toto zařízení.

Pokud nejste obeznámeni s procesem automatického zřizování, přečtěte [si koncepty automatického zřizování](concepts-auto-provisioning.md). Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). 

Služba Azure IoT Device Provisioning podporuje dva typy registrací:
- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

V tomto článku si předvedeme jednotlivé registrace.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Následující předpoklady jsou pro vývojové prostředí systému Windows. Pro Linux nebo macOS, najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci sady SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 s povoleným temanem [pro vývoj plochy s C++.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Visual Studio 2015 a Visual Studio 2017 jsou také podporovány.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Příprava vývojového prostředí pro Azure IoT C SDK

V této části připravíte vývojové prostředí použité k sestavení [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a ukázky simulátoru zařízení [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview).

1. Stáhněte si [systém sestavení CMake](https://cmake.org/download/).

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

3. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkazy a naklonovat nejnovější verzi úložiště [GitHub Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) Jako hodnotu `-b` parametru použijte značku, kterou jste našli v předchozím kroku:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

4. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Spusťte z adresáře následující příkazy: `azure-iot-sdk-c`

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Sestavení SDK a spuštění simulátoru zařízení TPM

V této části sestavíte sadu Azure IoT C SDK, která zahrnuje ukázkový kód simulátoru zařízení TPM. Tato ukázka poskytuje [mechanismus osvědčení](concepts-security.md#attestation-mechanism) TPM prostřednictvím ověřování tokenu sdíleného přístupového podpisu (SAS).

1. Z podadresáře `cmake`, ve kterém jste vytvořili úložiště azure-iot-sdk-c git, spusťte následující příkaz pro sestavení ukázky. Tento příkaz pro sestavení vygeneruje pro toto simulované zařízení také řešení Visual Studio.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Přejděte ke kořenové složce úložiště Git, které jste naklonovali, a spusťte simulátor [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) s využitím cesty uvedené níž. Tento simulátor naslouchá přes soket na portech 2321 a 2322. Toto příkazové okno nezavírejte, simulátor je potřeba nechat spuštěný až do konce tohoto rychlého zprovoznění. 

   Pokud jste ve složce *cmake*, spusťte následující příkazy:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Ze simulátoru neuvidíte žádný výstup. Necháme ho běžet, aby simuloval zařízení TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Načtení kryptografických klíčů ze zařízení TPM

V této části sestavíte a spustíte příklad, který ze simulátoru TPM, který jste nechali spuštěný, aby naslouchal na portech 2321 a 2322, načte ověřovací klíč a ID registrace. Tyto hodnoty se použijí pro registraci zařízení s vaší instancí služby Device Provisioning.

1. Spusťte sadu Visual Studio a otevřete nový soubor řešení s názvem `azure_iot_sdks.sln`. Tento soubor řešení je umístěný ve složce `cmake`, kterou jste vytvořili v kořenovém adresáři úložiště Git azure-iot-sdk-c.

2. V nabídce Visual Studio vyberte **sestavení** > **sestavení řešení** k sestavení všechny projekty v řešení.

3. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **tpm_device_provision** a vyberte **Nastavit jako spouštěný projekt**. 

4. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Aplikace přečte a zobrazí **_ID registrace_** a **_ověřovací klíč_**. Poznamenejte si tyto hodnoty nebo zkopírujte. Použijí se v další části pro registraci zařízení. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Vytvoření položky registrace zařízení na portálu

1. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete službu Zřizování zařízení.

1. Vyberte kartu **Spravovat zápisy** a nahoře vyberte tlačítko **Přidat jednotlivé zápisy.** 

1. V panelu **Přidat zápis** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **TPM**.
   - Zadejte *ID registrace* a *ověřovací klíč* pro zařízení TPM z hodnot, které jste si dříve poznamenali.
   - Vyberte centrum IoT propojené s vaší zřizovací službou.
   - Volitelně můžete zadat následující informace:
       - Zadejte jedinečné *ID zařízení* (můžete použít navrhované **testovací docs-zařízení** nebo zadat vlastní). Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. Pokud se rozhodnete neposkytnout jeden, id registrace se použije k identifikaci zařízení místo.
       - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení stiskněte tlačítko **Uložit.** 

      ![Zadání informací o registraci zařízení na portálu](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Po úspěšné registraci se *ID registrace* vašeho zařízení zobrazí v seznamu na kartě *Jednotlivé registrace*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulace první spouštěcí sekvence pro zařízení

V této části nakonfigurujete ukázkový kód, aby použil [protokol AMQP (Advanced Message Queuing Protocol)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) k odeslání spouštěcí sekvence zařízení do vaší instance služby Device Provisioning. Toto spouštěcí sekvence způsobí, že se zařízení rozpozná a přiřadí službě IoT Hub propojené s instancí služby Device Provisioning.

1. Na webu Azure Portal vyberte okno **Přehled** služby Device Provisioning a zkopírujte hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z portálu](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Samples**. Rozbalte ukázkový projekt s názvem **prov\_dev\_client\_sample**. Rozbalte **zdrojové soubory** a otevřete **prov\_dev\_client\_sample.c**.

3. V horní části souboru najděte příkazy `#define` pro jednotlivé protokoly zařízení, jak je vidět dole. Zkontrolujte, že položka `SAMPLE_AMQP` není zakomentovaná.

    V současnosti [se protokol MQTT pro jednotlivé registrace TPM nepodporuje](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Ve stejném souboru vyhledejte definici funkce `main()`. Zkontrolujte, jestli je proměnná `hsm_type` nastavená na hodnotu `SECURE_DEVICE_TYPE_TPM`, a ne na hodnotu `SECURE_DEVICE_TYPE_X509`, jak je vidět dole.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**. 

7. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Ve výzvě k opětovnému sestavení projektu vyberte **ano**, chcete-li projekt před spuštěním znovu sestavit.

    Následující výstup je příkladem úspěšného spuštění ukázky klienta zřizování zařízení a připojení k instanci služby Device Provisioning pro získání informací o centru IoT hub a zajištění registrace:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Jakmile je simulované zařízení zřízeno do služby IoT hub vaší zřizovací službou, zobrazí se ID zařízení se **zařízeními IoT centra**. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
2. Zavřete na svém počítači okno simulátoru TPM.
3. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. Otevřete **Spravovat registrace** pro vaši službu a pak zaškrtněte kartu **Jednotlivé registrace.** Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
4. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. Otevřete **zařízení IoT** pro svůj rozbočovač, zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v počítači vytvořili simulované zařízení TPM a zřídíte ho do služby IoT hub pomocí služby Zřizování zařízení služby IoT Hub. Chcete-li se dozvědět, jak zaregistrovat zařízení Čipové čipové čipy programově, pokračujte na úvodním startu pro programovou registraci zařízení TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení TPM do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-java.md)
