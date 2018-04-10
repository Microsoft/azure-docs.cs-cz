---
title: Nastavení zařízení pro službu Azure IoT Hub Device Provisioning
description: Nastavení zařízení pro zřízení prostřednictvím služby IoT Hub Device Provisioning během procesu výroby zařízení
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c885e4d5d747d913eaf0b7137b240950e920e7ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Nastavení zařízení pro zřízení pomocí služby Azure IoT Hub Device Provisioning

V předchozím kurzu jste zjistili, jak ve službě Azure IoT Hub Device Provisioning nastavit automatické zřizování vašich zařízení pro centrum IoT. V tomto kurzu se dozvíte, jak nastavit své zařízení během výrobního procesu a umožnit tak jeho automatické zřízení pomocí služby IoT Hub. Vaše zařízení se zřídí na základě [mechanismu ověřování](concepts-device.md#attestation-mechanism) po prvním spuštění a připojení ke zřizovací službě. Tento kurz popisuje následující procesy:

> [!div class="checklist"]
> * Sestavení klientské sady SDK služby Device Provisioning specifické pro platformu
> * Extrahování artefaktů zabezpečení
> * Vytvoření softwaru pro registraci zařízení

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, vytvořte si instanci služby Device Provisioning a centrum IoT podle pokynů v předchozím kurzu [1 – Nastavení cloudových prostředků](./tutorial-set-up-cloud.md).

V tomto kurzu se používá [úložiště sad SDK a knihoven Azure IoT pro C](https://github.com/Azure/azure-iot-sdk-c), které obsahuje klientskou sadu SDK služby Device Provisioning pro C. Tato sada SDK aktuálně poskytuje podporu TPM a X.509 pro zařízení využívající implementace Windows nebo Linuxu. Tento kurz je založený na použití vývojového klienta pro Windows, který také předpokládá základní znalost sady Visual Studio 2017. 

Pokud neznáte proces automatického zřizování, nezapomeňte si přečíst o [konceptech automatického zřizování](concepts-auto-provisioning.md), než budete pokračovat. 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Sestavení specifické verze sady SDK pro platformu

Klientská sada SDK služby Device Provisioning pomáhá implementovat software pro registraci zařízení. Před jejím použitím však musíte sestavit specifickou verzi sady SDK pro platformu vašeho vývojového klienta a váš mechanismus ověřování. V tomto kurzu sestavíte sadu SDK využívající sadu Visual Studio 2017 na vývojové platformě Windows pro podporovaný typ ověřování:

1. Nainstalujte požadované nástroje a naklonujte úložiště GitHub, které obsahuje sadu SDK služby Device Provisioning pro C:

   a. Ujistěte se, že na svém počítači máte nainstalovanou sadu Visual Studio 2015 nebo [Visual Studio 2017](https://www.visualstudio.com/vs/). Pro vaši instalaci sady Visual Studio musíte mít povolenou sadu funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).

   b. Stáhněte a nainstalujte [sestavovací systém CMake](https://cmake.org/download/). Je důležité, aby sada Visual Studio se sadou funkcí Vývoj desktopových aplikací pomocí C++ byla na vašem počítači nainstalovaná ještě **před** zahájením instalace CMake.

   c. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git`, včetně **Git Bash**, prostředí Bash v příkazovém řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

   d. Otevřete Git Bash a naklonujte úložiště sad SDK a knihoven Azure IoT pro C. Dokončení příkazu clone může trvat několik minut, protože stahuje také několik závislých dílčích modulů:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. V nově vytvořeném podadresáři úložiště vytvořte nový podadresář `cmake`:

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. V příkazovém řádku Git Bash přejděte do podadresáře `cmake` úložiště azure-iot-sdk-c:

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. Sestavte sadu SDK pro svou vývojovou platformu a některý z podporovaných mechanismů ověřování pomocí některého z následujících příkazů (všimněte si také dvou znaků tečky na konci). Po dokončení CMake vytvoří podadresář `/cmake` s obsahem specifickým pro vaše zařízení:
    - Zařízení, která k ověřování používají certifikát fyzického zařízení TPM/HSM nebo simulovaného zařízení X.509:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - Zařízení, která k ověřování používají simulátor TPM:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

Teď jste připraveni pomocí sady SDK sestavit kód pro registraci vašeho zařízení. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrahování artefaktů zabezpečení 

Dalším krokem je extrakce artefaktů zabezpečení pro mechanismus ověřování, který vaše zařízení používá. 

### <a name="physical-device"></a>Fyzické zařízení 

Pokud jste sestavili sadu SDK tak, aby používala ověřování z fyzického zařízení TPM/HSM:

- U zařízení TPM je potřeba určit **Ověřovací klíč**, který mu přiřadil výrobce čipu TPM. Vytvořením hodnoty hash ověřovacího klíče můžete získat jedinečné **ID registrace** pro vaše zařízení TPM.  

- U zařízení X.509 je potřeba získat certifikáty vydané pro vaše zařízení – certifikáty koncové entity pro registrace jednotlivých zařízení a kořenové certifikáty pro skupinové registrace zařízení. 

### <a name="simulated-device"></a>Simulované zařízení

Pokud jste sestavili sadu SDK tak, aby používala ověřování z certifikátu simulovaného zařízení TPM nebo X.509:

- Simulované zařízení TPM:
   1. V samostatném nebo novém příkazovém řádku přejděte do podadresáře `azure-iot-sdk-c` a spusťte simulátor TPM. Ten naslouchá přes soket na portech 2321 a 2322. Toto příkazové okno nezavírejte, simulátor je potřeba nechat spuštěný až do konce následujícího rychlého startu. 

      Spuštěním následujícího příkazu v podadresáři `azure-iot-sdk-c` spusťte simulátor:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. Pomocí sady Visual Studio otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho pomocí příkazu Sestavit řešení v nabídce Sestavení.

   3. V podokně *Průzkumník řešení* v sadě Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **tpm_device_provision** a vyberte **Nastavit jako spouštěný projekt**. 

   4. Spusťte řešení pomocí některého z příkazů Spustit v nabídce Ladění. V okně výstupu se zobrazí **_ID registrace_** a **_Ověřovací klíč_** simulátoru TPM potřebné pro registraci zařízení. Zkopírujte tyto hodnoty pro pozdější použití. Toto okno (s ID registrace a ověřovacím klíčem) můžete zavřít, ale okno simulátoru TPM, které jste spustili v kroku 1, nechte spuštěné.

- Simulované zařízení X.509:
  1. Pomocí sady Visual Studio otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho pomocí příkazu Sestavit řešení v nabídce Sestavení.

  2. V podokně *Průzkumník řešení* v sadě Visual Studio přejděte do složky **Provision\_Tools**. Klikněte pravým tlačítkem na projekt **dice\_device\_enrollment** a vyberte **Nastavit jako spouštěný projekt**. 
  
  3. Spusťte řešení pomocí některého z příkazů Spustit v nabídce Ladění. Po zobrazení výzvy zadejte v okně Výstup **i** pro jednotlivou registraci. V okně Výstup se zobrazí místně vygenerovaný certifikát X.509 pro vaše simulované zařízení. Zkopírujte do schránky výstup začínající na *-----BEGIN CERTIFICATE-----* a končící na první řádek *-----END CERTIFICATE-----* a ujistěte se, že kopírujete i oba tyto řádky. Pamatujte, že z okna Výstup potřebujete pouze první certifikát.
 
  4. Vytvořte soubor **_X509testcert.pem_**, otevřete ho v libovolném textovém editoru a zkopírujte do něj obsah schránky. Soubor uložte, protože ho použijete později k registraci zařízení. Software pro registraci po spuštění používá stejný certifikát jako při automatickém zřizování.    

Tyto artefakty zabezpečení jsou potřeba při registraci vašeho zařízení do služby Device Provisioning. Zřizovací služba počká na budoucí spuštění a připojení zařízení. Při prvním spuštění zařízení logika klientské sady SDK ve spolupráci s čipem (nebo simulátorem) extrahuje ze zařízení artefakty zabezpečení a ověří registraci ve službě Device Provisioning. 

## <a name="create-the-device-registration-software"></a>Vytvoření softwaru pro registraci zařízení

Posledním krokem je napsat registrační aplikaci, která pomocí klientské sady SDK služby Device Provisioning zaregistruje zařízení ve službě IoT Hub. 

> [!NOTE]
> V tomto kroku budeme předpokládat použití simulovaného zařízení a spuštění ukázkové registrační aplikace sady SDK na vaší pracovní stanici. Stejné koncepty však platí i v případě, že vytváříte registrační aplikaci pro nasazení na fyzické zařízení. 

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning a zkopírujte hodnotu **_Rozsah ID_**. *Rozsah ID* je generovaný službou a zaručuje jedinečnost. Je neměnný a slouží k jednoznačné identifikaci ID registrací.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. V *Průzkumníku řešení* sady Visual Studio na vašem počítači přejděte do složky **Provision\_Samples**. Vyberte ukázkový projekt **prov\_dev\_client\_sample** a otevřete zdrojový soubor **prov\_dev\_client\_sample.c**.

3. Přiřaďte hodnotu _Rozsah ID_, kterou jste získali v kroku 1, do proměnné `id_scope` (odeberte levou `[` a pravou `]` závorku): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Pro srovnání vidíte proměnnou `global_prov_uri`, která umožňuje rozhraní API `IoTHubClient_LL_CreateFromDeviceAuth` pro registraci klienta služby IoT Hub připojit se k vyhrazené instanci služby Device Provisioning.

4. Ve funkci **main()** ve stejném souboru přidejte nebo zrušte komentář u proměnné `hsm_type` odpovídající mechanismu ověřování, který používá váš software pro registraci zařízení (TPM nebo X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Uložte provedené změny a znovu sestavte ukázku **prov\_dev\_client\_sample** výběrem možnosti Sestavit řešení v nabídce Sestavení. 

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** ve složce **Provision\_Samples** a vyberte **Nastavit jako spouštěný projekt**. Ukázkovou aplikaci ještě NESPOUŠTĚJTE.

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
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Sestavení klientské sady SDK služby Device Provisioning specifické pro platformu
> * Extrahování artefaktů zabezpečení
> * Vytvoření softwaru pro registraci zařízení

V dalším kurzu se dozvíte, jak zřídit zařízení pro vaše centrum IoT prostřednictvím jeho registrace do služby Azure IoT Hub Device Provisioning pro účely automatického zřízení.

> [!div class="nextstepaction"]
> [Zřízení zařízení pro centrum IoT](tutorial-provision-device-to-hub.md)

