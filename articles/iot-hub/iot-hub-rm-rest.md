---
title: Vytvoření služby Azure IoT hub pomocí rozhraní REST API poskytovatele prostředků | Dokumentace Microsoftu
description: Jak používat rozhraní REST API poskytovatele prostředků k vytvoření služby IoT Hub.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 6d91f5e61dfd7c3cb4d1869edf0c6cb8c2c85190
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827517"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Vytvoření IoT hubu pomocí poskytovatel prostředků REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Můžete použít [rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) můžete vytvářet a spravovat služby Azure IoT hubs prostřednictvím kódu programu. V tomto kurzu se dozvíte, jak vytvořit IoT hub z programu v jazyce C# pomocí rozhraní REST API poskytovatele prostředků služby IoT Hub.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio.

* Aktivní účet Azure. Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V sadě Visual Studio, vytvořit pomocí Visual C# Windows klasický desktopový projekt **Konzolová aplikace (.NET Framework)** šablony projektu. Pojmenujte projekt **CreateIoTHubREST**.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a potom klikněte na tlačítko **spravovat balíčky NuGet**.

3. Ve správci balíčků NuGet zkontrolujte **zahrnout předběžné verze**a na **Procházet** stránkové vyhledávání pro **Microsoft.Azure.Management.ResourceManager**. Vyberte balíček, klikněte na tlačítko **nainstalovat**v **změny vyplývající z revize** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** tak, aby přijímal licence.

4. Vyhledejte ve správci balíčků NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klikněte na tlačítko **nainstalovat**v **změny vyplývající z revize** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** přijměte licenci.

5. V souboru Program.cs nahraďte existující **pomocí** příkazy následujícím kódem:

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

6. V souboru Program.cs přidejte následující proměnné na statické nahraďte zástupné hodnoty. Jste si poznamenali **ApplicationId**, **SubscriptionId**, **TenantId**, a **heslo** výše v tomto kurzu. **Název skupiny prostředků** je název skupiny prostředků, použijete při vytváření služby IoT hub. Můžete použít existující nebo nové skupiny prostředků. **Název služby IoT Hub** je název služby IoT Hub, můžete vytvořit, jako například **MyIoTHub**. Název služby IoT hub musí být globálně jedinečný. **Název nasazení** , jako je název pro nasazení, **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Vytvoření IoT hubu pomocí rozhraní REST API poskytovatele prostředků

Použití [rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) k vytvoření služby IoT hub ve vaší skupině prostředků. Provádět změny existující služby IoT hub můžete také použít rozhraní REST API poskytovatele prostředků.

1. Do souboru Program.cs přidejte následující metodu:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Přidejte následující kód, který **CreateIoTHub** metody. Tento kód vytvoří **HttpClient** objekt s ověřovací token do hlavičky:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Přidejte následující kód, který **CreateIoTHub** metody. Tento kód popisuje vytvoření služby IoT hub a generuje reprezentaci JSON. Aktuální seznam umístění, které podporují služby IoT Hub najdete v části [stav Azure](https://azure.microsoft.com/status/):

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

4. Přidejte následující kód, který **CreateIoTHub** metody. Tento kód odešle požadavek REST do Azure. Kód poté zkontroluje odpověď a načte adresu URL, které vám umožní monitorovat stav úlohy nasazení:

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

5. Přidejte následující kód do konce **CreateIoTHub** metody. Tento kód používá **asyncStatusUri** adresu získali v předchozím kroku, a počkejte na dokončení nasazení:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Přidejte následující kód do konce **CreateIoTHub** metody. Tento kód načte klíče ze služby IoT hub vytvořili a vypíše do konzoly:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Dokončení a spuštění aplikace

Aplikace je nyní možné dokončit voláním **CreateIoTHub** metoda před sestavit a spustit ho.

1. Přidejte následující kód do konce **hlavní** metody:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klikněte na tlačítko **sestavení** a potom **sestavit řešení**. Opravte všechny chyby.

3. Klikněte na tlačítko **ladění** a potom **spustit ladění** ke spuštění aplikace. Může trvat několik minut, než se nasazení pro spuštění.

4. Pokud chcete ověřit, že vaše aplikace přidá novou službu IoT hub, najdete [webu Azure portal](https://portal.azure.com/) a zobrazit seznam prostředků. Můžete taky použít **Get-AzResource** rutiny Powershellu.

> [!NOTE]
> Tato ukázková aplikace přidá S1 Standard služby IoT Hub pro kterou se vám účtuje. Až budete hotovi, můžete odstranit centrum IoT prostřednictvím [webu Azure portal](https://portal.azure.com/) nebo s použitím **odebrat AzResource** rutiny Powershellu, až budete hotovi.

## <a name="next-steps"></a>Další postup

Nyní jste nasadili IoT hubu pomocí rozhraní REST API poskytovatele prostředků, můžete dále zkoumat:

* Přečtěte si o možnostech [rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Čtení [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) Další informace o možnostech Azure Resource Manageru.

Další informace o vývoji pro službu IoT Hub, najdete v následujících článcích:

* [Seznámení s C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)