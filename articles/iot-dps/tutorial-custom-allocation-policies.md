---
title: Kurz použití vlastních zásad přidělování s Azure IoT Hub Device Provisioning Service (DPS)
description: Kurz pro použití vlastních zásad přidělování s IoT Hub Device Provisioning Service Azure (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 42098786bb92e98b89b1dbfba01a2e071398f460
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258537"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Kurz: použití vlastních zásad přidělování se službou Device Provisioning Service (DPS)

Vlastní zásady přidělení poskytují větší kontrolu nad tím, jak se zařízení přiřazují do služby IoT Hub. To se provádí pomocí vlastního kódu ve [funkci Azure](../azure-functions/functions-overview.md) , která běží během zřizování při přiřazování zařízení do služby IoT Hub. Služba Device Provisioning zavolá váš kód funkce Azure, který poskytuje všechny relevantní informace o zařízení a registraci. Váš kód funkce se spustí a vrátí informace o službě IoT Hub použité ke zřízení zařízení.

Když použijete vlastní zásady přidělování, definujete vlastní zásady přidělování, když zásady poskytované službou Device Provisioning nesplňují požadavky vašeho scénáře.

Například možná budete chtít ověřit certifikát, který zařízení používá během zřizování, a přiřadit ho ke službě IoT Hub na základě vlastnosti certifikátu. Nebo, možná máte informace uložené v databázi pro vaše zařízení a potřebujete zadat dotaz na databázi, abyste zjistili, ke kterému centru IoT Hub se má zařízení přiřadit.

Tento článek popisuje skupinu registrací s vlastní zásadou přidělení, která používá funkci Azure napsanou v jazyce C# ke zřízení informačních zařízení pomocí symetrických klíčů. Jakékoli zařízení, které nebylo rozpoznáno jako informační zařízení, nebude zřízené do služby IoT Hub.

Zařízení si vyžádají zřizování pomocí ukázkového kódu, který je zahrnutý v [sadě Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).


V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření nové Function App Azure pro podporu vlastní funkce přidělení
> * Vytvoření nové skupiny pro vlastní zásady přidělování pomocí funkce Azure Functions
> * Vytváření klíčů zařízení pro dvě zařízení
> * Nastavení vývojového prostředí pro příklad kódu zařízení ze [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Spusťte zařízení a ověřte, jestli jsou zřízené podle vlastních zásad přidělování.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* V tomto článku se předpokládá, že jste dokončili kroky v části [nastavení IoT Hub Device Provisioning Service s Azure Portal](./quick-setup-auto-provision.md) k vytvoření instance IoT Hub a DPS.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

* Pro vývojové prostředí Windows se vyžaduje [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 se zapnutou úlohou [vývoj desktopových aplikací pomocí C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Podporují se také sady Visual Studio 2015 a Visual Studio 2017.

* V případě systému Linux nebo macOS si přečtěte příslušnou část v tématu [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci k [sadě SDK pro Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) .


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Vytvoření vlastní alokační funkce

V této části vytvoříte funkci Azure, která implementuje vaše vlastní zásady přidělování. Tato funkce určuje, jestli má být zařízení zaregistrované ve vašem IoT Hub na základě toho, jestli jeho ID registrace obsahuje předponu řetězce **Contoso-ininformační** zprávy.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Na domovské stránce vyberte **+ vytvořit prostředek**.

2. Do vyhledávacího pole *Hledat na Marketplace* zadejte "Function App". V rozevíracím seznamu vyberte **Function App** a pak vyberte **vytvořit**.

3. Na **Function App** vytvořit stránku na kartě **základy** zadejte následující nastavení pro novou aplikaci Function App a vyberte **zkontrolovat + vytvořit**:

    **Předplatné**: Pokud máte více předplatných a není vybráno požadované předplatné, vyberte předplatné, které chcete použít.

    **Skupina prostředků**: Toto pole umožňuje vytvořit novou skupinu prostředků, nebo vybrat existující, která bude obsahovat aplikaci Function App. Vyberte stejnou skupinu prostředků, která obsahuje IoT Hub, který jste vytvořili pro předchozí testování, například **TestResources**. Když umístíte všechny související prostředky do jedné skupiny, můžete je spravovat společně.

    **Function App název**: Zadejte jedinečný název aplikace Function App. V tomto příkladu se používá **Contoso-Function-App**.

    **Publikovat**: Ověřte, že je vybraný **kód** .

    **Zásobník modulu runtime**: v rozevíracím seznamu vyberte **.NET Core** .

    **Oblast**: Vyberte stejnou oblast jako vaše skupina prostředků. Tento příklad používá **západní USA**.

    > [!NOTE]
    > Ve výchozím nastavení je povoleno Application Insights. Application Insights není pro tento článek nutný, ale může vám pomůže pochopit a prozkoumat všechny problémy, ke kterým dojde s vlastním přidělením. Pokud chcete, můžete Application Insights zakázat výběrem karty **monitorování** a výběrem možnosti **ne** pro **možnost Povolit Application Insights**.

    ![Vytvoření Function App Azure pro hostování vlastní alokační funkce](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Na stránce **Souhrn** vyberte **vytvořit** a vytvořte aplikaci Function App. Nasazení může trvat několik minut. Až se dokončí, vyberte **Přejít k prostředku**.

5. V levém podokně v části **funkce** klikněte na **funkce** a potom **+ Přidat** přidejte novou funkci.

6. Na stránce šablony vyberte dlaždici **Trigger http** a pak vyberte **vytvořit funkci**. Vytvoří se funkce s názvem **HttpTrigger1** a na portálu se zobrazí stránka přehled pro vaši funkci.

7. Pro novou funkci klikněte na **kód + test** . Portál zobrazí obsah souboru kódu **Run. csx** . 

8. Nahraďte kód funkce **HttpTrigger1** následujícím kódem a vyberte **Save (Uložit**). Vlastní alokační kód je připravený k použití.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. Hned pod dolní část souboru kódu **Run. csx** , klikněte na **protokoly** a sledujte protokolování z vlastní funkce přidělení. 


## <a name="create-the-enrollment"></a>Vytvořit registraci

V této části vytvoříte novou skupinu registrací, která používá vlastní zásady přidělování. V zájmu jednoduchosti Tento článek používá k registraci [symetrický klíč s ověřením identity](concepts-symmetric-key-attestation.md) . Pro bezpečnější řešení zvažte použití [ověření certifikátu X. 509](concepts-x509-attestation.md) s řetězem důvěryhodnosti.

1. Pořád na [Azure Portal](https://portal.azure.com)otevřete službu zřizování.

2. V levém podokně vyberte **spravovat registrace** a pak v horní části stránky vyberte tlačítko **Přidat skupinu** registrací.

3. Do pole **Přidat skupinu** registrací zadejte informace v následující tabulce a klikněte na tlačítko **Uložit** .

    | Pole | Popis a/nebo navrhovaná hodnota |
    | :---- | :----------------------------- |
    | **Název skupiny** | Zadejte **Contoso-Custom-alokovaný – zařízení** |
    | **Typ ověření identity** | Vybrat **symetrický klíč** |
    | **Automaticky generovat klíče** | Toto zaškrtávací políčko by již mělo být zaškrtnuté. |
    | **Vyberte, jak chcete přiřadit zařízení k rozbočovačům.** | Vyberte **vlastní (použijte funkci Azure)** |
    | **Vyberte centra IoT, ke kterým se má tato skupina přiřadit.** | Vyberte centrum IoT, které jste předtím vytvořili, když jste dokončili rychlé spuštění. |
    | **Výběr funkce Azure Functions** | Vyberte předplatné, které obsahuje aplikaci funkcí, kterou jste vytvořili. Pak pro funkci vyberte **Contoso-Function-App** a **HttpTrigger1** . |

    ![Přidat vlastní skupinu pro zápis přidělení pro ověření symetrického klíče](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Po uložení registrace ho znovu otevřete a poznamenejte si **primární klíč**. Nejdřív musíte uložit registraci, aby se vygenerovaly klíče. Tento primární symetrický klíč se použije ke generování jedinečných klíčů zařízení pro zařízení, která se pokoušejí zřídit později. 

## <a name="derive-unique-device-keys"></a>Odvodit jedinečné klíče zařízení

Zařízení nepoužívají primární symetrický klíč přímo. Místo toho použijte primární klíč pro odvození klíče zařízení pro každé zařízení. V této části vytvoříte dva jedinečné klíče zařízení. Pro simulované zařízení informačního zařízení se použije jeden klíč. Druhý klíč se použije pro simulované tepelné čerpadlo. Generované klíče umožní, aby se obě zařízení pokusila o registraci. Pouze jedno ID registrace zařízení bude mít platnou příponu, která bude obsahovat vlastní ukázkový kód zásad přidělení. V důsledku toho bude přijat a druhý odmítnut

Chcete-li odvodit klíč zařízení, použijte symetrický klíč, který jste si předtím poznamenali, k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ID registrace zařízení pro každé zařízení a výsledek převeďte na Formát Base64. Další informace o vytváření odvozených klíčů zařízení pomocí skupin registrací najdete v části registrace skupin v tématu [ověření identity symetrického klíče](concepts-symmetric-key-attestation.md).

Pro příklad v tomto článku použijte následující dvě ID registrace zařízení s níže uvedeným kódem, abyste mohli vypočítat klíč zařízení pro obě zařízení:

* **Společnost Contoso-informační zpráva – 007**
* **contoso-heatpump-088**

Nahraďte hodnotu **klíčové** proměnné **primárním klíčem** , který jste si předtím poznamenali po vytvoření registrační skupiny. Hodnota klíče a výstup zobrazený pod kódem níže jsou pouze příkladem.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud používáte pracovní stanici se systémem Windows, můžete použít PowerShell k vygenerování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Pokud používáte pracovní stanici se systémem Linux, můžete použít OpenSSL k vygenerování odvozených klíčů zařízení, jak je znázorněno v následujícím příkladu bash.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

Zařízení si vyžádají zřizování pomocí ukázkového kódu, který je zahrnutý v [sadě Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

V této části připravíte vývojové prostředí používané pro sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sada SDK obsahuje vzorový kód pro simulované zařízení. Toto simulované zařízení se pokusí zřídit během spouštěcí sekvence zařízení.

Tato část se orientuje k pracovní stanici založené na systému Windows. Příklad pro Linux najdete v tématu nastavení virtuálních počítačů při [zřizování víceklientské architektury](how-to-provision-multitenant.md).

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

5. Spuštěním následujícího příkazu sestavte verzi sady SDK určenou pro platformu vašeho vývojového klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Pokud nenajde `cmake` kompilátor jazyka C++, může při spuštění příkazu dojít k chybám sestavení. Pokud k tomu dojde, zkuste spustit příkaz v [příkazovém řádku sady Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Simulace zařízení

V této části aktualizujete ukázku zřizování s názvem **prov \_ dev \_ Client \_ Sample** v sadě Azure IoT C SDK, kterou jste si nastavili dříve.

Tento ukázkový kód simuluje spouštěcí sekvenci zařízení, která odesílá požadavek na zřízení do instance služby Device Provisioning. Sekvence spouštění způsobí, že zařízení informačního zařízení bude rozpoznáno a přiřazeno ke službě IoT Hub pomocí vlastních zásad přidělování.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V sadě Visual Studio otevřete soubor řešení **azure_iot_sdks. sln** , který byl vygenerován starším spuštěním cmake. Soubor řešení by se měl nacházet v následujícím umístění:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Samples**. Rozbalte ukázkový projekt s názvem **prov\_dev\_client\_sample**. Rozbalte **zdrojové soubory** a otevřete **prov\_dev\_client\_sample.c**.

4. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Ve stejném souboru vyhledejte definici funkce `main()`. Zkontrolujte, jestli je proměnná `hsm_type` nastavená na hodnotu `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, jak je vidět dole:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Ve `main()` funkci Najděte volání `Prov_Device_Register_Device()` . Těsně před tímto voláním přidejte následující řádky kódu, které slouží [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) k předání vlastní datové části JSON během zřizování. Dá se použít k poskytnutí dalších informací vlastním funkcím přidělení. To se dá také použít k předání typu zařízení místo prověření ID registrace. Další informace o posílání a přijímání vlastních datových částí pomocí DPS najdete v tématu [přenos datových částí mezi zařízeními a DPS](how-to-send-additional-data.md).

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**.

### <a name="simulate-the-contoso-toaster-device"></a>Simulace zařízení s informačními zprávami společnosti Contoso

1. Chcete-li simulovat zařízení informačního zařízení, vyhledejte volání `prov_dev_set_symmetric_key_info()` v části **prov \_ dev \_ Client \_ Sample. c** , který je přidán do komentáře.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně lomených závorek) ID registrace informačního zařízení a odvozeného klíče zařízení, který jste dříve vygenerovali. Hodnota klíče **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** uvedená níže je uvedena pouze jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Soubor uložte.

2. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** a spusťte řešení. V příkazovém řádku pro opětovné sestavení projektu vyberte **Ano** a znovu sestavte projekt před spuštěním.

    Následující text je příkladem protokolování výstupu z vlastního kódu funkce přidělení spuštěného pro zařízení informačního zařízení. Všimněte si, že centrum se úspěšně vybralo pro informační zařízení. Všimněte si také `payload` člena, který obsahuje vlastní obsah JSON, který jste přidali do kódu. To je k dispozici pro váš kód pro použití v rámci `deviceRuntimeContext` .

    Toto protokolování je dostupné kliknutím na **protokoly** pod kódem funkce na portálu:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    Následující příklad výstupu zařízení zobrazuje simulované zařízení se systémem, které se úspěšně spouští a připojuje se k instanci služby zřizování, která má být přiřazena ke službě IoT Hub pro vlastní zásady přidělování:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulace zařízení tepelného čerpadla contoso

1. Chcete-li simulovat zařízení tepelného čerpadla, aktualizujte volání `prov_dev_set_symmetric_key_info()` v nástroji **prov \_ dev \_ Client \_ Sample. c** znovu s ID registrace tepelného čerpadla a odvozeným klíčem zařízení, který jste dříve vygenerovali. Hodnota klíče **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** zobrazená níže je také uvedena jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Soubor uložte.

2. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** a spusťte řešení. V příkazovém řádku pro opětovné sestavení projektu vyberte **Ano** pro opětovné sestavení projektu před spuštěním.

    Následující text je příkladem protokolování výstupu z vlastního kódu funkce přidělení spuštěného pro zařízení tepelného čerpadla. Vlastní zásada přidělení odmítne tuto registraci chybou HTTP 400 Chybný požadavek. Všimněte si `payload` , že člen obsahující vlastní obsah JSON, který jste přidali do kódu. To je k dispozici pro váš kód pro použití v rámci `deviceRuntimeContext` .

    Toto protokolování je dostupné kliknutím na **protokoly** pod kódem funkce na portálu:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    Následující příklad výstupu zařízení zobrazuje simulované spouštění zařízení a připojení k instanci služby zřizování, aby se pokusila o registraci do služby IoT Hub pomocí vlastních zásad přidělování. Tato chyba se nezdařila s chybou ( `Custom allocation failed with status code: 400` ), protože vlastní zásada přidělení byla navržena tak, aby umožňovala pouze informační zařízení:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s prostředky vytvořenými v tomto článku, můžete je nechat. Pokud nechcete prostředky dál používat, pomocí následujícího postupu odstraňte všechny prostředky vytvořené v tomto článku, abyste se vyhnuli zbytečným poplatkům.

V těchto krocích se předpokládá, že jste vytvořili všechny prostředky v tomto článku podle pokynů ve stejné skupině prostředků s názvem **Contoso-US-Resource-Group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT.
>

Odstranění skupiny prostředků podle názvu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Do textového pole **filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaše prostředky, **Contoso-US-Resource-Group**. 

3. Napravo od skupiny prostředků v seznamu výsledků vyberte **...** a pak **odstraňte skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Opětovným zadáním názvu skupiny prostředků potvrďte a pak vyberte **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

* Podrobnější příklad vlastní zásady přidělování najdete v tématu [Jak používat vlastní zásady přidělování](how-to-use-custom-allocation-policies.md).
* Další informace o opětovném zřízení najdete v tématu [IoT Hub konceptů opětovného zřízení zařízení](concepts-device-reprovision.md).
* Další informace o zrušení zřízení najdete v tématu [Jak zrušit zřízení zařízení, která byla dřív zřízena](how-to-unprovision-devices.md).