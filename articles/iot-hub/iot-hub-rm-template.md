---
title: Vytvoření centra IoT Azure pomocí šablony (.NET) | Dokumentace Microsoftu
description: Jak používat šablony Azure Resource Manageru k vytvoření služby IoT Hub pomocí programu v jazyce C#.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a64749b7218fccfdad6b6eeebfac39a44aa0522
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687767"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Vytvoření IoT hubu pomocí šablony Azure Resource Manageru (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manageru můžete použít k vytváření a správě služby Azure IoT hubs prostřednictvím kódu programu. V tomto kurzu se dozvíte, jak pomocí šablony Azure Resource Manageru k vytvoření služby IoT hub z programu v jazyce C#.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manageru a Klasický model](../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje modelu nasazení Azure Resource Manageru.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017.
* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Účtu služby Azure Storage] [ lnk-storage-account] kam můžete ukládat soubory šablony Azure Resource Manageru.
* [Azure PowerShell 1.0] [ lnk-powershell-install] nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V sadě Visual Studio, vytvořit pomocí Visual C# Windows klasický desktopový projekt **Konzolová aplikace (.NET Framework)** šablony projektu. Pojmenujte projekt **CreateIoTHub**.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a potom klikněte na tlačítko **spravovat balíčky NuGet**.

3. Ve správci balíčků NuGet zkontrolujte **zahrnout předběžné verze**a na **Procházet** stránkové vyhledávání pro **Microsoft.Azure.Management.ResourceManager**. Vyberte balíček, klikněte na tlačítko **nainstalovat**v **změny vyplývající z revize** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** tak, aby přijímal licence.

4. Vyhledejte ve správci balíčků NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klikněte na tlačítko **nainstalovat**v **změny vyplývající z revize** klikněte na tlačítko **OK**, pak klikněte na tlačítko **souhlasím** přijměte licenci.

5. V souboru Program.cs nahraďte existující **pomocí** příkazy následujícím kódem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. V souboru Program.cs přidejte následující proměnné na statické nahraďte zástupné hodnoty. Jste si poznamenali **ApplicationId**, **SubscriptionId**, **TenantId**, a **heslo** výše v tomto kurzu. **Název účtu služby Azure Storage** je název účtu služby Azure Storage, kam ukládat vaše soubory šablon Azure Resource Manageru. **Název skupiny prostředků** je název skupiny prostředků, použijete při vytváření služby IoT hub. Název může obsahovat již existující nebo nové skupiny prostředků. **Název nasazení** , jako je název pro nasazení, **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Odeslání šablony k vytvoření služby IoT hub

Použijte šablonu a parametry soubor JSON pro vytvoření služby IoT hub ve vaší skupině prostředků. Můžete také použít šablony Azure Resource Manageru provádět změny existující služby IoT hub.

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na tlačítko **přidat**a potom klikněte na tlačítko **nová položka**. Přidat soubor JSON s názvem **template.json** do projektu.

2. Chcete-li přidat standard služby IoT hub do **USA – východ** oblast, nahraďte obsah **template.json** s následující definicí prostředku. Aktuální seznam oblastí, které podporují služby IoT Hub najdete v části [stav Azure][lnk-status]:

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

3. V Průzkumníku řešení klikněte pravým tlačítkem na projekt, klikněte na tlačítko **přidat**a potom klikněte na tlačítko **nová položka**. Přidat soubor JSON s názvem **parameters.json** do projektu.

4. Nahraďte obsah **parameters.json** s následujícími informacemi parametr, který nastaví název pro novou službu IoT hub jako **{vašimi iniciálami} mynewiothub**. Takže by měl obsahovat název nebo iniciály, musí být globálně jedinečný název centra IoT:

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

5. V **Průzkumníka serveru**, připojte se ke svému předplatnému Azure a ve vašem účtu Azure Storage vytvořte kontejner volá **šablony**. V **vlastnosti** panelu, nastavte **veřejné oprávnění ke čtení** oprávnění pro **šablony** kontejneru **Blob**.

6. V **Průzkumníka serveru**, klikněte pravým tlačítkem na **šablony** kontejner a pak klikněte na tlačítko **kontejner objektů Blob zobrazení**. Klikněte na tlačítko **nahrát objekt Blob** tlačítko, vyberte dva soubory **parameters.json** a **templates.json**a potom klikněte na tlačítko **otevřít** nahrát Soubory JSON **šablony** kontejneru. Jsou adresy URL objektů BLOB, který obsahuje JSON data:

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

8. Přidejte následující kód, který **CreateIoTHub** metody k odeslání souborů do Azure Resource Manageru šablonu a parametry:

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

9. Přidejte následující kód, který **CreateIoTHub** metodu, která zobrazuje stav a klíče pro novou službu IoT hub:

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

Aplikace je nyní možné dokončit voláním **CreateIoTHub** metoda před sestavit a spustit ho.

1. Přidejte následující kód do konce **hlavní** metody:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klikněte na tlačítko **sestavení** a potom **sestavit řešení**. Opravte všechny chyby.

3. Klikněte na tlačítko **ladění** a potom **spustit ladění** ke spuštění aplikace. Může trvat několik minut, než se nasazení pro spuštění.

4. K ověření vaší aplikace přidat novou službu IoT hub, najdete [webu Azure portal] [ lnk-azure-portal] a zobrazit seznam prostředků. Můžete taky použít **Get-AzureRmResource** rutiny Powershellu.

> [!NOTE]
> Tato ukázková aplikace přidá S1 Standard služby IoT Hub pro kterou se vám účtuje. Můžete odstranit centrum IoT prostřednictvím [webu Azure portal] [ lnk-azure-portal] nebo s použitím **Remove-AzureRmResource** rutiny Powershellu, až budete hotovi.

## <a name="next-steps"></a>Další postup
Nyní jste nasadili IoT hubu pomocí šablony Azure Resource Manageru pomocí programu v jazyce C#, můžete dále zkoumat:

* Přečtěte si o možnostech [rozhraní REST API poskytovatele prostředků služby IoT Hub][lnk-rest-api].
* Čtení [přehled Azure Resource Manageru] [ lnk-azure-rm-overview] Další informace o možnostech Azure Resource Manageru.

Další informace o vývoji pro službu IoT Hub, najdete v následujících článcích:

* [Seznámení s C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

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
