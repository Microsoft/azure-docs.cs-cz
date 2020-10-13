---
title: Vytvoření IoT Hub Azure pomocí šablony (.NET) | Microsoft Docs
description: Jak použít šablonu Azure Resource Manager k vytvoření IoT Hub pomocí programu v jazyce C#.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dcec1e40e9095c27abb1470e3739f65035a96834
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89007175"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Vytvoření služby IoT Hub pomocí šablony Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pomocí Azure Resource Manager můžete programově vytvářet a spravovat centra IoT Azure. V tomto kurzu se dozvíte, jak použít šablonu Azure Resource Manager k vytvoření centra IoT z programu v jazyce C#.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi:  [Azure Resource Manager a Classic](../azure-resource-manager/management/deployment-models.md).  Tento článek popisuje použití modelu nasazení Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto kurzu potřebujete:

* Visual Studio
* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Účet Azure Storage][lnk-storage-account] , do kterého můžete ukládat soubory šablon Azure Resource Manager.
* [Azure PowerShell 1,0][lnk-powershell-install] nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V aplikaci Visual Studio vytvořte klasický desktopový projekt Visual C# pro systém Windows pomocí šablony projektu **Konzolová aplikace (.NET Framework)** . Pojmenujte projekt **CreateIoTHub**.

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt a pak klikněte na **Spravovat balíčky NuGet**.

3. Ve Správci balíčků NuGet **Zahrňte zahrnutí předběžné verze**a na stránce **Procházet** vyhledejte **Microsoft. Azure. Management. ResourceManager**. Vyberte balíček, klikněte na **nainstalovat**. v části **Zkontrolovat změny** klikněte na **OK**a **pak kliknutím na Souhlasím** přijměte licence.

4. Ve Správci balíčků NuGet vyhledejte **Microsoft. IdentityModel. clients. Active**.  Klikněte na **nainstalovat**, v části **Zkontrolovat změny** klikněte na **OK**a pak **klikněte na Souhlasím, aby se** licence přijímala.

5. V Program.cs nahraďte existující příkazy **using** následujícím kódem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Do Program.cs přidejte následující statické proměnné nahrazující zástupné hodnoty. Dříve v tomto kurzu jste si poznamenali **ApplicationId**, **SubscriptionId**, **TenantId**a **Password** . **Název účtu Azure Storage** je název účtu Azure Storage, kam ukládáte soubory šablon Azure Resource Manager. **Název skupiny prostředků** je název skupiny prostředků, kterou použijete při vytváření centra IoT. Název může být již existující nebo nová skupina prostředků. **Název nasazení** je název pro nasazení, například **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Odeslání šablony pro vytvoření centra IoT

K vytvoření centra IoT ve vaší skupině prostředků použijte šablonu a soubor parametrů JSON. K provedení změn v existujícím centru IoT Hub můžete použít také šablonu Azure Resource Manager.

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt, klikněte na tlačítko **Přidat**a poté klikněte na položku **Nová položka**. Do projektu přidejte soubor JSON s názvem **template.js** .

2. Chcete-li do oblasti **východní USA** přidat standardní centrum IoT, nahraďte obsah **template.jsv** následující definici prostředků. Aktuální seznam oblastí, které podporují IoT Hub, najdete v tématu [stav Azure][lnk-status]:

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

3. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt, klikněte na tlačítko **Přidat**a poté klikněte na položku **Nová položka**. Do projektu přidejte soubor JSON s názvem **parameters.js** .

4. Nahraďte obsah **parameters.jsinformacemi o** následujících parametrech, které nastaví název nového centra IoT, jako je například **{vaše iniciály} mynewiothub**. Název centra IoT musí být globálně jedinečný, aby měl zahrnovat vaše jméno nebo iniciály:

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

5. V **Průzkumník serveru**se připojte k předplatnému Azure a v účtu Azure Storage vytvořte kontejner nazvaný **šablony**. Na panelu **vlastnosti** nastavte veřejné oprávnění ke **čtení** pro kontejner **šablony** na hodnotu **BLOB**.

6. V **Průzkumník serveru**klikněte pravým tlačítkem na kontejner **šablon** a pak klikněte na **Zobrazit kontejner objektů BLOB**. Klikněte na tlačítko **nahrát objekt BLOB** , vyberte dva soubory **parameters.jszapnuto** a **templates.jsna**a potom kliknutím na tlačítko **otevřít** nahrajte soubory JSON do kontejneru **šablony** . Adresy URL objektů BLOB obsahující data JSON jsou:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Přidejte následující metodu do Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Přidejte následující kód do metody **CreateIoTHub** pro odeslání šablony a souborů parametrů do Azure Resource Manager:

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

9. Do metody **CreateIoTHub** přidejte následující kód, který zobrazí stav a klíče pro nové centrum IoT:

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

Nyní můžete aplikaci dokončit voláním metody **CreateIoTHub** před sestavením a spuštěním.

1. Na konec metody **Main** přidejte následující kód:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klikněte na **sestavit** a pak na **Sestavit řešení**. Opravte všechny chyby.

3. Klikněte na **ladit** a potom **Spusťte ladění** , aby se aplikace spustila. Spuštění nasazení může trvat několik minut.

4. Pokud chcete ověřit, že vaše aplikace přidala nové centrum IoT, navštivte [Azure Portal][lnk-azure-portal] a zobrazte si seznam prostředků. Alternativně můžete použít rutinu PowerShellu **Get-AzResource** .

> [!NOTE]
> Tato ukázková aplikace přidá standardní IoT Hub S1, pro které se účtuje. Služba IoT Hub se dá odstranit pomocí [Azure Portal][lnk-azure-portal] nebo pomocí rutiny **Remove-AzResource** PowerShellu po dokončení.

## <a name="next-steps"></a>Další kroky
Nyní jste nasadili IoT Hub pomocí šablony Azure Resource Manager v programu v jazyce C#, možná budete chtít prozkoumat další:

* Přečtěte si o možnostech [REST API IoT Hub poskytovatele prostředků][lnk-rest-api].
* Přečtěte si [Azure Resource Manager přehled][lnk-azure-rm-overview] , kde najdete další informace o možnostech Azure Resource Manager.
* Informace o syntaxi a vlastnostech JSON pro použití v šablonách najdete v tématu [typy prostředků Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Seznámení se sadou C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
