---
title: Vlastní zásady přidělení se službou Azure IoT Hub DeviceProvisioning Service
description: Jak používat vlastní zásady přidělení se službou Azure IoT Hub DeviceProvisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453938"
---
# <a name="how-to-use-custom-allocation-policies"></a>Použití vlastních zásad přidělení

Vlastní zásady přidělení poskytuje větší kontrolu nad tím, jak jsou zařízení přiřazena k centru IoT Hub. Toho se provádí pomocí vlastního kódu ve [funkci Azure](../azure-functions/functions-overview.md) přiřadit zařízení k centru IoT. Služba zřizování zařízení volá kód funkce Azure, který poskytuje všechny relevantní informace o zařízení a registraci. Kód funkce se spustí a vrátí informace o centru IoT používané pro zřízení zařízení.

Pomocí vlastní zásady přidělení, definujete vlastní zásady přidělení, pokud zásady poskytované službou device provisioning služby nesplňují požadavky vašeho scénáře.

Například možná chcete zkontrolovat certifikát, který zařízení používá během zřizování, a přiřadit zařízení k centru IoT hub na základě vlastnosti certifikátu. Nebo možná máte informace uložené v databázi pro vaše zařízení a potřebujete dotaz ovat do databáze, abyste zjistili, ke kterému centru IoT by mělo být zařízení přiřazeno.

Tento článek ukazuje vlastní zásady přidělení pomocí funkce Azure napsané v C#. Jsou vytvořeny dva nové rozbočovače IoT, které představují *divizi Toustovačů Contoso* a *divizi tepelných čerpadel Contoso*. Zařízení požadující zřízení musí mít ID registrace s jedním z následujících přípon, které mají být přijaty pro zřizování:

* **-contoso-tstrsd-007**: Divize toustovačů Contoso
* **-contoso-hpsd-088**: Divize tepelných čerpadel Contoso

