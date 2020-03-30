---
title: Vytvoření služby Azure IoT Hub pomocí šablony (.NET) | Dokumenty společnosti Microsoft
description: Jak použít šablonu Azure Resource Manager k vytvoření služby IoT Hub s programem C#.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976739"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Vytvoření centra IoT pomocí šablony Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager můžete použít k vytvoření a správě center Azure IoT programově. Tento kurz ukazuje, jak použít šablonu Azure Resource Manager k vytvoření centra IoT hub z programu C#.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasické](../azure-resource-manager/management/deployment-models.md).  Tento článek popisuje použití modelu nasazení Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio.
* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Účet Azure Storage,][lnk-storage-account] kde můžete ukládat soubory šablon Azure Resource Manager.
* [Azure PowerShell 1.0][lnk-powershell-install] nebo novější.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Příprava projektu sady Visual Studio

1. V sadě Visual Studio vytvořte projekt klasické plochy systému Visual C# windows classic pomocí šablony projektu **Console App (.NET Framework).** Název projektu **CreateIoTHub**.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a potom klikněte na **spravovat balíčky NuGet**.

3. Ve Správci balíčků NuGet **zaškrtněte políčko Zahrnout předběžnou verzi**a na stránce **Procházet** **microsoft.Azure.Management.ResourceManager**. Vyberte balíček, klikněte na **Instalovat**, v **části Revize změn** klikněte na **OK**a potom klikněte na **Souhlasím,** abyste licence přijali.

4. Ve Správci balíčků NuGet vyhledejte **soubor Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klepněte na **tlačítko Instalovat**, v **části Revize změn** klepněte na tlačítko **OK**a potom klepnutím na tlačítko **Souhlasím** licenci přijměte.

5. V Program.cs nahraďte existující **příkazy using** následujícím kódem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. V Program.cs přidejte následující statické proměnné nahrazující zástupné hodnoty. Jste si poznamenejte **ApplicationId**, **SubscriptionId**, **TenantId**a **heslo** dříve v tomto kurzu. **Název účtu Azure Storage** je název účtu Azure Storage, kde ukládáte soubory šablon Azure Resource Manager. **Název skupiny prostředků** je název skupiny prostředků, kterou používáte při vytváření centra IoT Hub. Název může být již existující nebo nová skupina prostředků. **Název nasazení** je název nasazení, například **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Odeslání šablony pro vytvoření centra IoT hub

Pomocí šablony JSON a souboru parametrů vytvořte centrum IoT hub ve skupině prostředků. K provádění změn v existujícím centru IoT hub uděláte taky šablonu Azure Resource Manageru.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt, klikněte na **Přidat**a potom klikněte na **Nová položka**. Přidejte do projektu soubor JSON s názvem **template.json.**

2. Chcete-li přidat standardní centrum IoT do oblasti **USA – východ,** nahraďte obsah **souboru template.json** následujícím definicí prostředků. Aktuální seznam oblastí, které podporují Službu IoT Hub, najdete [v tématu Stav Azure][lnk-status]:

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

3. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt, klikněte na **Přidat**a potom klikněte na **Nová položka**. Přidejte do projektu soubor JSON s názvem **parameters.json.**

4. Nahraďte obsah **parametru parameters.json** následujícími informacemi o parametrech, které nastaví název nového centra IoT hub, například **{your initials}mynewiothub**. Název centra IoT hub musí být globálně jedinečný, takže by měl obsahovat vaše jméno nebo iniciály:

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

5. V **Průzkumníkovi serveru**se připojte k předplatnému Azure a ve svém účtu Azure Storage vytvořte kontejner s názvem **šablony**. V panelu **Vlastnosti** nastavte **veřejná** oprávnění ke čtení pro kontejner **šablon** na **objekt Blob**.

6. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na kontejner **šablon** a potom klikněte na příkaz Zobrazit kontejner **objektů blob**. Klepněte na tlačítko **Nahrát objekt Blob,** vyberte dva soubory, **parameters.json** a **templates.json**a pak klepněte na **otevřít** a nahrajte soubory JSON do kontejneru **šablon.** Adresy URL objektů BLOB obsahující chodjsovná data jsou:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Do Program.cs přidejte následující metodu:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Přidejte následující kód do metody **CreateIoTHub** a odešlete soubory šablon a parametrů do Správce prostředků Azure:

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

9. Přidejte následující kód do metody **CreateIoTHub,** která zobrazuje stav a klíče pro nové centrum IoT:

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

Nyní můžete dokončit aplikaci voláním **CreateIoTHub** metoda před sestavením a spuštěním.

1. Na konec **metody Main** přidejte následující kód:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klepněte na **tlačítko Sestavit** a potom **na vytvořit řešení**. Opravte všechny chyby.

3. Klikněte na **Ladění** a **potom spusťte** ladění a spusťte aplikaci. Spuštění nasazení může trvat několik minut.

4. Pokud chcete ověřit, že vaše aplikace přidala nové centrum IoT, navštivte [portál Azure][lnk-azure-portal] a zobrazte seznam prostředků. Případně použijte rutinu **Get-AzResource** PowerShell.

> [!NOTE]
> Tato ukázková aplikace přidá sbíhá S1 Standard IoT Hub, za který se vám účtují. Centrum IoT můžete odstranit prostřednictvím [portálu Azure][lnk-azure-portal] nebo pomocí rutiny **Remove-AzResource** PowerShell po dokončení.

## <a name="next-steps"></a>Další kroky
Teď jste nasadili centrum IoT pomocí šablony Azure Resource Manager s programem C#, možná budete chtít prozkoumat další:

* Přečtěte si o možnostech [rozhraní REST API poskytovatele prostředků služby IoT Hub][lnk-rest-api].
* Další informace o možnostech Azure Resource Manageru najdete v [přehledu][lnk-azure-rm-overview] správce prostředků Azure.
* Syntaxe json a vlastnosti pro použití v šablonách naleznete v tématu [Microsoft.Devices typy prostředků](/azure/templates/microsoft.devices/iothub-allversions).

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Úvod do sady C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

Další informace o možnostech IoT Hubu najdete v následujících tématech:

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
