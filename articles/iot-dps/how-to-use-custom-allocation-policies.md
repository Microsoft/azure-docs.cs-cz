---
title: Jak používat vlastní přidělení zásady s Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Jak používat vlastní přidělení zásady s Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0229b83a1b19e422954879ea9660373a34b18002
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340045"
---
# <a name="how-to-use-custom-allocation-policies"></a>Použití vlastní přidělení zásad


Vlastní přidělení zásad vám dává větší kontrolu nad způsob přiřazování zařízení do služby IoT hub. Toho lze dosáhnout pomocí vlastního kódu v [funkce Azure Functions](../azure-functions/functions-overview.md) přiřazení zařízení do služby IoT hub. Službou device provisioning volá kód vaší funkce Azure poskytuje všechny relevantní informace o zařízení a registraci. Kód vaší funkce se spustí a vrátí informace o Centru IoT pro zřizování zařízení.

Pomocí zásad vlastní přidělení definovat vlastní zásady přidělování Pokud zásady, které poskytuje služba Device Provisioning nesplňuje požadavky na váš scénář.

Například možná jste chcete prozkoumat certifikát, který zařízení používá během zřizování a přiřazení zařízení do služby IoT hub na základě vlastnosti certifikátu. Možná může informacemi uloženými v databázi pro vaše zařízení a potřebují k dotazování databáze k určení, které by mělo být přiřazeno zařízení služby IoT hub.


Tento článek ukazuje vlastní přidělení zásad, pomocí funkce Azure napsané v jazyce C#. Dvě nové služby IoT hub se vytvářejí představující *divizi společnosti Contoso opékače* a *divizi společnosti Contoso Heat čerpadel*. Zařízení vyžadující zřizování, musí mít ID registrace s jedním z následujících přípon přijetí pro zřizování:

- **-contoso-tstrsd 007**: Opékače divizi společnosti Contoso
- **-contoso-hpsd 088**: Heat čerpadel divizi společnosti Contoso