Zařízení budou zřízena na základě jedné z těchto požadovaných přípon na ID registrace. Tato zařízení budou simulována pomocí ukázky zřizování zahrnuté v [sadě Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

V tomto článku provedete následující kroky:

* Pomocí příkazového příkazového příkazového příkazu Azure můžete vytvořit dvě centra IoT divize Contoso **(divize Toustovačů Contoso a** **divize tepelných čerpadel Contoso)**
* Vytvoření nové registrace skupiny pomocí funkce Azure pro vlastní zásady přidělení
* Vytvořte klíče zařízení pro dvě simulace zařízení.
* Nastavení vývojového prostředí pro sadu Azure IoT C SDK
* Simulace zařízení a ověření, že jsou zřízena podle ukázkového kódu ve vlastní chodové zásady

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Následující předpoklady jsou pro vývojové prostředí systému Windows. Pro Linux nebo macOS, najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci sady SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 s povoleným temanem [pro vývoj plochy s C++.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Visual Studio 2015 a Visual Studio 2017 jsou také podporovány.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Vytvoření zřizovací služby a dvou divizních center IoT

V této části použijete Prostředí Azure Cloud Shell k vytvoření zřizovací služby a dvou center IoT představujících **divizi Contoso Toasters division** a **divizi tepelných čerpadel Contoso**.

> [!TIP]
> Příkazy použité v tomto článku vytvořit zřizovací služby a další prostředky v umístění západní USA. Doporučujeme vytvořit prostředky v nejbližší oblasti, která podporuje službu zřizování zařízení. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo na stránce [Stav Azure](https://azure.microsoft.com/status/) vyhledáním výrazu Služba Device Provisioning. V příkazech lze umístění zadat buď v jednom formátu slova nebo ve formátu s více slovy; například: westus, west US, WEST US atd. Hodnota není rozlišována malá a velká písmena. Pokud k zadání umístění použijete víceslovný formát, uveďte hodnotu v uvozovkách, například `-- location "West US"`.
>

1. Azure Cloud Shell slouží k vytvoření skupiny prostředků pomocí příkazu [az group create.](/cli/azure/group#az-group-create) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

    Následující příklad vytvoří skupinu prostředků s názvem *contoso-us-resource-group* v oblasti *westus.* Doporučujeme použít tuto skupinu pro všechny prostředky vytvořené v tomto článku. Tento přístup bude uklízet jednodušší po dokončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Azure Cloud Shell slouží k vytvoření služby zřizování zařízení pomocí [příkazu az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) Zřizování služby budou přidány do *contoso-us-resource-group*.

    Následující příklad vytvoří zřizovací službu s názvem *contoso-provisioning service-1098* v umístění *westus.* Je nutné použít jedinečný název služby. Místo roku **1098**si vytvořte vlastní příponu v názvu služby .

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Dokončení tohoto příkazu může trvat několik minut.

3. Azure Cloud Shell slouží k vytvoření centra **IoT divize Contoso Toanos** s příkazem [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) Centrum IoT bude přidáno do *skupiny contoso-us-resource- .*

    Následující příklad vytvoří centrum IoT s názvem *contoso-toasters-hub-1098* v umístění *westus.* Je nutné použít jedinečný název rozbočovače. Vytvořte si vlastní příponu v názvu rozbočovače místo **1098**. Ukázkový kód pro vlastní `-toasters-` zásady přidělení vyžaduje v názvu centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.

4. Azure Cloud Shell slouží k vytvoření centra IoT **divize tepelných čerpadel Contoso** s příkazem [az iot hub](/cli/azure/iot/hub#az-iot-hub-create) create. Toto centrum IoT bude také přidáno do *skupiny contoso-us-resource- .*

    Následující příklad vytvoří rozbočovač IoT s názvem *contoso-heatpumps-hub-1098* v umístění *westus.* Je nutné použít jedinečný název rozbočovače. Vytvořte si vlastní příponu v názvu rozbočovače místo **1098**. Ukázkový kód pro vlastní `-heatpumps-` zásady přidělení vyžaduje v názvu centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.

## <a name="create-the-custom-allocation-function"></a>Vytvoření vlastní funkce přidělení

V této části vytvoříte funkci Azure, která implementuje vlastní zásady přidělení. Tato funkce rozhoduje o tom, na které divizní centrum IoT má být zařízení zaregistrováno na základě toho, zda jeho ID registrace obsahuje řetězec **-contoso-tstrsd-007** nebo **-contoso-hpsd-088**. Nastavuje také počáteční stav dvojčete zařízení na základě toho, zda je zařízení toustovačem nebo tepelným čerpadlem.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Na domovské stránce vyberte **+ Vytvořit zdroj**.

2. Do *vyhledávacího* pole Hledat na Marketplace zadejte "Aplikace funkcí". V rozevíracím seznamu vyberte **Funkci aplikace**a pak vyberte **Vytvořit**.

3. Na stránce **vytvoření aplikace funkce** zadejte na kartě **Základy** následující nastavení nové aplikace funkcí a vyberte **Zkontrolovat + vytvořit**:

    **Skupina prostředků**: Vyberte **contoso-us-resource-group, chcete-li** zachovat všechny prostředky vytvořené v tomto článku pohromadě.

    **Název aplikace funkce**: Zadejte název aplikace s jedinečnou funkcí. Tento příklad používá **contoso-function-app-1098**.

    **Publikovat**: Ověřte, zda je vybrán **kód.**

    **Runtime Stack**: Z rozevíracího souboru vyberte **.NET Core.**

    **Oblast**: Vyberte stejnou oblast jako skupinu prostředků. Tento příklad používá **západní USA**.

    > [!NOTE]
    > Ve výchozím nastavení je application insights povolena. Application Insights není nutné pro tento článek, ale může vám pomoci pochopit a prozkoumat všechny problémy, se kterými se setkáte s vlastní přidělení. Pokud chcete, můžete zakázat Application Insights výběrem karty **Monitorování** a výběrem **možnosti Ne** pro **povolení přehledů aplikací**.

    ![Vytvoření aplikace Azure Function App pro hostování vlastní funkce přidělení](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na stránce **Souhrn** vyberte **Vytvořit** a vytvořte aplikaci funkcí. Nasazení může trvat několik minut. Po dokončení vyberte **Přejít na zdroj**.

5. V levém podokně stránky **Přehled** aplikace **+** funkce vyberte vedle **Funkce** a přidejte novou funkci.

    ![Přidání funkce do aplikace funkce](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Na stránce **Azure Functions for .NET – getting started** vyberte v kroku ZVOLIT PROSTŘEDÍ **NASAZENÍ** dlaždici **In-portal** a pak vyberte **Pokračovat**.

    ![Vyberte vývojové prostředí portálu](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Na další stránce vyberte pro krok **VYTVOŘIT FUNKCI** dlaždici **Webhook + API a** pak vyberte **Vytvořit**. Je vytvořena funkce s názvem **HttpTrigger1** a portál zobrazí obsah souboru kódu **run.csx.**

8. Odkaz vyžadoval balíčky Nuget. Chcete-li vytvořit počáteční dvojče zařízení, vlastní funkce přidělení používá třídy, které jsou definovány ve dvou balíčcích Nuget, které musí být načteny do hostitelského prostředí. S Funkcemi Azure jsou balíčky Nuget odkazovány pomocí souboru *function.host.* V tomto kroku uložíte a nahrajete soubor *function.host.*

    1. Zkopírujte následující řádky do oblíbeného editoru a uložte soubor do počítače jako *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. Ve funkci **HttpTrigger1** rozbalte kartu **Zobrazit soubory** na pravé straně okna.

        ![Otevřít soubory zobrazení](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Vyberte **Nahrát**, vyhledejte soubor **function.proj** a vyberte **Otevřít,** chcete-li soubor nahrát.

        ![Vybrat soubor pro nahrávání](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Nahraďte kód funkce **HttpTrigger1** následujícím kódem a vyberte **Uložit**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
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
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Vytvoření registrace

V této části vytvoříte novou skupinu registrace, která používá vlastní zásady přidělení. Pro jednoduchost tento článek používá [symetrické atestace klíče](concepts-symmetric-key-attestation.md) s zápisem. Pro bezpečnější řešení zvažte použití [certifikátu X.509 atestace](concepts-security.md#x509-certificates) s řetězcem důvěryhodnosti.

1. Stále na [webu Azure Portal](https://portal.azure.com)otevřete zřizovací službu.

2. V levém podokně vyberte **Spravovat zápisy** a v horní části stránky vyberte tlačítko **Přidat skupinu zápisů.**

3. V **části Přidat skupinu zápisů**zadejte následující informace a vyberte tlačítko **Uložit.**

    **Název skupiny**: Zadejte **contoso-custom-alokovaná zařízení**.

    **Typ atestace**: Vyberte **symetrický klíč**.

    **Automatické generování klíčů**: Toto zaškrtávací políčko by již mělo být zaškrtnuto.

    **Vyberte způsob přiřazení zařízení k rozbočovačům**: Vyberte **vlastní (Použijte funkci Azure).**

    ![Přidání vlastní skupiny registrace přidělení pro symetrické atestace klíče](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. V **části Přidat skupinu registrací**vyberte **Propojit nové centrum IoT a** propojit obě nová divizní centra IoT hub.

    Tento krok proveďte pro obě divizní centra IoT.

    **Předplatné**: Pokud máte více předplatných, zvolte předplatné, kde jste vytvořili divizní centra IoT.

    **IoT hub**: Vyberte jeden z divizních hubů, které jste vytvořili.

    **Zásady přístupu**: Zvolte **iothubowner**.

    ![Propojení divizních center IoT se službou zřizování](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. V **části Přidat skupinu pro zápis**je po propojení obou divizních center IoT nutné je vybrat jako skupinu IoT Hub pro skupinu zápisů, jak je znázorněno níže:

    ![Vytvoření skupiny divizních hubů pro registraci](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. V **části Přidat skupinu registrací**přejděte dolů do části **Vybrat funkci Azure** a vyberte aplikaci Function, kterou jste vytvořili v předchozí části. Pak vyberte funkci, kterou jste vytvořili, a vyberte Uložit, chcete-li skupinu zápisů uložit.

    ![Vyberte funkci a uložte skupinu zápisů.](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Po uložení registrace ji znovu otevřete a poznamenejte si **primární klíč**. Chcete-li vygenerovat klíče, musíte nejprve uložit registraci. Tento klíč bude později použit ke generování jedinečných klíčů zařízení pro simulovaná zařízení.

## <a name="derive-unique-device-keys"></a>Odvození jedinečných klíčů zařízení

V této části vytvoříte dvě jedinečné klíče zařízení. Jeden klíč bude použit pro simulované toustovač zařízení. Druhý klíč bude použit pro simulované zařízení tepelného čerpadla.

Chcete-li generovat klíč zařízení, použijte **primární klíč,** který jste si poznamenali dříve, k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ID registrace zařízení pro každé zařízení a převést výsledek do formátu Base64. Další informace o vytváření odvozených klíčů zařízení se skupinami zápisů naleznete v části skupiny zápisů [symetrického klíče atestace](concepts-symmetric-key-attestation.md).

Příklad v tomto článku použijte následující dvě ID registrace zařízení a vypočítejte klíč zařízení pro obě zařízení. Obě ID registrace mají platnou příponu pro práci s ukázkovým kódem pro vlastní zásady přidělení:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linuxové pracovní stanice

Pokud používáte pracovní stanici Linux, můžete použít openssl ke generování odvozených klíčů zařízení, jak je znázorněno v následujícím příkladu.

1. Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si poznamenali dříve.

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

Pokud používáte pracovní stanici založenou na systému Windows, můžete pomocí prostředí PowerShell vygenerovat odvozený klíč zařízení, jak je znázorněno v následujícím příkladu.

1. Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si poznamenali dříve.

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

Simulovaná zařízení budou používat odvozené klíče zařízení s každým ID registrace k provedení symetrického atestace klíče.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

V této části připravíte vývojové prostředí používané k sestavení [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sada SDK obsahuje ukázkový kód pro simulované zařízení. Toto simulované zařízení se pokusí zřídit během spouštěcí sekvence zařízení.

Tato část je orientována na pracovní stanici se systémem Windows. Příklad Linuxu najdete v tématu nastavení virtuálních počítače v [jak zřídit víceklientské .](how-to-provision-multitenant.md)

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

5. Spuštěním následujícího příkazu sestavte verzi sady SDK určenou pro platformu vašeho vývojového klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Pokud `cmake` nenajde kompilátor Jazyka C++, může dojít k chybám sestavení při spuštění příkazu. Pokud k tomu dojde, zkuste spustit příkaz v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

V této části aktualizujete ukázku zřizování s názvem **ukázka\_klienta\_\_prov dev** umístěnou v sadě Azure IoT C SDK, kterou jste nastavili dříve.

Tento ukázkový kód simuluje spouštěcí sekvenci zařízení, která odešle požadavek na zřízení instanci služby Device Provisioning Service. Spouštěcí sekvence způsobí, že toaster zařízení, které mají být rozpoznány a přiřazeny k centru IoT pomocí vlastní zásady přidělení.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V sadě Visual Studio otevřete soubor řešení **azure_iot_sdks.sln,** který byl vygenerován spuštěním CMake dříve. Soubor řešení by se měl nacházet v následujícím umístění:

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

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**.

### <a name="simulate-the-contoso-toaster-device"></a>Simulace zařízení totastru Contoso

1. Chcete-li simulovat toustovač `prov_dev_set_symmetric_key_info()` zařízení, najít volání v **prov\_dev\_klienta\_sample.c,** který je komentoval ven.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně úhlových závorek) ID registrace toustovače a odvozený klíč zařízení, který jste dříve vygenerovali. Hodnota klíče **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** uvedená níže je uvedena pouze jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Uložte soubor.

2. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Ve výzvě k opětovnému sestavení projektu vyberte **ano**, chcete-li projekt před spuštěním znovu sestavit.

    Následující výstup je příkladem simulovaného toastrového zařízení, které se úspěšně nastartuje a připojuje se k instanci služby zřizování, která má být přiřazena k rozbočovačům IoT hub pomocí vlastnízásady přidělení:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulace tepelného čerpadla Contoso

1. Chcete-li simulovat zařízení tepelného `prov_dev_set_symmetric_key_info()` čerpadla, aktualizujte volání v **prov\_dev\_client\_sample.c** znovu s ID registrace tepelného čerpadla a odvozeným klíčem zařízení, který jste vygenerovali dříve. Hodnota klíče **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** uvedená níže je také uvedena pouze jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Uložte soubor.

2. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Ve výzvě k opětovnému sestavení projektu vyberte **ano,** chcete-li projekt před spuštěním znovu sestavit.

    Následující výstup je příkladem simulovaného zařízení tepelného čerpadla, které úspěšně nastartuje a připojí k instanci služby zřizování, která má být přiřazena k centru IoT společnosti Contoso tepelných čerpadel podle zásad vlastní holokace:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Poradce při potížích s vlastními zásadami přidělení

V následující tabulce jsou uvedeny očekávané scénáře a kódy chyb výsledků, které se mohou zobrazit. V této tabulce můžete pomoci při řešení potíží s chybami zásad přidělení pomocí funkcí Azure.

| Scénář | Výsledek registrace ze služby Zřizování | Zřizování výsledků sady SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook vrátí 200 OK s 'iotHubHostName' nastavena na platný název hostitele centra IoT | Stav výsledku: Přiřazeno  | Sada SDK vrací PROV_DEVICE_RESULT_OK spolu s informacemi o rozbočovači. |
| Webhook vrátí 200 OK s 'iotHubHostName' v odpovědi, ale nastavena na prázdný řetězec nebo null | Stav výsledku: Nezdařilo se<br><br> Kód chyby: CustomAllocationIotHubNotSpecified (400208) | Sada SDK vrátí PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook vrátí 401 Neautorizovaný | Stav výsledku: Nezdařilo se<br><br>Kód chyby: CustomAllocationUnauthorizedAccess (400209) | Sada SDK vrátí PROV_DEVICE_RESULT_UNAUTHORIZED |
| Pro zakázání zařízení byla vytvořena individuální registrace. | Stav výsledku: Zakázáno | Sada SDK vrátí PROV_DEVICE_RESULT_DISABLED |
| Webhook vrátí kód chyby >= 429 | Orchestrace DPS bude opakovat několikrát. Zásady opakování je aktuálně:<br><br>&nbsp;&nbsp;- Počet opakování: 10<br>&nbsp;&nbsp;- Počáteční interval: 1s<br>&nbsp;&nbsp;- Přírůstek: 9s | Sada SDK bude chybu ignorovat a v určeném čase odešle další zprávu o stavu získání. |
| Webhook vrátí jakýkoli jiný stavový kód | Stav výsledku: Nezdařilo se<br><br>Kód chyby: Vlastní allocationFailed (400207) | Sada SDK vrátí PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci s prostředky vytvořené v tomto článku, můžete je opustit. Pokud nemáte v plánu pokračovat v používání prostředků, použijte následující kroky k odstranění všech prostředků vytvořených v tomto článku, abyste se vyhnuli zbytečným poplatkům.

Kroky zde předpokládají, že jste vytvořili všechny prostředky v tomto článku podle pokynů ve stejné skupině prostředků s názvem **contoso-us-resource-group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT.
>

Odstranění skupiny prostředků podle názvu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Do textového pole **Filtr podle názvu...** zadejte název skupiny prostředků obsahující **prostředky, contoso-us-resource-group**. 

3. Napravo od skupiny prostředků v seznamu výsledků vyberte **...** a pak **Odstranit skupinu prostředků**.

4. Budete vyzváni k potvrzení odstranění skupiny prostředků. Zadejte název skupiny prostředků znovu, abyste to potvrdili, a pak vyberte **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

* Další informace o opětovném zřízení najdete v tématu [Koncepty opětovného zřizování zařízení centra IoT Hub](concepts-device-reprovision.md) 
* Další informace o zrušení zřízení najdete v tématu [Jak zrušení zřízení zařízení, která byla dříve automaticky zřízena](how-to-unprovision-devices.md) 
