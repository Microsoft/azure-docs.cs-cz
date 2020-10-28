---
title: Použití svazku založeného na souborech Azure v aplikaci Service Fabricové sítě
description: Naučte se ukládat stav do aplikace sítě Azure Service Fabric pomocí rozhraní příkazového řádku Azure CLI připojením svazku založeného na službě Azure Files v rámci služby.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 00addbe992bc113c67df08f73414107d51b3bce5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748200"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Připojení svazku založeného na souborech Azure v aplikaci Service Fabric sítě 

Tento článek popisuje, jak připojit svazek založený na souborech Azure ve službě Service Fabric aplikace sítě.  Ovladač svazku souborů Azure je ovladač svazku Docker, který se používá k připojení sdílené složky Azure Files do kontejneru, který používáte k trvalému stavu služby. Svazky poskytují úložiště souborů pro obecné účely a umožňují čtení a zápis souborů pomocí normálních rozhraní API v/v souborů na disku.  Pokud se chcete dozvědět víc o svazcích a možnostech pro ukládání dat aplikací, přečtěte si téma [uložení stavu](service-fabric-mesh-storing-state.md).

Pokud chcete připojit svazek ve službě, vytvořte v aplikaci Service Fabric sítě prostředek svazku a pak na tento svazek odkázat v rámci služby.  Deklarace prostředku svazku a odkazování na něj v prostředku služby se dá provést buď v [souborech prostředků založených na YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) , nebo v [šabloně nasazení založené na JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Před připojením svazku nejdřív vytvořte účet úložiště Azure a [sdílenou složku ve službě soubory Azure](../storage/files/storage-how-to-create-file-share.md).

## <a name="prerequisites"></a>Předpoklady
> [!NOTE]
> **Známý problém s nasazením na vývojovém počítači s Windows RS5:** K dispozici je otevřená chyba rutiny PowerShellu New-SmbGlobalMapping na počítačích s RS5 Windows, které brání připojení svazků Azurefile. Níže je uvedená ukázková chyba, ke které došlo při připojení svazku založeného na AzureFile do místního vývojového počítače.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Alternativní řešení problému je 1) spustit pod příkazem jako správce PowerShellu a 2) restartovat počítač.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

K dokončení tohoto článku můžete použít Azure Cloud Shell nebo místní instalaci rozhraní příkazového řádku Azure. 

Pokud chcete používat rozhraní příkazového řádku Azure v místním prostředí s tímto článkem, ujistěte se, že se `az --version` vrátí aspoň `azure-cli (2.0.43)` .  Pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md)nainstalujte (nebo aktualizujte) modul rozšíření CLI pro Azure Service Fabric.

Přihlaste se k Azure a nastavte své předplatné:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Vytvoření účtu úložiště a sdílené složky (volitelné)
Připojení svazku souborů Azure vyžaduje účet úložiště a sdílení souborů.  Můžete použít existující účet úložiště Azure a sdílenou složku nebo vytvořit prostředky:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Získání názvu a klíče účtu úložiště a názvu sdílené složky
Název účtu úložiště, klíč účtu úložiště a název sdílené složky jsou odkazovány jako `<storageAccountName>` , `<storageAccountKey>` a `<fileShareName>` v následujících oddílech. 

Zobrazte seznam účtů úložiště a získejte název účtu úložiště se sdílenou složkou, kterou chcete použít:
```azurecli-interactive
az storage account list
```

Získání názvu sdílené složky:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Získání klíče účtu úložiště (klíč1):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Tyto hodnoty můžete najít také v [Azure Portal](https://portal.azure.com):
* `<storageAccountName>` – V části **účty úložiště** se jedná o název účtu úložiště použitého k vytvoření sdílené složky.
* `<storageAccountKey>` – Vyberte svůj účet úložiště v části **účty úložiště** a pak vyberte **přístupové klíče** a použijte hodnotu pod **klíč1** .
* `<fileShareName>` – V části  **účty úložiště** vyberte svůj účet úložiště a pak vyberte **soubory** . Název, který se má použít, je název sdílené složky, kterou jste vytvořili.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarace prostředku svazku a aktualizace prostředku služby (JSON)

Přidejte parametry pro `<fileShareName>` hodnoty, `<storageAccountName>` a, `<storageAccountKey>` které jste našli v předchozím kroku. 

Vytvořte prostředek svazku jako partnerský uzel prostředku aplikace. Zadejte název a poskytovatele ("SFAzureFile" pro použití svazku založeného na službě Azure Files). V `azureFileParameters` Zadejte parametry pro `<fileShareName>` `<storageAccountName>` hodnoty, a, `<storageAccountKey>` které jste našli v předchozím kroku.

Pokud chcete připojit svazek ve službě, přidejte `volumeRefs` do `codePackages` elementu služby.  `name` je ID prostředku pro svazek (nebo parametr šablony nasazení pro prostředek svazku) a název svazku deklarovaného v souboru prostředků Volume. yaml.  `destinationPath` je místní adresář, ke kterému bude svazek připojen.

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

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarace prostředku svazku a aktualizace prostředku služby (YAML)

Přidejte nový soubor *Volume. yaml* do adresáře *prostředků aplikace* pro vaši aplikaci.  Zadejte název a poskytovatele ("SFAzureFile" pro použití svazku založeného na službě Azure Files). `<fileShareName>`, `<storageAccountName>` a `<storageAccountKey>` jsou hodnoty, které jste našli v předchozím kroku.

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

Pokud chcete připojit svazek ve službě, aktualizujte soubor *Service. yaml* v adresáři *prostředků služby* .  Přidejte `volumeRefs` element do `codePackages` elementu.  `name` je ID prostředku pro svazek (nebo parametr šablony nasazení pro prostředek svazku) a název svazku deklarovaného v souboru prostředků Volume. yaml.  `destinationPath` je místní adresář, ke kterému bude svazek připojen.

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

- Podívejte se na ukázkovou aplikaci svazku soubory Azure na [GitHubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
