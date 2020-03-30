---
title: Zřízení simulovaných zařízení X.509 do služby Azure IoT Hub pomocí c
description: V tomto rychlém startu se používají jednotlivé registrace. V tomto rychlém startu vytvoříte a zřídíte simulované zařízení X.509 pomocí sady SDK zařízení C pro službu DPS centra Azure IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f0c95e495e222cc72f0a6fc432404fcbaa47df65
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241161"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Rychlé zprovoznění: Zřízení simulovaného zařízení X.509 s využitím sady Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

V tomto rychlém startu se dozvíte, jak vytvořit a spustit simulátor zařízení X.509 na vývojovém počítači s Windows. Toto simulované zařízení nakonfigurujete tak, aby se dalo přiřadit k IoT Hubu pomocí registrace instance služby Device Provisioning Service. K simulaci spouštěcí sekvence pro zařízení se použije vzorový kód sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Rozpoznání zařízení proběhne na základě registrace ve službě zřizování a dojde k jeho přiřazení do IoT Hubu.

Pokud nejste obeznámeni s procesem automatického zřizování, přečtěte [si koncepty automatického zřizování](concepts-auto-provisioning.md). Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](quick-setup-auto-provision.md). 

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

* [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
* [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

V tomto článku si předvedeme jednotlivé registrace.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Následující předpoklady jsou pro vývojové prostředí systému Windows. Pro Linux nebo macOS, najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci sady SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 s povoleným temanem [pro vývoj plochy s C++.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Visual Studio 2015 a Visual Studio 2017 jsou také podporovány.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Příprava vývojového prostředí pro Azure IoT C SDK

V této části připravíte vývojové prostředí, které se používá k sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c), která obsahuje ukázkový kód spouštěcí sekvence X.509.

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

5. Vzorový kód k zajištění ověření prostřednictvím ověřování X.509 používá certifikát X.509. Spusťte následující příkaz k vytvoření verze sady SDK specifické pro vývojovou platformu, která zahrnuje klienta zřizování zařízení. V `cmake` adresáři je generováno řešení sady Visual Studio pro simulované zařízení.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Jakmile sestavení úspěšné, posledních několik výstupních řádků vypadat podobně jako následující výstup:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Vytvoření certifikátu zařízení X.509 podepsaného svým držitelem

V této části budete používat certifikát X.509 podepsaný svým držitelem. Je důležité vzít v úvahu tyto body:

* Certifikáty podepsané svým držitelem jsou určené jenom pro testování a neměly by se používat v produkčním prostředí.
* Výchozí datum vypršení platnosti certifikátu podepsaného svým držitelem je jeden rok.

Pomocí vzorového kódu ze sady Azure IoT C SDK vytvoříte certifikát, který použije položka registrace pro simulované zařízení.

1. Spusťte sadu Visual Studio a otevřete nový soubor řešení s názvem `azure_iot_sdks.sln`. Tento soubor řešení je umístěný ve složce `cmake`, kterou jste vytvořili v kořenovém adresáři úložiště Git azure-iot-sdk-c.

2. V nabídce Visual Studio vyberte **sestavení** > **sestavení řešení** k sestavení všechny projekty v řešení.

3. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **dice\_device\_enrollment** a vyberte **Nastavit jako spouštěný projekt**.

4. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Po zobrazení výzvy zadejte v okně Výstup **i** pro jednotlivou registraci.

    V okně Výstup se zobrazí místně vygenerovaný certifikát X.509 podepsaný svým držitelem pro vaše simulované zařízení. Zkopírujte výstup do schránky od řádku **-----BEGIN CERTIFICATE-----** po první řádek **-----END CERTIFICATE-----** a ujistěte se, že kopírujete i oba tyto řádky. Z okna výstupu potřebujete jenom první certifikát.

5. Pomocí textového editoru uložte certifikát do nového souboru s názvem **_X509testcert.pem_**.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Vytvoření položky registrace zařízení na portálu

1. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete službu Zřizování zařízení.

2. Vyberte kartu **Spravovat zápisy** a nahoře vyberte tlačítko **Přidat jednotlivé zápisy.**

3. V panelu **Přidat zápis** zadejte následující informace a stiskněte tlačítko **Uložit.**

    * **Mechanismus:** Jako *Mechanismus* ověření identity vyberte **X.509**.
    * **Primární soubor .pem nebo .cer:** Zvolte **Vybrat soubor,** chcete-li vybrat soubor certifikátu X509testcert.pem, který jste vytvořili dříve.
    * **ID zařízení centra IoT Hub:** Jako ID zařízení zadejte **test-docs-cert-device**.

      [![Přidání individuální registrace pro atestaci X.509 na portálu](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Po úspěšné registraci se vaše zařízení X.509 zobrazí jako **riot-device-cert** ve sloupci *ID registrace* na kartě *Jednotlivé registrace*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulace první spouštěcí sekvence pro zařízení

V této části aktualizujete vzorový kód tak, aby odeslal spouštěcí sekvenci zařízení do instance služby Device Provisioning Service. Toto spouštěcí sekvence způsobí, že se zařízení rozpozná a přiřadí službě IoT Hub propojené s instancí služby Device Provisioning.

1. Na webu Azure Portal vyberte kartu **Přehled** pro službu Zřizování zařízení a poznamenejte si hodnotu **_oboru ID._**

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Samples**. Rozbalte ukázkový projekt s názvem **prov\_dev\_client\_sample**. Rozbalte **zdrojové soubory** a otevřete **prov\_dev\_client\_sample.c**.

3. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Ve stejném souboru vyhledejte definici funkce `main()`. Zkontrolujte, jestli je proměnná `hsm_type` nastavená na hodnotu `SECURE_DEVICE_TYPE_X509`, a ne na hodnotu `SECURE_DEVICE_TYPE_TPM`, jak je vidět dole.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**.

6. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Ve výzvě k opětovnému sestavení projektu vyberte **ano,** chcete-li projekt před spuštěním znovu sestavit.

    Následující výstup je příkladem úspěšného spuštění ukázky klienta zřizování zařízení a připojení k instanci služby zřizování pro získání informací o centru IoT hub a zajištění registrace:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. Na portálu přejděte do centra IoT propojeného s vaší zřizovací službou a vyberte kartu **Zařízení IoT.** Při úspěšném zřizování simulovaného zařízení X.509 do rozbočovače se jeho ID zařízení zobrazí na okně **zařízení IoT** s *funkcí STATUS* jako **povolenou**. Možná budete muset stisknout tlačítko **Aktualizovat** v horní části. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device/hub-registration.png) 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. Otevřete **Spravovat registrace** pro vaši službu a pak zaškrtněte kartu **Jednotlivé registrace.** Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. Otevřete **zařízení IoT** pro svůj rozbočovač, zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste na počítači s Windows vytvořili simulované zařízení X.509 a zřídíte ho do vašeho centra IoT hub pomocí služby Azure IoT Hub Device Provisioning Service na portálu. Chcete-li se dozvědět, jak zaregistrovat zařízení X.509 programově, pokračujte na rychlém startu pro programovou registraci zařízení X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
