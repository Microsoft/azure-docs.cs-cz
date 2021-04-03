---
title: Vytvoření centra IoT Azure pomocí poskytovatele prostředků REST API | Microsoft Docs
description: Naučte se, jak pomocí poskytovatele prostředků C# REST API vytvořit a spravovat IoT Hub programově.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144433"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Vytvoření služby IoT Hub pomocí REST API poskytovatele prostředků (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pomocí [poskytovatele prostředků IoT Hub REST API](/rest/api/iothub/iothubresource) můžete programově vytvářet a spravovat centra IoT Azure. V tomto kurzu se dozvíte, jak používat IoT Hub poskytovatele prostředků REST API k vytvoření centra IoT z programu v jazyce C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto kurzu potřebujete:

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* [Azure PowerShell 1,0](/powershell/azure/install-Az-ps) nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V aplikaci Visual Studio vytvořte klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)** . Pojmenujte projekt **CreateIoTHubREST**.

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt a pak klikněte na **Spravovat balíčky NuGet**.

3. Ve Správci balíčků NuGet **Zahrňte zahrnutí předběžné verze** a na stránce **Procházet** vyhledejte **Microsoft. Azure. Management. ResourceManager**. Vyberte balíček, klikněte na **nainstalovat**. v části **Zkontrolovat změny** klikněte na **OK** a **pak kliknutím na Souhlasím** přijměte licence.

4. Ve Správci balíčků NuGet vyhledejte **Microsoft. IdentityModel. clients. Active**.  Klikněte na **nainstalovat**, v části **Zkontrolovat změny** klikněte na **OK** a pak **klikněte na Souhlasím, aby se** licence přijímala.

5. V programu program. cs nahraďte existující příkazy **using** následujícím kódem:

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

6. V programu program. cs přidejte následující statické proměnné, které nahradí zástupné hodnoty. Dříve v tomto kurzu jste si poznamenali **ApplicationId**, **SubscriptionId**, **TenantId** a **Password** . **Název skupiny prostředků** je název skupiny prostředků, kterou použijete při vytváření centra IoT. Můžete použít již existující nebo novou skupinu prostředků. **Název IoT Hub** je název vytvořeného IoT Hub, například **MyIoTHub**. Název vašeho centra IoT musí být globálně jedinečný. **Název nasazení** je název pro nasazení, například **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Vytvoření centra IoT pomocí REST API poskytovatele prostředků

[REST API poskytovatele prostředků IoT Hub](/rest/api/iothub/iothubresource) použijte k vytvoření centra IoT ve vaší skupině prostředků. K provedení změn v existující službě IoT Hub můžete také použít REST API poskytovatele prostředků.

1. Do programu program. cs přidejte následující metodu:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Do metody **CreateIoTHub** přidejte následující kód. Tento kód vytvoří objekt **HttpClient** s ověřovacím tokenem v hlavičkách:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Do metody **CreateIoTHub** přidejte následující kód. Tento kód popisuje, jak centrum IoT vytvoří a vygeneruje reprezentaci JSON. Aktuální seznam umístění, která podporují IoT Hub, najdete v tématu [stav Azure](https://azure.microsoft.com/status/):

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

4. Do metody **CreateIoTHub** přidejte následující kód. Tento kód odešle požadavek REST do Azure. Kód pak zkontroluje odpověď a načte adresu URL, kterou můžete použít k monitorování stavu úlohy nasazení:

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

5. Na konec metody **CreateIoTHub** přidejte následující kód. Tento kód používá **asyncStatusUri** adresu získanou v předchozím kroku k čekání na dokončení nasazení:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Na konec metody **CreateIoTHub** přidejte následující kód. Tento kód načte klíče centra IoT, které jste vytvořili, a vytiskne je do konzoly:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Dokončení a spuštění aplikace

Nyní můžete aplikaci dokončit voláním metody **CreateIoTHub** před sestavením a spuštěním.

1. Na konec metody **Main** přidejte následující kód:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klikněte na **sestavit** a pak na **Sestavit řešení**. Opravte všechny chyby.

3. Klikněte na **ladit** a potom **Spusťte ladění** , aby se aplikace spustila. Spuštění nasazení může trvat několik minut.

4. Pokud chcete ověřit, že vaše aplikace přidala nové centrum IoT, přejděte na [Azure Portal](https://portal.azure.com/) a zobrazte si seznam prostředků. Alternativně můžete použít rutinu PowerShellu **Get-AzResource** .

> [!NOTE]
> Tato ukázková aplikace přidá standardní IoT Hub S1, pro které se účtuje. Až budete hotovi, můžete Centrum IoT odstranit pomocí [Azure Portal](https://portal.azure.com/) nebo pomocí rutiny prostředí PowerShell **Remove-AzResource** .

## <a name="next-steps"></a>Další kroky

Nyní jste nasadili IoT Hub pomocí REST API poskytovatele prostředků, možná budete chtít prozkoumat další:

* Přečtěte si o možnostech [REST API IoT Hub poskytovatele prostředků](/rest/api/iothub/iothubresource).

* Přečtěte si [Azure Resource Manager přehled](../azure-resource-manager/management/overview.md) , kde najdete další informace o možnostech Azure Resource Manager.

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)