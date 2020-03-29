---
title: Použití svazku založeného na souborech Azure v aplikaci Service Fabric Mesh
description: Zjistěte, jak ukládat stav v aplikaci Azure Service Fabric Mesh připojením svazku založeného na Azure Files uvnitř služby pomocí azure cli.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718816"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Připojení svazku založeného na souborech Azure v aplikaci Service Fabric Mesh 

Tento článek popisuje, jak připojit svazek založený na souborech Azure ve službě aplikace Service Fabric Mesh.  Ovladač svazku Soubory Azure je ovladač svazku Dockeru, který se používá k připojení sdílené složky Souborů Azure do kontejneru, který používáte k zachování stavu služby. Svazky umožňují ukládání souborů pro obecné účely a umožňují čtení a zápis souborů pomocí běžných souborů V/O disku.  Další informace o svazcích a možnostech ukládání dat aplikací najdete ve [stavu ukládání](service-fabric-mesh-storing-state.md).

Chcete-li připojit svazek ve službě, vytvořte prostředek svazku v aplikaci Service Fabric Mesh a potom na tento svazek ve službě.  Deklarování prostředku svazku a jeho odkazování v prostředku služby lze provést buď v [souborech prostředků založených na YAML,](#declare-a-volume-resource-and-update-the-service-resource-yaml) nebo v [šabloně nasazení založené na JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Před připojením svazku nejprve vytvořte účet úložiště Azure a [sdílenou složku v Azure Files](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Požadavky
> [!NOTE]
> **Známý problém s nasazením ve vývojovém počítači se systémem Windows RS5:** Na počítačích s RS5 Windows je otevřená chyba s rutinou Powershellu New-SmbGlobalMapping, která brání připojení svazků souborů AzureFile. Níže je ukázková chyba, ke které dochází při připojení svazku založeného na AzureFile v místním vývojovém počítači.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Řešení problému je 1) Spustit pod příkazem jako správce prostředí Powershell a 2) Restartujte počítač.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

K dokončení tohoto článku můžete použít Azure Cloud Shell nebo místní instalaci vázací mise Azure. 

Chcete-li použít Azure CLI místně s `az --version` tímto `azure-cli (2.0.43)`článkem, ujistěte se, že vrátí alespoň .  Nainstalujte (nebo aktualizujte) rozšiřující modul rozšíření Azure Service Fabric Mesh CLI podle následujících [pokynů](service-fabric-mesh-howto-setup-cli.md).

Přihlášení k Azure a nastavení předplatného:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Vytvoření účtu úložiště a sdílené složky (volitelné)
Připojení svazku souborů Azure vyžaduje účet úložiště a sdílenou složku.  Můžete použít existující účet úložiště Azure a sdílenou složku nebo vytvořit prostředky:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Získání názvu a klíče účtu úložiště a názvu sdílené složky
Název účtu úložiště, klíč účtu úložiště a název sdílené `<storageAccountName>` `<storageAccountKey>`složky `<fileShareName>` jsou odkazovány jako , a v následujících částech. 

Uveďte své účty úložiště a získejte název účtu úložiště pomocí sdílené složky, kterou chcete použít:
```azurecli-interactive
az storage account list
```

Získejte název sdílené složky:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Získejte klíč účtu úložiště ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Tyto hodnoty najdete taky na [webu Azure Portal](https://portal.azure.com):
* `<storageAccountName>`- V části **Účty úložiště**se název účtu úložiště, který slouží k vytvoření sdílené složky.
* `<storageAccountKey>`- Vyberte svůj účet úložiště v části **Účty úložiště** a pak vyberte **přístupové klávesy** a použijte hodnotu pod **key1**.
* `<fileShareName>`- Vyberte svůj účet úložiště v části **Účty úložiště** a pak vyberte **Soubory**. Název, který chcete použít, je název sdílené složky, kterou jste vytvořili.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarovat prostředek svazku a aktualizovat prostředek služby (JSON)

Přidejte parametry `<fileShareName>` `<storageAccountName>`pro `<storageAccountKey>` , a hodnoty, které jste našli v předchozím kroku. 

Vytvořte prostředek svazku jako partnera prostředku aplikace. Zadejte název a zprostředkovatele ("SFAzureFile" pro použití svazku založeného na souborech Azure). V `azureFileParameters`oblasti zadejte parametry `<storageAccountName>`pro `<storageAccountKey>` `<fileShareName>`, a hodnoty, které jste našli v předchozím kroku.

Chcete-li připojit svazek ve `volumeRefs` službě, přidejte a k `codePackages` prvku služby.  `name`je ID prostředku pro svazek (nebo parametr šablony nasazení pro prostředek svazku) a název svazku deklarovaného v souboru prostředků volume.yaml.  `destinationPath`je místní adresář, ke kterému bude svazek připojen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarovat prostředek svazku a aktualizovat prostředek služby (YAML)

Přidejte nový soubor *volume.yaml* do *adresáře prostředků aplikace* pro vaši aplikaci.  Zadejte název a zprostředkovatele ("SFAzureFile" pro použití svazku založeného na souborech Azure). `<fileShareName>`a `<storageAccountName>` `<storageAccountKey>` jsou hodnoty nalezené v předchozím kroku.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Aktualizujte soubor *service.yaml* v adresáři *Zdroje služby a* připojte svazek do služby.  Přidejte `volumeRefs` prvek `codePackages` do prvku.  `name`je ID prostředku pro svazek (nebo parametr šablony nasazení pro prostředek svazku) a název svazku deklarovaného v souboru prostředků volume.yaml.  `destinationPath`je místní adresář, ke kterému bude svazek připojen.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Další kroky

- Zobrazení ukázkové aplikace svazku souborů Azure na [GitHubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
