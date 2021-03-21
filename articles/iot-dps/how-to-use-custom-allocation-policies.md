---
title: Vlastní zásady přidělování pomocí Azure IoT Hub Device Provisioning Service
description: Jak používat vlastní zásady přidělování pro Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 14a405dbab0460f841a5e9104dbfeff101568f44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919170"
---
# <a name="how-to-use-custom-allocation-policies"></a>Postupy používání vlastních zásad přidělování

Vlastní zásady přidělení poskytují větší kontrolu nad tím, jak se zařízení přiřazují do služby IoT Hub. K tomu je potřeba pomocí vlastního kódu ve [službě Azure Function](../azure-functions/functions-overview.md) přiřazovat zařízení ke službě IoT Hub. Služba Device Provisioning zavolá váš kód funkce Azure, který poskytuje všechny relevantní informace o zařízení a registraci. Váš kód funkce se spustí a vrátí informace o službě IoT Hub použité ke zřízení zařízení.

Když použijete vlastní zásady přidělování, definujete vlastní zásady přidělování, když zásady poskytované službou Device Provisioning nesplňují požadavky vašeho scénáře.

Například možná budete chtít ověřit certifikát, který zařízení používá během zřizování, a přiřadit ho ke službě IoT Hub na základě vlastnosti certifikátu. Nebo, možná máte informace uložené v databázi pro vaše zařízení a potřebujete zadat dotaz na databázi, abyste zjistili, ke kterému centru IoT Hub se má zařízení přiřadit.

Tento článek ukazuje vlastní zásadu přidělování pomocí funkce Azure napsané v jazyce C#. Vytvoří se dvě nová centra IoT, která představují *divize informačních společností společnosti Contoso* a *divizi tepelného čerpadla společnosti Contoso*. Zařízení požadující zřizování musí mít ID registrace s jednou z následujících přípon, které se mají přijmout pro zřizování:

* **-Contoso-tstrsd-007**: divize pro informační společnosti Contoso
* **-Contoso-hpsd-088**: divize pro tepelné pumpy contoso

