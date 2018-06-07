---
title: Vytvoření služby Azure IoT Hub pomocí šablony (.NET) | Microsoft Docs
description: Postup vytvoření služby IoT Hub s programu v C# pomocí šablony Azure Resource Manager.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a64749b7218fccfdad6b6eeebfac39a44aa0522
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635538"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Vytvoření služby IoT hub pomocí šablony Azure Resource Manageru (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager můžete použít k vytváření a správě Azure IoT hubs prostřednictvím kódu programu. V tomto kurzu se dozvíte, jak k vytvoření služby IoT hub z programu v C# pomocí šablony Azure Resource Manager.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se zabývá pomocí modelu nasazení Azure Resource Manager.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Účet úložiště Azure] [ lnk-storage-account] kam můžete ukládat soubory šablony Azure Resource Manager.
* [Azure PowerShell 1.0] [ lnk-powershell-install] nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V sadě Visual Studio vytvořit Visual C# Windows klasický desktopový projekt pomocí **konzolovou aplikaci (rozhraní .NET Framework)** šablona projektu. Název projektu **CreateIoTHub**.

2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **spravovat balíčky NuGet**.

3. Zkontrolujte v Správce balíčků NuGet **zahrnout předběžné verze**a na **Procházet** stránky hledání **Microsoft.Azure.Management.ResourceManager**. Vyberte balíček, klikněte na tlačítko **nainstalovat**v **zkontrolujte změny** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** tak, aby přijímal licence.

4. Vyhledejte v Správce balíčků NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klikněte na tlačítko **nainstalovat**v **zkontrolujte změny** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** tak, aby přijímal licence.

5. V Program.cs místo existující **pomocí** příkazy následujícím kódem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. V souboru Program.cs přidejte následující proměnné na statické nahraďte zástupný symbol hodnoty. Učinit poznámku o **ApplicationId**, **SubscriptionId**, **TenantId**, a **heslo** výše v tomto kurzu. **Název účtu úložiště Azure** je název účtu úložiště Azure, kde můžete ukládat soubory šablony Azure Resource Manager. **Název skupiny prostředků** je název skupiny prostředků, můžete použít, když vytvoříte Centrum IoT. Název může být existující nebo nové skupiny prostředků. **Název nasazení** , jako je název pro nasazení, **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Odeslat šablonu pro vytvoření služby IoT hub

Použijte soubor JSON šablony a parametrů k vytvoření služby IoT hub ve vaší skupině prostředků. Šablonu Azure Resource Manager můžete také provést změny do stávající služby IoT hub.

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na tlačítko **přidat**a potom klikněte na **novou položku**. Přidejte soubor JSON s názvem **template.json** do projektu.

2. Chcete-li přidat standardní IoT hub, která **východní USA** oblast, nahraďte obsah **template.json** s následující definice prostředků. Aktuální seznam oblastí, které podporují služby IoT Hub naleznete v části [stavu Azure][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na tlačítko **přidat**a potom klikněte na **novou položku**. Přidejte soubor JSON s názvem **Parameters.JSON tímto kódem** do projektu.

4. Nahraďte obsah **Parameters.JSON tímto kódem** s následujícími informacemi parametr, který nastaví název nového centra IoT, jako **{vašimi iniciálami} mynewiothub**. Název centra IoT musí být globálně jedinečný, aby měl by obsahovat název nebo initials:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. V **Průzkumníka serveru**, připojení k předplatnému Azure a v účtu úložiště Azure vytvořit kontejner s názvem **šablony**. V **vlastnosti** panelu, nastavte **veřejný přístup pro čtení** oprávnění pro **šablony** kontejner, aby **Blob**.

6. V **Průzkumníka serveru**, klikněte pravým tlačítkem na **šablony** kontejneru a pak klikněte na tlačítko **kontejner objektů Blob zobrazení**. Klikněte na tlačítko **nahrát objekt Blob** tlačítko, vyberte dva soubory **Parameters.JSON tímto kódem** a **templates.json**a potom klikněte na **otevřete** nahrát JSON souborů do **šablony** kontejneru. Adresy URL obsahující JSON data objekty BLOB jsou:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Do souboru Program.cs přidejte následující metodu:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Přidejte následující kód, který **CreateIoTHub** metoda odeslat soubory šablony a parametrů do Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Přidejte následující kód, který **CreateIoTHub** metoda, která zobrazuje stav a klíče pro nového centra IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Dokončení a spuštění aplikace

Aplikace je nyní možné dokončit voláním **CreateIoTHub** metoda předtím, než můžete sestavit a spustit ho.

1. Přidejte následující kód do konce **hlavní** metoda:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klikněte na tlačítko **sestavení** a potom **sestavení řešení**. Opravte všechny chyby.

3. Klikněte na tlačítko **ladění** a potom **spustit ladění** ke spuštění aplikace. Ho může trvat několik minut, než nasazení ke spuštění.

4. Chcete-li ověřit, vaše aplikace přidat nového centra IoT, navštivte [portál Azure] [ lnk-azure-portal] a zobrazení seznamu prostředků. Můžete taky použít **Get-AzureRmResource** rutiny prostředí PowerShell.

> [!NOTE]
> Tato ukázková aplikace přidá S1 Standard IoT Hub pro kterou se účtují. Odstraněním služby IoT hub prostřednictvím [portál Azure] [ lnk-azure-portal] nebo pomocí **AzureRmResource odebrat** rutiny prostředí PowerShell po dokončení.

## <a name="next-steps"></a>Další postup
Nyní jste nasadili služby IoT hub pomocí šablony Azure Resource Manageru pomocí programu v C#, můžete chtít Další:

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
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
