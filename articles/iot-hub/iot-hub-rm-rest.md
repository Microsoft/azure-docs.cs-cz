---
title: Vytvoření centra Azure IoT pomocí rozhraní REST API poskytovatele prostředků | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí poskytovatele prostředků C# ROZHRANÍ REST API vytvořit a spravovat službu IoT Hub programově.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429162"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Vytvoření centra IoT pomocí rozhraní REST API zprostředkovatele prostředků (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pomocí rozhraní [REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) můžete vytvářet a spravovat centra Azure IoT programově. Tento kurz ukazuje, jak pomocí rozhraní REST API poskytovatele prostředků služby IoT Hub vytvořit centrum IoT z programu C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V sadě Visual Studio vytvořte projekt klasické plochy systému Visual C# windows classic pomocí šablony projektu **Console App (.NET Framework).** Název projektu **CreateIoTHubREST**.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a potom klikněte na **spravovat balíčky NuGet**.

3. Ve Správci balíčků NuGet **zaškrtněte políčko Zahrnout předběžnou verzi**a na stránce **Procházet** **microsoft.Azure.Management.ResourceManager**. Vyberte balíček, klikněte na **Instalovat**, v **části Revize změn** klikněte na **OK**a potom klikněte na **Souhlasím,** abyste licence přijali.

4. Ve Správci balíčků NuGet vyhledejte **soubor Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klepněte na **tlačítko Instalovat**, v **části Revize změn** klepněte na tlačítko **OK**a potom klepnutím na tlačítko **Souhlasím** licenci přijměte.

5. V Program.cs nahraďte existující **příkazy using** následujícím kódem:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. V Program.cs přidejte následující statické proměnné nahrazující zástupné hodnoty. Jste si poznamenejte **ApplicationId**, **SubscriptionId**, **TenantId**a **heslo** dříve v tomto kurzu. **Název skupiny prostředků** je název skupiny prostředků, kterou používáte při vytváření centra IoT Hub. Můžete použít již existující nebo novou skupinu prostředků. **Název ioT hubu** je název vytvářeného centra IoT Hub, například **MyIoTHub**. Název vašeho centra IoT musí být globálně jedinečný. **Název nasazení** je název nasazení, například **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Vytvoření centra IoT hub pomocí rozhraní REST API poskytovatele prostředků

Pomocí [rozhraní REST API poskytovatele centra IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) vytvořte centrum IoT ve vaší skupině prostředků. Pomocí rozhraní REST API poskytovatele prostředků můžete také provádět změny v existujícím centru IoT.

1. Do Program.cs přidejte následující metodu:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Přidejte následující kód do metody **CreateIoTHub.** Tento kód vytvoří objekt **HttpClient** s ověřovacím tokenem v záhlaví:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Přidejte následující kód do metody **CreateIoTHub.** Tento kód popisuje centrum IoT k vytvoření a generuje reprezentaci JSON. Aktuální seznam lokalit, které podporují Službu IoT Hub, najdete [v tématu Stav Azure](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Přidejte následující kód do metody **CreateIoTHub.** Tento kód odešle požadavek REST do Azure. Kód pak zkontroluje odpověď a načte adresu URL, kterou můžete použít ke sledování stavu úlohy nasazení:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Přidejte následující kód na konec **metody CreateIoTHub.** Tento kód používá adresu **asyncStatusUri** načtenou v předchozím kroku k čekání na dokončení nasazení:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Přidejte následující kód na konec **metody CreateIoTHub.** Tento kód načte klíče služby IoT hub, které jste vytvořili, a vytiskne je do konzoly:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Dokončení a spuštění aplikace

Nyní můžete dokončit aplikaci voláním **CreateIoTHub** metoda před sestavením a spuštěním.

1. Na konec **metody Main** přidejte následující kód:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klepněte na **tlačítko Sestavit** a potom **na vytvořit řešení**. Opravte všechny chyby.

3. Klikněte na **Ladění** a **potom spusťte** ladění a spusťte aplikaci. Spuštění nasazení může trvat několik minut.

4. Pokud chcete ověřit, že vaše aplikace přidala nové centrum IoT, navštivte [portál Azure](https://portal.azure.com/) a zobrazte seznam prostředků. Případně použijte rutinu **Get-AzResource** PowerShell.

> [!NOTE]
> Tato ukázková aplikace přidá sbíhá S1 Standard IoT Hub, za který se vám účtují. Po dokončení můžete odstranit službu IoT hub prostřednictvím [portálu Azure](https://portal.azure.com/) nebo pomocí rutiny **Remove-AzResource** PowerShell po dokončení.

## <a name="next-steps"></a>Další kroky

Teď jste nasadili centrum IoT pomocí rozhraní REST API poskytovatele prostředků, možná budete chtít prozkoumat další:

* Přečtěte si o možnostech [rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Další informace o možnostech Azure Resource Manageru najdete v [přehledu](../azure-resource-manager/management/overview.md) správce prostředků Azure.

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)