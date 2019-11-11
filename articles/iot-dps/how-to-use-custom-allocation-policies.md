---
title: Jak používat vlastní zásady přidělování pro Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Jak používat vlastní zásady přidělování pro Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 11872f8efcebf39edef2f97cd30c225edbe74bb4
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903567"
---
# <a name="how-to-use-custom-allocation-policies"></a>Jak používat vlastní zásady přidělování

Vlastní zásady přidělení poskytují větší kontrolu nad tím, jak se zařízení přiřazují do služby IoT Hub. K tomu je potřeba pomocí vlastního kódu ve [službě Azure Function](../azure-functions/functions-overview.md) přiřazovat zařízení ke službě IoT Hub. Služba Device Provisioning zavolá váš kód funkce Azure, který poskytuje všechny relevantní informace o zařízení a registraci. Váš kód funkce se spustí a vrátí informace o službě IoT Hub použité ke zřízení zařízení.

Když použijete vlastní zásady přidělování, definujete vlastní zásady přidělování, když zásady poskytované službou Device Provisioning nesplňují požadavky vašeho scénáře.

Například možná budete chtít ověřit certifikát, který zařízení používá během zřizování, a přiřadit ho ke službě IoT Hub na základě vlastnosti certifikátu. Nebo, možná máte informace uložené v databázi pro vaše zařízení a potřebujete zadat dotaz na databázi, abyste zjistili, ke kterému centru IoT Hub se má zařízení přiřadit.

Tento článek ukazuje vlastní zásadu přidělování pomocí funkce Azure napsané v C#. Vytvoří se dvě nová centra IoT, která představují *divize informačních společností společnosti Contoso* a *divizi tepelného čerpadla společnosti Contoso*. Zařízení požadující zřizování musí mít ID registrace s jednou z následujících přípon, které se mají přijmout pro zřizování:

* **-Contoso-tstrsd-007**: divize pro informační společnosti Contoso
* **-Contoso-hpsd-088**: divize pro tepelné pumpy contoso

Zařízení se zřídí na základě jedné z těchto požadovaných přípon s ID registrace. Tato zařízení se budou simulovat pomocí ukázky zřizování zahrnuté v [sadě Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

V tomto článku provedete následující kroky:

* Použití rozhraní příkazového řádku Azure k vytvoření dvou oddělení IoT divize**společnosti Contoso (divize a části** se **tepelnými čerpadly contoso**)
* Vytvoření nové skupiny pro vlastní zásady přidělování pomocí funkce Azure Functions
* Vytvořte klíče zařízení pro dvě simulace zařízení.
* Nastavení vývojového prostředí pro sadu SDK Azure IoT C
* Simulovat zařízení a ověřit, jestli jsou zřízené podle ukázkového kódu ve vlastních zásadách přidělení

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončení [nastavení IoT Hub Device Provisioning Service pomocí](./quick-setup-auto-provision.md) nástroje pro rychlý Start Azure Portal
* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 nebo novější s povolenou úlohou [" C++vývoj pro stolní počítače"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Vytváření dvou Center IoT Hub

V této části použijete Azure Cloud Shell k vytvoření dvou nových rozbočovačů IoT, které představují **divize informačních společností společnosti** contoso a **divizi tepelného čerpadla společnosti Contoso**.

1. Pomocí Azure Cloud Shell vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

    Následující příklad vytvoří skupinu prostředků s názvem *Contoso-US-Resource-Group* v oblasti *eastus* . Doporučuje se používat tuto skupinu pro všechny prostředky vytvořené v tomto článku. Tento přístup usnadňuje vyčištění po dokončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Pomocí Azure Cloud Shell můžete pomocí příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) vytvořit **informační centrum společnosti Contoso s oddělením** IoT Hub. Centrum IoT se přidá do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří centrum IoT s názvem contoso- *-hub-1098* v umístění *eastus* . Je nutné použít jedinečný název centra. Vytvořte vlastní příponu v názvu centra místo **1098**. Vzorový kód pro vlastní zásady přidělování vyžaduje `-toasters-` v názvu centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.

3. Pomocí Azure Cloud Shell vytvořte pomocí příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) vytvořit centrum IoT pro **tepelné pumpy společnosti Contoso** . Toto centrum IoT se taky přidá do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří centrum IoT s názvem *Contoso-heatpumps-hub-1098* v umístění *eastus* . Je nutné použít jedinečný název centra. Vytvořte vlastní příponu v názvu centra místo **1098**. Vzorový kód pro vlastní zásady přidělování vyžaduje `-heatpumps-` v názvu centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Dokončení tohoto příkazu může trvat i několik minut.