Zařízení se zřídí na základě jedné z těchto požadovaných přípon s ID registrace. Tato zařízení se budou simulovat pomocí ukázky zřizování zahrnuté v [sadě Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

V tomto článku provedete následující kroky:

* Použití rozhraní příkazového řádku Azure k vytvoření dvou oddělení IoT divize **společnosti Contoso (divize a části** se **tepelnými čerpadly contoso**)
* Vytvoření nové skupiny pro vlastní zásady přidělování pomocí funkce Azure Functions
* Vytvořte klíče zařízení pro dvě simulace zařízení.
* Nastavení vývojového prostředí pro sadu SDK Azure IoT C
* Simulovat zařízení a ověřit, jestli jsou zřízené podle ukázkového kódu ve vlastních zásadách přidělení

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Následující požadavky jsou pro vývojové prostředí systému Windows. Informace o systému Linux nebo macOS najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci k sadě SDK.

- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 se zapnutou úlohou [vývoj desktopových aplikací v jazyce C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Podporují se také sady Visual Studio 2015 a Visual Studio 2017.

- Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Vytvoření služby zřizování a dvou Center IoT Hub

V této části použijete Azure Cloud Shell k vytvoření služby zřizování a dvou rozbočovačů IoT, které představují **divize informačních společností společnosti Contoso** a **divizi vytápěcích čerpadel společnosti Contoso**.

> [!TIP]
> Příkazy použité v tomto článku vytvářejí službu zřizování a další prostředky v umístění Západní USA. Doporučujeme vytvořit prostředky v oblasti nejbližší vašemu, který podporuje službu Device Provisioning Service. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo na stránce [Stav Azure](https://azure.microsoft.com/status/) vyhledáním výrazu Služba Device Provisioning. V příkazech lze umístění zadat buď v jednom nebo více slovovém formátu; například: westus, Západní USA, západ USA atd. Hodnota nerozlišuje velká a malá písmena. Pokud k zadání umístění použijete víceslovný formát, uveďte hodnotu v uvozovkách, například `-- location "West US"`.
>

1. Pomocí Azure Cloud Shell vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

    Následující příklad vytvoří skupinu prostředků s názvem *Contoso-US-Resource-Group* v oblasti *westus* . Doporučuje se používat tuto skupinu pro všechny prostředky vytvořené v tomto článku. Tento přístup usnadňuje vyčištění po dokončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Pomocí Azure Cloud Shell vytvořte službu Device Provisioning Service (DPS) pomocí příkazu [AZ IoT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . Služba zřizování bude přidána do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří službu zřizování s názvem *Contoso-Provisioning-Service-1098* v umístění *westus* . Je nutné použít jedinečný název služby. Vytvořte vlastní příponu v názvu služby místo **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Dokončení tohoto příkazu může trvat několik minut.

3. Pomocí Azure Cloud Shell můžete pomocí příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) vytvořit **informační centrum společnosti Contoso s oddělením** IoT Hub. Centrum IoT se přidá do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří centrum IoT s názvem contoso- *-hub-1098* v umístění *westus* . Je nutné použít jedinečný název centra. Vytvořte vlastní příponu v názvu centra místo **1098**. 

    > [!CAUTION]
    > Ukázkový kód funkce Azure pro vlastní zásadu přidělení vyžaduje podřetězec `-toasters-` v názvu centra. Nezapomeňte použít název, který obsahuje požadovaný podřetězec informačních zpráv.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.

4. Pomocí Azure Cloud Shell vytvořte pomocí příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) vytvořit centrum IoT pro **tepelné pumpy společnosti Contoso** . Toto centrum IoT se taky přidá do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří centrum IoT s názvem *Contoso-heatpumps-hub-1098* v umístění *westus* . Je nutné použít jedinečný název centra. Vytvořte vlastní příponu v názvu centra místo **1098**. 

    > [!CAUTION]
    > Ukázkový kód funkce Azure pro vlastní zásadu přidělení vyžaduje podřetězec `-heatpumps-` v názvu centra. Nezapomeňte použít název, který obsahuje požadovaný podřetězec heatpumps.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.

5. Centra IoT musí být propojena s prostředkem DPS. 

    Spusťte následující dva příkazy a získejte připojovací řetězce pro rozbočovače, které jste právě vytvořili. Názvy prostředků centra nahraďte názvy, které jste zvolili v každém příkazu:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Spuštěním následujících příkazů propojte centra s prostředkem DPS. Nahraďte název prostředku DPS názvem, který jste zvolili v každém příkazu:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>Vytvoření vlastní alokační funkce

V této části vytvoříte funkci Azure, která implementuje vaše vlastní zásady přidělování. Tato funkce určuje, ke kterému centru IoT Hub má být zařízení zaregistrováno na základě toho, zda ID registrace obsahuje řetězec **-Contoso-tstrsd-007** nebo **-Contoso-hpsd-088**. Také nastaví počáteční stav vlákna zařízení na základě toho, zda je zařízení informační nebo tepelné čerpadlo.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Na domovské stránce vyberte **+ vytvořit prostředek**.

2. Do vyhledávacího pole *Hledat na Marketplace* zadejte "Function App". V rozevíracím seznamu vyberte **Function App** a pak vyberte **vytvořit**.

3. Na **Function App** vytvořit stránku na kartě **základy** zadejte následující nastavení pro novou aplikaci Function App a vyberte **zkontrolovat + vytvořit**:

    **Skupina prostředků**: vyberte **Contoso-US-Resource-Group** , aby se všechny prostředky vytvořené v tomto článku zachovaly společně.

    **Function App název**: Zadejte jedinečný název aplikace Function App. V tomto příkladu se používá **Contoso-Function-App-1098**.

    **Publikovat**: Ověřte, že je vybraný **kód** .

    **Zásobník modulu runtime**: v rozevíracím seznamu vyberte **.NET Core** .

    **Verze**: z rozevíracího seznamu vyberte **3,1** .

    **Oblast**: Vyberte stejnou oblast jako vaše skupina prostředků. Tento příklad používá **západní USA**.

    > [!NOTE]
    > Ve výchozím nastavení je povoleno Application Insights. Application Insights není pro tento článek nutný, ale může vám pomůže pochopit a prozkoumat všechny problémy, ke kterým dojde s vlastním přidělením. Pokud chcete, můžete Application Insights zakázat výběrem karty **monitorování** a výběrem možnosti **ne** pro **možnost Povolit Application Insights**.

    ![Vytvoření Function App Azure pro hostování vlastní alokační funkce](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na stránce **Souhrn** vyberte **vytvořit** a vytvořte aplikaci Function App. Nasazení může trvat několik minut. Až se dokončí, vyberte **Přejít k prostředku**.

5. V levém podokně na stránce **Přehled** aplikace Function App klikněte na **funkce** a potom na **+ Přidat** pro přidání nové funkce.

6. Na stránce **Přidat funkci** klikněte na **Trigger http** a pak klikněte na tlačítko **Přidat** .

7. Na další stránce klikněte na **kód + test**. To umožňuje upravit kód pro funkci s názvem **HttpTrigger1**. Soubor kódu **Run. csx** by měl být otevřen pro úpravy.

8. Odkaz na požadované balíčky NuGet. Chcete-li vytvořit počáteční vlákna zařízení, vlastní funkce přidělení používá třídy, které jsou definovány ve dvou balíčcích NuGet, které musí být načteny do hostitelského prostředí. S Azure Functions jsou balíčky NuGet odkazovány pomocí souboru *Function. proj* . V tomto kroku uložíte a nahrajete soubor *Function. proj* pro požadovaná sestavení.  Další informace najdete v tématu [použití balíčků NuGet s Azure Functions](../azure-functions/functions-reference-csharp.md#using-nuget-packages).

    1. Zkopírujte následující řádky do svého oblíbeného editoru a uložte soubor do svého počítače jako *Function. proj*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.16.3" />
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.27.0" />
            </ItemGroup>  
        </Project>
        ```

    2. Klikněte na tlačítko **nahrát** umístěné nad Editor kódu a nahrajte soubor *Function. proj* . Po odeslání vyberte soubor v editoru kódu pomocí rozevíracího seznamu pro ověření obsahu.

9. V editoru kódu se ujistěte, že je vybraná možnost *Spustit. csx* pro **HttpTrigger1** . Nahraďte kód funkce **HttpTrigger1** následujícím kódem a vyberte **Uložit**:

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

## <a name="create-the-enrollment"></a>Vytvořit registraci

V této části vytvoříte novou skupinu registrací, která používá vlastní zásady přidělování. V zájmu jednoduchosti Tento článek používá k registraci [symetrický klíč s ověřením identity](concepts-symmetric-key-attestation.md) . Pro bezpečnější řešení zvažte použití [ověření certifikátu X. 509](concepts-x509-attestation.md) s řetězem důvěryhodnosti.

1. Pořád na [Azure Portal](https://portal.azure.com)otevřete službu zřizování.

2. V levém podokně vyberte **spravovat registrace** a pak v horní části stránky vyberte tlačítko **Přidat skupinu** registrací.

3. Do pole **Přidat skupinu** registrací zadejte následující informace a klikněte na tlačítko **Uložit** .

    **Název skupiny**: zadejte **Contoso-customed-alokovaný-Devices**.

    **Typ ověření identity**: vyberte **symetrický klíč**.

    **Automaticky vygenerovat klíče**: Toto zaškrtávací políčko by již mělo být zaškrtnuté.

    **Vyberte, jak chcete přiřadit zařízení k** centrům: vyberte **vlastní (použijte funkci Azure Functions)**.

    **Předplatné**: vyberte předplatné, ve kterém jste službu Azure Function vytvořili.

    **Function App**: Vyberte svoji aplikaci Function App podle názvu. v tomto příkladu se použila **funkce contoso-Function-App-1098** .

    **Funkce**: vyberte funkci **HttpTrigger1** .

    ![Přidat vlastní skupinu pro zápis přidělení pro ověření symetrického klíče](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Po uložení registrace ho znovu otevřete a poznamenejte si **primární klíč**. Nejdřív musíte uložit registraci, aby se vygenerovaly klíče. Tento klíč se použije k vygenerování jedinečných klíčů zařízení pro simulovaná zařízení později.

## <a name="derive-unique-device-keys"></a>Odvodit jedinečné klíče zařízení

V této části vytvoříte dva jedinečné klíče zařízení. Pro simulované zařízení informačního zařízení se použije jeden klíč. Druhý klíč se použije pro simulované tepelné čerpadlo.

K vygenerování klíče zařízení použijete **primární klíč** , který jste si dříve poznamenali, k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ID registrace zařízení pro každé zařízení a výsledek převedete na Formát Base64. Další informace o vytváření odvozených klíčů zařízení pomocí skupin registrací najdete v části registrace skupin v tématu [ověření identity symetrického klíče](concepts-symmetric-key-attestation.md).

Pro příklad v tomto článku použijte následující dvě ID registrace zařízení a Vypočtěte klíč zařízení pro obě zařízení. ID registrace mají platnou příponu pro práci s ukázkovým kódem pro vlastní zásady přidělování:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Pokud používáte pracovní stanici se systémem Windows, můžete použít PowerShell k vygenerování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem** , který jste si poznamenali dříve.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
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

# <a name="linux"></a>[Linux](#tab/linux)

Pokud používáte pracovní stanici se systémem Linux, můžete použít OpenSSL k vygenerování odvozených klíčů zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem** , který jste si poznamenali dříve.

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

---

Simulovaná zařízení budou používat odvozené klíče zařízení s každým registračním ID k provedení ověření symetrického klíče.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

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

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**.

### <a name="simulate-the-contoso-toaster-device"></a>Simulace zařízení s informačními zprávami společnosti Contoso

1. Chcete-li simulovat zařízení informačního zařízení, vyhledejte volání `prov_dev_set_symmetric_key_info()` v části **prov \_ dev \_ Client \_ Sample. c** , který je přidán do komentáře.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně lomených závorek) ID registrace informačního zařízení a odvozeného klíče zařízení, který jste dříve vygenerovali. Hodnota klíče **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** uvedená níže je uvedena pouze jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Soubor uložte.

2. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** a spusťte řešení. V příkazovém řádku pro opětovné sestavení projektu vyberte **Ano** a znovu sestavte projekt před spuštěním.

    Následující výstup je příkladem simulovaného zařízení, které se úspěšně spouští a připojuje k instanci služby zřizování, která má být přiřazena ke službě IoT Hub pro vlastní zásady přidělování:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulace zařízení tepelného čerpadla contoso

1. Chcete-li simulovat zařízení tepelného čerpadla, aktualizujte volání `prov_dev_set_symmetric_key_info()` v nástroji **prov \_ dev \_ Client \_ Sample. c** znovu s ID registrace tepelného čerpadla a odvozeným klíčem zařízení, který jste dříve vygenerovali. Hodnota klíče **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** zobrazená níže je také uvedena jako příklad.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Soubor uložte.

2. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** a spusťte řešení. V příkazovém řádku pro opětovné sestavení projektu vyberte **Ano** pro opětovné sestavení projektu před spuštěním.

    Následující výstup je příkladem simulovaného tepelného zařízení, které se úspěšně spouští a připojuje k instanci služby zřizování, která je přiřazená k službě IoT Hub v zařízeních společnosti Contoso pomocí vlastních zásad přidělování:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Řešení potíží s vlastními zásadami přidělování

V následující tabulce jsou uvedeny očekávané scénáře a kódy chyb výsledků, které se mohou zobrazit. Tato tabulka vám umožní pomoct řešit problémy s chybami vlastního nastavení zásad přidělení s vaším Azure Functions.

| Scenario | Výsledek registrace ze služby zřizování | Zřizování výsledků sady SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook vrátí 200 OK s iotHubHostName nastavenou na platný název hostitele centra IoT Hub. | Stav výsledku: přiřazeno  | Sada SDK vrací PROV_DEVICE_RESULT_OK společně s informacemi z centra |
| Webhook vrátí 200 OK s ' iotHubHostName ' obsažený v odpovědi, ale nastavte na prázdný řetězec nebo hodnotu null. | Stav výsledku: neúspěšné<br><br> Kód chyby: CustomAllocationIotHubNotSpecified (400208) | Sada SDK vrací PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook vrátí 401 – Neautorizováno. | Stav výsledku: neúspěšné<br><br>Kód chyby: CustomAllocationUnauthorizedAccess (400209) | Sada SDK vrací PROV_DEVICE_RESULT_UNAUTHORIZED |
| Byl vytvořen jednotlivý zápis za účelem zakázání zařízení. | Stav výsledku: zakázáno | Sada SDK vrací PROV_DEVICE_RESULT_DISABLED |
| Webhook vrátí kód chyby >= 429. | Orchestrace DPS bude opakována kolikrát. Zásada opakování je aktuálně:<br><br>&nbsp;&nbsp;-Počet opakování: 10<br>&nbsp;&nbsp;-Počáteční interval: 1<br>&nbsp;&nbsp;-Přírůstek: devítky | Sada SDK bude ignorovat chybu a pošle do určeného času další zprávu o stavu Get. |
| Webhook vrátí jakýkoliv jiný stavový kód. | Stav výsledku: neúspěšné<br><br>Kód chyby: CustomAllocationFailed (400207) | Sada SDK vrací PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

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

* Další informace o opětovném zřízení najdete v tématu Koncepty opětovného [zřizování zařízení IoT Hub](concepts-device-reprovision.md) 
* Další informace o zrušení zřízení najdete v tématu [Postup zrušení zřízení zařízení, která byla dřív zřízena](how-to-unprovision-devices.md) .