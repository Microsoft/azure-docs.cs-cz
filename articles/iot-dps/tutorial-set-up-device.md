---
title: Kurz – nastavení zařízení pro Azure IoT Hub Device Provisioning Service
description: V tomto kurzu se dozvíte, jak můžete nastavit zařízení pro zřizování prostřednictvím IoT Hub Device Provisioning Service (DPS) během procesu výroby zařízení.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dd82b8ac3a510d1b16b0d2f42d3e50803162a119
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94959794"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Kurz: nastavení zařízení pro zřízení pomocí IoT Hub Device Provisioning Service Azure

V předchozím kurzu jste zjistili, jak ve službě Azure IoT Hub Device Provisioning nastavit automatické zřizování vašich zařízení pro centrum IoT. V tomto kurzu se dozvíte, jak nastavit své zařízení během výrobního procesu a umožnit tak jeho automatické zřízení pomocí služby IoT Hub. Vaše zařízení se zřídí na základě [mechanismu ověřování](concepts-service.md#attestation-mechanism) po prvním spuštění a připojení ke zřizovací službě. Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Sestavení klientské sady SDK služby Device Provisioning specifické pro platformu
> * Extrahování artefaktů zabezpečení
> * Vytvoření softwaru pro registraci zařízení

Tento kurz předpokládá, že jste si už vytvořili instanci služby Device Provisioning a IoT Hub podle pokynů v předchozím kurzu [Nastavení cloudových prostředků](tutorial-set-up-cloud.md).

V tomto kurzu se používá [úložiště sad SDK a knihoven Azure IoT pro C](https://github.com/Azure/azure-iot-sdk-c), které obsahuje klientskou sadu SDK služby Device Provisioning pro C. Tato sada SDK aktuálně poskytuje podporu TPM a X.509 pro zařízení využívající implementace Windows nebo Linuxu. Tento kurz je založen na použití vývojového klienta ve Windows, který také předpokládá základní znalosti se sadou Visual Studio. 

Pokud neznáte proces automatického zřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) a teprve potom pokračujte. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Následující požadavky jsou pro vývojové prostředí systému Windows. Informace o systému Linux nebo macOS najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci k sadě SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 se zapnutou úlohou [vývoj desktopových aplikací v jazyce C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Podporují se také sady Visual Studio 2015 a Visual Studio 2017.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Sestavení specifické verze sady SDK pro platformu

Klientská sada SDK služby Device Provisioning pomáhá implementovat software pro registraci zařízení. Před jejím použitím však musíte sestavit specifickou verzi sady SDK pro platformu vašeho vývojového klienta a váš mechanismus ověřování. V tomto kurzu sestavíte sadu SDK, která používá sadu Visual Studio na vývojové platformě Windows, pro podporovaný typ ověření identity:

1. Stáhněte si [sestavovací systém cmake](https://cmake.org/download/).

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

3. Otevřete prostředí příkazového řádku nebo Git Bash. Spuštěním následujících příkazů naklonujte nejnovější verzi úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) . Použijte značku, kterou jste našli v předchozím kroku, jako hodnotu `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

4. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Z adresáře spusťte následující příkazy `azure-iot-sdk-c` :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Sadu SDK pro svou vývojovou platformu sestavte na základě mechanismů ověřování, které budete používat. Použijte jeden z následujících příkazů (nezapomeňte u každého příkazu na dvě tečky na konci). Po dokončení CMake vytvoří podadresář `/cmake` s obsahem specifickým pro vaše zařízení:
 
    - Zařízení, která k ověřování používají simulátor TPM:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Všechna ostatní zařízení (fyzická zařízení s certifikátem TPM/HSM/X.509 nebo simulovaná zařízení s certifikátem X.509):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Teď jste připraveni pomocí sady SDK sestavit kód pro registraci vašeho zařízení. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrahování artefaktů zabezpečení 

Dalším krokem je extrakce artefaktů zabezpečení pro mechanismus ověřování, který vaše zařízení používá. 

### <a name="physical-devices"></a>Fyzická zařízení 

V závislosti na tom, jestli jste sestavili sadu SDK tak, aby používala ověřování pro fyzická zařízení s certifikáty TPM nebo HSM či certifikátem X.509, bude shromažďování artefaktů zabezpečení následující:

- U zařízení TPM je potřeba určit **Ověřovací klíč**, který mu přiřadil výrobce čipu TPM. Vytvořením hodnoty hash ověřovacího klíče můžete získat jedinečné **ID registrace** pro vaše zařízení TPM.  

- U zařízení X.509 je potřeba získat certifikáty vydané pro vaše zařízení. Služba zřizování zveřejňuje dva typy položek registrace, které kontrolují přístup u zařízení používajících mechanismus ověřování X.509. Potřebné certifikáty závisí na typech registrací, které budete používat.

    - Jednotlivé registrace: registrace pro jedno konkrétní zařízení. Tento typ položky registrace vyžaduje [certifikáty koncové entity typu „list“](concepts-x509-attestation.md#end-entity-leaf-certificate).
    
    - Skupiny registrací: tento typ položky registrace vyžaduje zprostředkující nebo kořenové certifikáty. Další informace najdete v části o [řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Simulovaná zařízení

V závislosti na tom, jestli jste sestavili sadu SDK, aby používala ověřování pro simulované zařízení pomocí certifikátů TPM nebo X.509, bude shromažďování artefaktů zabezpečení následující:

- Simulované zařízení TPM:

   1. Otevřete příkazový řádek systému Windows, přejděte do podadresáře `azure-iot-sdk-c` a spusťte simulátor TPM. Ten naslouchá přes soket na portech 2321 a 2322. Toto příkazové okno nezavírejte, simulátor je potřeba nechat spuštěný až do konce následujícího rychlého startu. 

      Spuštěním následujícího příkazu v podadresáři `azure-iot-sdk-c` spusťte simulátor:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Pokud pro tento krok používáte příkazový řádek Git Bash, budete muset změnit zpětná lomítka na lomítka – například: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Pomocí sady Visual Studio otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho pomocí příkazu Sestavit řešení v nabídce Sestavení.

   1. V podokně *Průzkumník řešení* v sadě Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **tpm_device_provision** a vyberte **Nastavit jako spouštěný projekt**. 

   1. Spusťte řešení pomocí některého z příkazů Spustit v nabídce Ladění. V okně výstupu se zobrazí **_ID registrace_** a **_Ověřovací klíč_** simulátoru TPM potřebné pro registraci zařízení. Zkopírujte tyto hodnoty pro pozdější použití. Toto okno (s ID registrace a ověřovacím klíčem) můžete zavřít, ale okno simulátoru TPM, které jste spustili v kroku 1, nechte spuštěné.

- Simulované zařízení X.509:

  1. Pomocí sady Visual Studio otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho pomocí příkazu Sestavit řešení v nabídce Sestavení.

  1. V podokně *Průzkumník řešení* v sadě Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **dice\_device\_enrollment** a vyberte **Nastavit jako spouštěný projekt**. 
  
  1. Spusťte řešení pomocí některého z příkazů Spustit v nabídce Ladění. Po zobrazení výzvy zadejte v okně Výstup **i** pro jednotlivou registraci. V okně Výstup se zobrazí místně vygenerovaný certifikát X.509 pro vaše simulované zařízení. Zkopírujte do schránky výstup začínající na *-----BEGIN CERTIFICATE-----* a končící na první řádek *-----END CERTIFICATE-----* a ujistěte se, že kopírujete i oba tyto řádky. Z okna Výstup potřebujete pouze první certifikát.
 
  1. Vytvořte soubor **_X509testcert.pem_**, otevřete ho v libovolném textovém editoru a zkopírujte do něj obsah schránky. Soubor uložte, protože ho použijete později k registraci zařízení. Software pro registraci po spuštění používá stejný certifikát jako při automatickém zřizování.    

Tyto artefakty zabezpečení jsou potřeba při registraci vašeho zařízení do služby Device Provisioning. Zřizovací služba počká na budoucí spuštění a připojení zařízení. Při prvním spuštění zařízení logika klientské sady SDK ve spolupráci s čipem (nebo simulátorem) extrahuje ze zařízení artefakty zabezpečení a ověří registraci ve službě Device Provisioning. 

## <a name="create-the-device-registration-software"></a>Vytvoření softwaru pro registraci zařízení

Posledním krokem je napsat registrační aplikaci, která pomocí klientské sady SDK služby Device Provisioning zaregistruje zařízení ve službě IoT Hub. 

> [!NOTE]
> V tomto kroku budeme předpokládat použití simulovaného zařízení a spuštění ukázkové registrační aplikace sady SDK na vaší pracovní stanici. Stejné koncepty však platí i v případě, že vytváříte registrační aplikaci pro nasazení na fyzické zařízení. 

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning a zkopírujte hodnotu **_Rozsah ID_**. *Rozsah ID* je generovaný službou a zaručuje jedinečnost. Je neměnný a slouží k jednoznačné identifikaci ID registrací.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. V *Průzkumníku řešení* sady Visual Studio na vašem počítači přejděte do složky **Provision\_Samples**. Vyberte ukázkový projekt **prov\_dev\_client\_sample** a otevřete zdrojový soubor **prov\_dev\_client\_sample.c**.

1. Přiřaďte hodnotu _Rozsah ID_, kterou jste získali v kroku 1, do proměnné `id_scope` (odeberte levou `[` a pravou `]` závorku): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Pro srovnání vidíte proměnnou `global_prov_uri`, která umožňuje rozhraní API `IoTHubClient_LL_CreateFromDeviceAuth` pro registraci klienta služby IoT Hub připojit se k vyhrazené instanci služby Device Provisioning.

1. Ve funkci **main()** ve stejném souboru přidejte nebo zrušte komentář u proměnné `hsm_type` odpovídající mechanismu ověřování, který používá váš software pro registraci zařízení (TPM nebo X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Uložte provedené změny a znovu sestavte ukázku **prov\_dev\_client\_sample** výběrem možnosti Sestavit řešení v nabídce Sestavení. 

1. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** ve složce **Provision\_Samples** a vyberte **Nastavit jako spouštěný projekt**. Ukázkovou aplikaci ještě NESPOUŠTĚJTE.

> [!IMPORTANT]
> Zařízení ještě nepouštějte. Proces musíte nejprve dokončit registrací zařízení ve službě Device Provisioning, a teprve pak můžete zařízení spustit. V části Další kroky níže najdete odkaz na další článek.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>Rozhraní API sady SDK použité během registrace (pouze pro srovnání)

Sada SDK pro srovnání poskytuje následující rozhraní API, která může vaše aplikace během registrace použít. Tato rozhraní API pomáhají zařízení s připojením ke službě Device Provisioning a registrací v ní, jakmile se spustí. Vaše zařízení pak získá informace potřebné k navázání připojení k vaší instanci služby IoT Hub:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Můžete také zjistit, že potřebujete svou aplikaci pro registraci klienta služby Device Provisioning upřesnit, nejprve pomocí simulovaného zařízení a pak pomocí nastavení testovací služby. Jakmile bude aplikace fungovat v testovacím prostředí, můžete ji sestavit pro své konkrétní zařízení a zkopírovat spustitelný soubor do image vašeho zařízení. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tuto chvíli pravděpodobně máte na portálu spuštěné služby Device Provisioning a IoT Hub. Pokud chcete přerušit nastavování zřizování zařízení nebo chcete tuto sérii kurzů dokončit později, doporučujeme tyto služby vypnout, aby se vám neúčtovaly zbytečné poplatky.

1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Sestavení klientské sady SDK služby Device Provisioning specifické pro platformu
> * Extrahování artefaktů zabezpečení
> * Vytvoření softwaru pro registraci zařízení

V dalším kurzu se dozvíte, jak zřídit zařízení pro vaše centrum IoT prostřednictvím jeho registrace do služby Azure IoT Hub Device Provisioning pro účely automatického zřízení.

> [!div class="nextstepaction"]
> [Zřízení zařízení pro centrum IoT](tutorial-provision-device-to-hub.md)