## <a name="create-the-enrollment"></a>Vytvořit registraci

V této části vytvoříte novou skupinu registrací, která používá vlastní zásady přidělování. V zájmu jednoduchosti Tento článek používá k registraci [symetrický klíč s ověřením identity](concepts-symmetric-key-attestation.md) . Pro bezpečnější řešení zvažte použití [ověření certifikátu X. 509](concepts-security.md#x509-certificates) s řetězem důvěryhodnosti.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete instanci služby Device Provisioning.

2. Vyberte kartu **spravovat registrace** a pak klikněte na tlačítko **Přidat skupinu** registrací v horní části stránky. 

3. Do pole **Přidat skupinu**registrací zadejte následující informace a klikněte na tlačítko **Uložit** .

    **Název skupiny**: zadejte **Contoso-customed-alokovaný-Devices**.

    **Typ ověření identity**: vyberte **symetrický klíč**.

    **Automaticky vygenerovat klíče**: Toto zaškrtávací políčko by již mělo být zaškrtnuté.

    **Vyberte, jak chcete přiřadit zařízení k**centrům: vyberte **vlastní (použijte funkci Azure Functions)** .

    ![Přidat vlastní skupinu pro zápis přidělení pro ověření symetrického klíče](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. V části **Přidat skupinu**registrací klikněte na **odkaz propojit nové centrum IoT** a propojte si vaše nová centra IoT Hub. 

    Tento krok proveďte pro obě vaše divize IoT Hub.

    **Předplatné**: Pokud máte více předplatných, vyberte předplatné, ve kterém jste vytvořili centra IoT Hub.

    **IoT Hub**: vyberte jedno ze špičkových rozbočovačů, které jste vytvořili.

    **Zásady přístupu**: vyberte **iothubowner**.

    ![Propojte centra IoT v divizi se službou zřizování.](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Když je v nabídce **Přidat skupinu**registrací propojená centra IoT, musíte je vybrat jako skupinu IoT Hub pro skupinu registrací, jak je znázorněno níže:

    ![Vytvoření skupiny oddělení invisioning pro registraci](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. V okně **Přidat skupinu**registrací přejděte dolů k části **funkce Azure** a klikněte na možnost **vytvořit novou aplikaci Function App**.

7. Na **Function App** vytvořit stránku, která se otevře, zadejte pro novou funkci následující nastavení a klikněte na **vytvořit**:

    **Název aplikace**: Zadejte jedinečný název aplikace Function App. **Contoso-Function-App-1098** se zobrazuje jako příklad.

    **Skupina prostředků**: vyberte **použít existující** a **Contoso-US-Resource-Group** , abyste zachovali všechny prostředky vytvořené v tomto článku.

    **Application Insights**: pro toto cvičení můžete tento postup vypnout.

    ![Vytvoření aplikace funkcí](./media/how-to-use-custom-allocation-policies/function-app-create.png)

8. Zpátky na stránce **Přidat skupinu** registrací se ujistěte, že je vybraná nová aplikace Function App. Možná budete muset znovu vybrat předplatné, aby se aktualizoval seznam aplikací funkcí.

    Po výběru nové aplikace Function App klikněte na **vytvořit novou funkci**.

    ![Vytvoření aplikace funkcí](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Otevře se nová aplikace Function App.

9. V aplikaci Function App klikněte na **+** a vytvořte novou funkci.

    ![Vytvoření aplikace funkcí](./media/how-to-use-custom-allocation-policies/new-function.png)

    Pro novou funkci použijte výchozí nastavení k vytvoření nového **Webhooku + rozhraní API** pomocí jazyka **CSharp** . Klikněte na **vytvořit tuto funkci**.

    Tím se vytvoří nová C# funkce s názvem **HttpTriggerCSharp1**.

10. Nahraďte kód nové C# funkce následujícím kódem a klikněte na **Uložit**:

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```

11. Vraťte se na stránku **Přidat skupinu registrace** a ujistěte se, že je vybraná nová funkce. Možná budete muset znovu vybrat aplikaci Function App a aktualizovat seznam funkcí.

    Po výběru nové funkce klikněte na **Uložit** a uložte skupinu registrace.

    ![Nakonec uložte skupinu registrace.](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

12. Po uložení registrace ho znovu otevřete a poznamenejte si **primární klíč**. Nejdřív musíte uložit registraci, aby se vygenerovaly klíče. Tento klíč se použije k vygenerování jedinečných klíčů zařízení pro simulovaná zařízení později.

## <a name="derive-unique-device-keys"></a>Odvodit jedinečné klíče zařízení

V této části vytvoříte dva jedinečné klíče zařízení. Pro simulované zařízení informačního zařízení se použije jeden klíč. Druhý klíč se použije pro simulované tepelné čerpadlo.

K vygenerování klíče zařízení použijete **primární klíč** , který jste si dříve poznamenali, k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ID registrace zařízení pro každé zařízení a výsledek převedete na Formát Base64. Další informace o vytváření odvozených klíčů zařízení pomocí skupin registrací najdete v části registrace skupin v tématu [ověření identity symetrického klíče](concepts-symmetric-key-attestation.md).

Pro příklad v tomto článku použijte následující dvě ID registrace zařízení a Vypočtěte klíč zařízení pro obě zařízení. ID registrace mají platnou příponu pro práci s ukázkovým kódem pro vlastní zásady přidělování:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Pracovní stanice Linux

Pokud používáte pracovní stanici se systémem Linux, můžete použít OpenSSL k vygenerování odvozených klíčů zařízení, jak je znázorněno v následujícím příkladu.

1. Nahraďte hodnotu **klíče** **primárním klíčem** , který jste si poznamenali dříve.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Pracovní stanice založené na systému Windows

Pokud používáte pracovní stanici se systémem Windows, můžete použít PowerShell k vygenerování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

1. Nahraďte hodnotu **klíče** **primárním klíčem** , který jste si poznamenali dříve.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

Simulovaná zařízení budou používat odvozené klíče zařízení s každým registračním ID k provedení ověření symetrického klíče.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

V této části připravíte vývojové prostředí používané pro sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sada SDK obsahuje vzorový kód pro simulované zařízení. Toto simulované zařízení se pokusí zřídit během spouštěcí sekvence zařízení.

Tato část se orientuje k pracovní stanici založené na systému Windows. Příklad pro Linux najdete v tématu nastavení virtuálních počítačů při [zřizování víceklientské architektury](how-to-provision-multitenant.md).

1. Stáhněte si [sestavovací systém cmake](https://cmake.org/download/).

    Před spuštěním instalace `CMake` je důležité, aby byly na počítači nainstalovány požadavky sady Visual Studio ( C++Visual Studio a "vývoj desktopových aplikací s"). Až budou požadavky splněné a bude ověřeno stažení, nainstalujte systém sestavení CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady Azure IoT C SDK:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

3. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Spuštěním následujícího příkazu sestavte verzi sady SDK určenou pro platformu vašeho vývojového klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Pokud `cmake` nenajde váš C++ kompilátor, může při spuštění příkazu dojít k chybám sestavení. Pokud k tomu dojde, zkuste spustit příkaz v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

V této části aktualizujete ukázku zřizování s názvem **prov\_dev\_client\_Sample** v sadě Azure IoT C SDK, kterou jste si nastavili dříve.

Tento ukázkový kód simuluje spouštěcí sekvenci zařízení, která odesílá požadavek na zřízení do instance služby Device Provisioning. Sekvence spouštění způsobí, že zařízení informačního zařízení bude rozpoznáno a přiřazeno ke službě IoT Hub pomocí vlastních zásad přidělování.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_** .

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V sadě Visual Studio otevřete soubor řešení **azure_iot_sdks. sln** , který byl vygenerován starším spuštěním cmake. Soubor řešení by se měl nacházet v následujícím umístění:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**. 

### <a name="simulate-the-contoso-toaster-device"></a>Simulace zařízení s informačními zprávami společnosti Contoso

1. Chcete-li simulovat zařízení informačního zařízení, vyhledejte volání `prov_dev_set_symmetric_key_info()` v **prov\_dev\_client\_Sample. c** , který je komentovaný.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně lomených závorek) ID registrace informačního zařízení a odvozeného klíče zařízení, který jste dříve vygenerovali. Hodnota klíče **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** uvedená níže je uvedena pouze jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Uložte soubor.

2. V nabídce sady Visual Studio vyberte **Ladit** > **Spustit bez ladění** a spusťte řešení. Po zobrazení výzvy ke znovusestavení projektu klikněte na **Ano** a před spuštěním projekt znovu sestavte.

    Následující výstup je příkladem simulovaného zařízení, které se úspěšně spouští a připojuje k instanci služby zřizování, která má být přiřazena ke službě IoT Hub pro vlastní zásady přidělování:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulace zařízení tepelného čerpadla contoso

1. Chcete-li simulovat zařízení tepelného čerpadla, aktualizujte volání `prov_dev_set_symmetric_key_info()` v **prov\_dev\_client\_Sample. c** s ID registrace tepelného čerpadla a odvozeným klíčem zařízení, který jste dříve vygenerovali. Hodnota klíče **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** zobrazená níže je také uvedena jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Uložte soubor.

2. V nabídce sady Visual Studio vyberte **Ladit** > **Spustit bez ladění** a spusťte řešení. V příkazovém řádku pro opětovné sestavení projektu klikněte na tlačítko **Ano** pro opětovné sestavení projektu před spuštěním.

    Následující výstup je příkladem simulovaného tepelného zařízení, které se úspěšně spouští a připojuje k instanci služby zřizování, která je přiřazená k službě IoT Hub v zařízeních společnosti Contoso pomocí vlastních zásad přidělování:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Řešení potíží s vlastními zásadami přidělování

V následující tabulce jsou uvedeny očekávané scénáře a kódy chyb výsledků, které se mohou zobrazit. Tato tabulka vám umožní pomoct řešit problémy s chybami vlastního nastavení zásad přidělení s vaším Azure Functions.

| Scénář | Výsledek registrace ze služby zřizování | Zřizování výsledků sady SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook vrátí 200 OK s iotHubHostName nastavenou na platný název hostitele centra IoT Hub. | Stav výsledku: přiřazeno  | Sada SDK vrací PROV_DEVICE_RESULT_OK společně s informacemi z centra |
| Webhook vrátí 200 OK s ' iotHubHostName ' obsažený v odpovědi, ale nastavte na prázdný řetězec nebo hodnotu null. | Stav výsledku: neúspěšné<br><br> Kód chyby: CustomAllocationIotHubNotSpecified (400208) | Sada SDK vrací PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook vrátí 401 – Neautorizováno. | Stav výsledku: neúspěšné<br><br>Kód chyby: CustomAllocationUnauthorizedAccess (400209) | Sada SDK vrací PROV_DEVICE_RESULT_UNAUTHORIZED |
| Byl vytvořen jednotlivý zápis za účelem zakázání zařízení. | Stav výsledku: zakázáno | Sada SDK vrací PROV_DEVICE_RESULT_DISABLED |
| Webhook vrátí kód chyby > = 429. | Orchestrace DPS bude opakována kolikrát. Zásada opakování je aktuálně:<br><br>&nbsp;&nbsp;-počet opakování: 10<br>&nbsp;&nbsp;– počáteční interval: 1<br>&nbsp;&nbsp;-přírůstek: devítky | Sada SDK bude ignorovat chybu a pošle do určeného času další zprávu o stavu Get. |
| Webhook vrátí jakýkoliv jiný stavový kód. | Stav výsledku: neúspěšné<br><br>Kód chyby: CustomAllocationFailed (400207) | Sada SDK vrací PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s prostředky vytvořenými v tomto článku, můžete je nechat. Pokud nechcete prostředky dál používat, pomocí následujícího postupu odstraňte všechny prostředky vytvořené v tomto článku, abyste se vyhnuli zbytečným poplatkům.

V těchto krocích se předpokládá, že jste vytvořili všechny prostředky v tomto článku podle pokynů ve stejné skupině prostředků s názvem **Contoso-US-Resource-Group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Odstranění skupiny prostředků podle názvu:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. Do textového pole **filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaše prostředky, **Contoso-US-Resource-Group**. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

* Další informace o opětovném zřízení najdete v tématu Koncepty opětovného [zřizování zařízení IoT Hub](concepts-device-reprovision.md) 
* Další informace o zrušení zřízení najdete v tématu [Postup zrušení zřízení zařízení, která byla dřív zřízena](how-to-unprovision-devices.md) . 