Zařízení se zřídí na základě jedné z těchto požadovaných přípon na ID registrace. Tato zařízení budou simulované pomocí ukázkou zřizování součástí [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

V tomto článku provedete následující kroky:

* Pomocí Azure CLI k vytvoření dvou centra IoT divize společnosti Contoso (**divizi společnosti Contoso opékače** a **divizi společnosti Contoso Heat čerpadel**)
* Vytvoření nové skupiny registrace pomocí funkce Azure pro zásady vlastní přidělení
* Vytvoření klíče zařízení pro dva simulace zařízení.
* Nastavení vývojového prostředí pro Azure IoT C SDK
* Simulace zařízení, která chcete zobrazit, že se zřizují podle ukázkový kód vlastní přidělení zásad


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončení [nastavení služby IoT Hub Device Provisioning pomocí webu Azure portal](./quick-setup-auto-provision.md) rychlý start.
* Visual Studio 2015 nebo [Visual Studio 2017](https://www.visualstudio.com/vs/) s povolenou sadou funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)
* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Vytvoření dvou oddělených centra IoT hub

V této části použijete Azure Cloud Shell vytvořte dvě nové služby IoT hub představující **divizi společnosti Contoso opékače** a **divize společnosti Contoso Heat čerpadel**.

1. Použít Azure Cloud Shell vytvořte skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az-group-create) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

    Následující příklad vytvoří skupinu prostředků s názvem *contoso-us-resource-group* v *eastus* oblasti. Doporučujeme použít tuto skupinu pro všechny prostředky vytvořené v tomto článku. Tento přístup vám usnadní vyčištění po skončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Použít Azure Cloud Shell vytvořte **divizi společnosti Contoso opékače** službu IoT hub pro [az iot hub vytvořit](/cli/azure/iot/hub#az-iot-hub-create) příkazu. IoT hub se přidají do *contoso-us-resource-group*.

    Následující příklad vytvoří službu IoT hub s názvem *contoso opékače hub-. 1098* v *eastus* umístění. Je nutné použít vlastní název centra jedinečné. Tvoří vlastní přípony v názvu centra místo **. 1098**. Ukázkový kód pro vlastní přidělení zásad vyžaduje `-toasters-` v názvu centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Tento příkaz může trvat několik minut.

3. Použít Azure Cloud Shell vytvořte **divizi společnosti Contoso Heat čerpadel** službu IoT hub pro [az iot hub vytvořit](/cli/azure/iot/hub#az-iot-hub-create) příkazu. Toto centrum IoT se také zařadí do *contoso-us-resource-group*.

    Následující příklad vytvoří službu IoT hub s názvem *contoso-heatpumps-hub-. 1098* v *eastus* umístění. Je nutné použít vlastní název centra jedinečné. Tvoří vlastní přípony v názvu centra místo **. 1098**. Ukázkový kód pro vlastní přidělení zásad vyžaduje `-heatpumps-` v názvu centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Tento příkaz také může trvat několik minut.




## <a name="create-the-enrollment"></a>Vytvoření přihlášení

V této části vytvoříte novou skupinu registrace, který používá vlastní přidělení zásad. Pro zjednodušení tohoto článku používá [symetrického klíče ověření](concepts-symmetric-key-attestation.md) se registrace. Bezpečnější řešení, zvažte použití [ověření certifikátu X.509](concepts-security.md#x509-certificates) s řetěz certifikátů.

1. Přihlaste se k [webu Azure portal](http://portal.azure.com)a otevřete vaší instanci služby Device Provisioning.

2. Vyberte **Správa registrací** kartu a potom klikněte na tlačítko **přidat skupinu registrací** tlačítko v horní části stránky. 

3. Na **přidat skupinu registrací**, zadejte následující informace a klikněte na tlačítko **Uložit** tlačítko.

    **Název skupiny**: Zadejte **contoso-custom přidělené – zařízení**.

    **Typ ověření**: Vyberte **symetrický klíč**.

    **Automaticky vygenerovat klíče**: Toto políčko mělo být již zaškrtnuté.

    **Vyberte, jak chcete přiřadit zařízení k centrům**: Vyberte **vlastní (pomocí funkce Azure Functions)**.

    ![Přidat vlastní přidělení skupinu registrací pro ověření identity symetrického klíče](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. Na **přidat skupinu registrací**, klikněte na tlačítko **propojit novou službu IoT hub** propojení i nový intranetový centra IoT. 

    Tento krok je třeba spustit pro obě oddělení centra IoT hub.

    **Předplatné**: Pokud máte více předplatných, vyberte předplatné, ve které jste vytvořili oddělení centra IoT hub.

    **Služby IoT hub**: Vyberte jednu z oddělení rozbočovače, který jste vytvořili.

    **Zásady přístupu**: Zvolte **iothubowner**.

    ![Propojit oddělení IoT hub ve službě zřizování](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. Na **přidat skupinu registrací**, jakmile propojená i oddělení centra IoT hub, je nutné vybrat je jako skupiny služby IoT Hub pro skupiny registrací jak je znázorněno níže:

    ![Vytvořit skupinu oddělení rozbočovače pro přihlášení](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. Na **přidat skupinu registrací**, přejděte dolů k položce **vyberte funkce Azure Functions** části a klikněte na tlačítko **vytvořit novou aplikaci function app**.

7. Na **aplikace Function App** vytvořit stránku, že se otevře, zadejte následující nastavení pro nové funkce a klikněte na tlačítko **vytvořit**:

    **Název aplikace**: Zadejte název jedinečné funkce aplikace. **Contoso – funkce – aplikace –. 1098** se zobrazí jako příklad.

    **Skupina prostředků**: Vyberte **použít existující** a **contoso-us-resource-group** zachovat všechny prostředky vytvořené v tomto článku společně.

    **Application Insights**: Pro účely tohoto cvičení můžete vypnout to.

    ![Vytvoření aplikace funkcí](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Zpět na vaše **přidat skupinu registrací** stránky, ujistěte se, že je vybraná novou aplikaci function app. Budete muset znovu vyberte předplatné, které chcete aktualizovat seznam funkce aplikace.

    Po výběru novou aplikaci function app klikněte na tlačítko **vytvořit novou funkci**.

    ![Vytvoření aplikace funkcí](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Otevře se nová aplikace function app.

9. Na aplikace function app klikněte na tlačítko Vytvořit novou funkci

    ![Vytvoření aplikace funkcí](./media/how-to-use-custom-allocation-policies/new-function.png)

    Nové funkce, použít výchozí nastavení pro vytvoření nového **Webhook + API** pomocí **CSharp** jazyka. Klikněte na tlačítko **vytvořit tuto funkci**.

    Tím se vytvoří nové funkce jazyka C# s názvem **HttpTriggerCSharp1**.

10. Nahraďte kód pro nové funkce jazyka C# následujícím kódem a klikněte na tlačítko **Uložit**:    

    ```C#
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


11. Vraťte se na vaše **přidat skupinu registrací** stránce a ujistěte se, že je vybraná nové funkce. Budete muset znovu vybrat function app k aktualizaci seznamu funkcí.

    Po výběru nové funkci klikněte na tlačítko **Uložit** se uložit skupinu registrací.

    ![Nakonec uložte skupinu registrací](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Po uložení registraci, znovu ho otevřete a poznamenejte si, **primární klíč**. Registrace nejprve má klíčů vygenerovaných musíte uložit. Tento klíč se použije k vygenerování klíče jedinečné zařízení pro zařízení s Simulovaná později.


## <a name="derive-unique-device-keys"></a>Odvození klíče jedinečné zařízení

V této části vytvoříte dva jedinečné klíče. Jeden z nich se použije pro zařízení s Simulovaná toaster. Další klíč se použije pro zařízení s Simulovaná heat čerpadla.

Vygenerujte klíč zařízení, kterou použijete **primární klíč** jste si předtím poznamenali vypočítat [HMAC SHA256](https://wikipedia.org/wiki/HMAC) ID registrace zařízení pro každé zařízení a převodu výsledků do formátu Base64. Další informace o vytváření klíčů odvozené zařízení s skupin pro registraci, najdete v části registrace skupiny [symetrického klíče ověření](concepts-symmetric-key-attestation.md).

Například v tomto článku použijte následující dvě drs ID a výpočetní klíč zařízení i zařízení. Mají obě ID registrace platnou příponu pro práci s ukázkový kód pro vlastní přidělení zásad:

- **breakroom499 contoso tstrsd 007**
- **mainbuilding167 contoso hpsd 088**

#### <a name="linux-workstations"></a>Pracovní stanice pro Linux

Pokud používáte pracovní stanici systému Linux, vám pomůže openssl generujte svoje klíče odvozené zařízení, jak je znázorněno v následujícím příkladu.

1. Nahraďte hodnotu **klíč** s **primární klíč** jste si předtím poznamenali.

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


#### <a name="windows-based-workstations"></a>Pracovní stanice se systémem Windows

Pokud používáte pracovní stanici se systémem Windows, můžete použít PowerShell k vygenerování klíče odvozené zařízení jak je znázorněno v následujícím příkladu.

1. Nahraďte hodnotu **klíč** s **primární klíč** jste si předtím poznamenali.

    ```PowerShell
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

    ```PowerShell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


Simulovaná zařízení bude používat klíče odvozené zařízení s každou ID registrace k provedení ověření identity symetrického klíče.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

V této části připravíte vývojové prostředí použité k sestavení [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sada SDK zahrnuje ukázkový kód pro simulované zařízení. Toto simulované zařízení se pokusí zřídit během spouštěcí sekvence zařízení.

Tato část se orientované na pracovní stanici se systémem Windows. Příklad pro Linux, najdete v části Nastavení virtuálních počítačů ve skupině [jak zřídit pro víceklientskou architekturu](how-to-provision-multitenant.md).



1. Stáhněte si verzi 3.11.4 [sestavovacího systému CMake](https://cmake.org/download/). Stažený binární soubor ověřte pomocí odpovídající kryptografické hodnoty hash. Následující příklad používá Windows PowerShell k ověření kryptografické hodnoty hash pro verzi 3.11.4 distribuce x64 MSI:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    V době psaní tohoto textu byly na webu CMake uvedené tyto hodnoty hash pro verzi 3.11.4:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady Azure IoT C SDK:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Velikost tohoto úložiště je aktuálně přibližně 220 MB. Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.


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
    
    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

V této části budete aktualizovat ukázkou zřizování s názvem **prov\_dev\_klienta\_ukázka** nachází v sadě Azure IoT C SDK jste nastavili dříve. 

Tento ukázkový kód simuluje posloupnost spouštěcí zařízení, která odešle žádost o zřízení instance služby Device Provisioning. Pořadí spouštění způsobí, že toaster zařízení bude používat a přiřazené k IoT hubu pomocí zásad vlastní přidělení.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V sadě Visual Studio, otevřete **azure_iot_sdks.sln** soubor řešení, který byl vytvořen starší CMake. Soubor řešení by se měl nacházet v následujícím umístění:

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


#### <a name="simulate-the-contoso-toaster-device"></a>Simulace zařízení toaster Contoso

1. Pro simulaci zařízení toaster najít volání `prov_dev_set_symmetric_key_info()` v **prov\_dev\_klienta\_sample.c** což je označené jako komentář.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Zrušením komentáře u volání funkce a nahraďte zástupné hodnoty (včetně ostrých závorek) s ID registrace toaster a odvozené zařízení klíč, který jste vygenerovali dříve. Hodnota klíče **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** uvedené níže je pouze uvedeny jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Uložte soubor.

2. V nabídce sady Visual Studio vyberte **Ladit** > **Spustit bez ladění** a spusťte řešení. Po zobrazení výzvy k opětovnému sestavení projektu klikněte na **Ano** a před spuštěním projekt znovu sestavte.

    Následující výstup je příkladem toaster s Simulovaná zařízení úspěšně spouští a připojením k instanci služby zřizování pro přiřazení ke službě IoT hub opékače pomocí zásad vlastní přidělení:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simulace zařízení heat čerpadlo Contoso

1. Pro simulaci zařízení heat čerpadla, aktualizujte volání `prov_dev_set_symmetric_key_info()` v **prov\_dev\_klienta\_sample.c** znovu heat čerpadlo registrace ID a klíčem odvozené zařízení jste vygenerovali dříve . Hodnota klíče **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** uvedené níže je také pouze uvedeny jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Uložte soubor.

2. V nabídce sady Visual Studio vyberte **Ladit** > **Spustit bez ladění** a spusťte řešení. Po zobrazení výzvy k opětovnému sestavení projektu klikněte na **Ano** a před spuštěním projekt znovu sestavte.

    Následující výstup je příkladem simulované čerpadlo heat zařízení úspěšně spouští a připojením k instanci služby zřizování pro přiřazení ve společnosti Contoso heat čerpadel IoT hubu pomocí zásad vlastní přidělení:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Odstraňování potíží se zásadami vlastní přidělení

V následující tabulce jsou uvedeny očekávané scénáře a výsledky kódy chyb, ke kterým může dojít. Tato tabulka slouží k řešení potíží se vlastní zásady přidělováním s využitím Azure Functions.


| Scénář | Výsledek registrace ze služby zřizování | Zřizování výsledky sady SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook vrátí 200 OK iotHubHostName nastavena na platný název hostitele centra IoT | Výsledný stav: Přiřazené  | Sada SDK vrátí PROV_DEVICE_RESULT_OK společně s informacemi centra |
| Webhook se vrátí k dispozici v odpovědi 200 OK v "iotHubHostName", ale nastavit na prázdný řetězec nebo hodnotu null | Výsledný stav: Selhalo<br><br> Kód chyby: CustomAllocationIotHubNotSpecified (400208) | Sada SDK vrátí PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook vrátí 401 Neautorizováno | Výsledný stav: Selhalo<br><br>Kód chyby: CustomAllocationUnauthorizedAccess (400209) | Sada SDK vrátí PROV_DEVICE_RESULT_UNAUTHORIZED |
| Jednotlivé registrace byl vytvořen za účelem zakázání zařízení | Výsledný stav: Zakázáno | Sada SDK vrátí PROV_DEVICE_RESULT_DISABLED |
| Webhook vrátí kód chyby: > = 429 | Orchestrace distribučních bodů se bude opakovat několikrát. Zásady opakování je aktuálně:<br><br>&nbsp;&nbsp;-Počet opakování: 10<br>&nbsp;&nbsp;-Počátečního intervalu: 1s<br>&nbsp;&nbsp;-Inkrementace: 9s | Sada SDK bude chybu ignorovat a odeslat další get stavová zpráva v zadaném čase |
| Každý jiný stavový kód vrátí se webhook. | Výsledný stav: Selhalo<br><br>Kód chyby: CustomAllocationFailed (400207) | Sada SDK vrátí PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít pokračovat v práci s prostředky vytvořené v tomto článku, můžete je nechat. Pokud neplánujete pokračovat v používání prostředků, postupujte následovně Chcete-li odstranit všechny prostředky vytvořené podle tohoto článku, chcete-li se vyhnout zbytečným poplatkům.

Zde uvedené kroky předpokládají, všechny prostředky vytvořené v tomto článku podle pokynů ve stejné skupině prostředků s názvem **contoso-us-resource-group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Pokud chcete odstranit skupinu prostředků podle názvu:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. V **filtrovat podle názvu...**  textového pole zadejte název prostředku skupiny obsahující prostředky, **contoso-us-resource-group**. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další postup

- Přečtěte si další Reprovisioning, najdete v článku [reprovisoning koncepty zařízení centra IoT](concepts-device-reprovision.md) 
- Zrušení zřízení Další informace najdete v tématu [jak zrušit zřízení zařízení, které byly dříve automatické zřizování ](how-to-unprovision-devices.md) 











