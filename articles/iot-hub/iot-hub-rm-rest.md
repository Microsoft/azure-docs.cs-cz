---
title: Vytvoření služby Azure IoT hub pomocí zprostředkovatele prostředků REST API | Microsoft Docs
description: Jak používat rozhraní REST API poskytovatele prostředků k vytvoření služby IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 06f05da839ebca2ae53e255acce7f07d1989673c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635215"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Vytvoření služby IoT hub pomocí zprostředkovatele prostředků rozhraní API REST (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Můžete použít [zprostředkovatele prostředků služby IoT Hub REST API] [ lnk-rest-api] k vytváření a správě Azure IoT hubs prostřednictvím kódu programu. V tomto kurzu se dozvíte, jak vytvořit centrum IoT z programu v C# pomocí zprostředkovatele prostředků služby IoT Hub REST API.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se zabývá pomocí modelu nasazení Azure Resource Manager.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Azure PowerShell 1.0] [ lnk-powershell-install] nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V sadě Visual Studio vytvořit Visual C# Windows klasický desktopový projekt pomocí **konzolovou aplikaci (rozhraní .NET Framework)** šablona projektu. Název projektu **CreateIoTHubREST**.

2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **spravovat balíčky NuGet**.

3. Zkontrolujte v Správce balíčků NuGet **zahrnout předběžné verze**a na **Procházet** stránky hledání **Microsoft.Azure.Management.ResourceManager**. Vyberte balíček, klikněte na tlačítko **nainstalovat**v **zkontrolujte změny** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** tak, aby přijímal licence.

4. Vyhledejte v Správce balíčků NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klikněte na tlačítko **nainstalovat**v **zkontrolujte změny** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** tak, aby přijímal licence.

5. V Program.cs místo existující **pomocí** příkazy následujícím kódem:

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

6. V souboru Program.cs přidejte následující proměnné na statické nahraďte zástupný symbol hodnoty. Učinit poznámku o **ApplicationId**, **SubscriptionId**, **TenantId**, a **heslo** výše v tomto kurzu. **Název skupiny prostředků** je název skupiny prostředků, můžete použít, když vytvoříte Centrum IoT. Můžete použít existující nebo novou skupinu prostředků. **Název centra IoT** je název služby IoT Hub vytvoříte, jako například **MyIoTHub**. Název služby IoT hub musí být globálně jedinečný. **Název nasazení** , jako je název pro nasazení, **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Vytvoření služby IoT hub pomocí rozhraní REST API poskytovatele prostředků

Použití [zprostředkovatele prostředků služby IoT Hub REST API] [ lnk-rest-api] k vytvoření služby IoT hub ve vaší skupině prostředků. Zprostředkovatel prostředků REST API můžete taky provést změny do stávající služby IoT hub.

1. Do souboru Program.cs přidejte následující metodu:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Přidejte následující kód, který **CreateIoTHub** metoda. Tento kód vytvoří **HttpClient** objekt s ověřovací token v hlavičkách:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Přidejte následující kód, který **CreateIoTHub** metoda. Tento kód popisuje vytvoření služby IoT hub a generuje reprezentaci JSON. Aktuální seznam umístění, které podporují služby IoT Hub naleznete v části [stavu Azure][lnk-status]:

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

4. Přidejte následující kód, který **CreateIoTHub** metoda. Tento kód odešle žádost o REST do Azure. Kód pak odpověď a načte adresu URL můžete použít ke sledování stavu úlohy nasazení:

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

5. Přidejte následující kód do konce **CreateIoTHub** metoda. Tento kód používá **asyncStatusUri** získat adresu v předchozím kroku pro čekání na dokončení nasazení:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Přidejte následující kód do konce **CreateIoTHub** metoda. Tento kód načte klíče ze služby IoT hub jste vytvořili a vypíše je do konzoly:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Dokončení a spuštění aplikace

Aplikace je nyní možné dokončit voláním **CreateIoTHub** metoda předtím, než můžete sestavit a spustit ho.

1. Přidejte následující kód do konce **hlavní** metoda:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klikněte na tlačítko **sestavení** a potom **sestavení řešení**. Opravte všechny chyby.

3. Klikněte na tlačítko **ladění** a potom **spustit ladění** ke spuštění aplikace. Ho může trvat několik minut, než nasazení ke spuštění.

4. Pokud chcete ověřit, že vaše aplikace přidat nového centra IoT, přejděte [portál Azure] [ lnk-azure-portal] a zobrazení seznamu prostředků. Můžete taky použít **Get-AzureRmResource** rutiny prostředí PowerShell.

> [!NOTE]
> Tato ukázková aplikace přidá S1 Standard IoT Hub pro kterou se účtují. Po dokončení můžete odstranit službu IoT hub prostřednictvím [portál Azure] [ lnk-azure-portal] nebo pomocí **odebrat AzureRmResource** rutiny prostředí PowerShell po dokončení.

## <a name="next-steps"></a>Další postup
Nyní jste nasadili služby IoT hub pomocí rozhraní REST API poskytovatele prostředků, můžete chtít Další:

* Přečtěte si informace o možnostech [zprostředkovatele prostředků služby IoT Hub REST API][lnk-rest-api].
* Čtení [přehled Azure Resource Manageru] [ lnk-azure-rm-overview] Další informace o funkcích nástroje Azure Resource Manager.

Další informace o vývoji pro Centrum IoT, naleznete v následujících článcích:

* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
