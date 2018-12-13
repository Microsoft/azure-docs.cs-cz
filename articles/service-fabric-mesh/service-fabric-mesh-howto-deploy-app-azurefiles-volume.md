---
title: Použít svazku soubory Azure na základě aplikace Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak ukládat stavu v aplikaci Azure Service Fabric sítě tak, že připojení svazku soubory Azure na základě uvnitř služby pomocí rozhraní příkazového řádku Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9bce2d0e6d01813fd376b2505838defc9c772d70
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891091"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Připojit soubory Azure na základě svazek v aplikaci Service Fabric mřížky 

Tento článek popisuje, jak připojit soubory Azure na základě svazek ve službě aplikace Service Fabric mřížky.  Ovladač soubory Azure svazek je svazek ovladač Dockeru pro připojení sdílené složky služby soubory Azure do kontejneru, který použijete k uchování stavu služby. Svazky poskytují úložiště pro obecné účely souborů a umožňují čtení a zápis souborů pomocí rozhraní API normální disku vstupně-výstupní operace souboru.  Další informace o svazky a možnosti pro ukládání dat aplikací, přečtěte si téma [ukládání stavu](service-fabric-mesh-storing-state.md).

Připojit svazek ve službě, vytvoření prostředku svazku ve vaší aplikaci Service Fabric mřížky a odkázat na tomto svazku ve své službě.  Deklarování prostředku svazku a odkazování na ně v prostředku service můžete provést buď [soubory prostředků na základě YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) nebo [šablony nasazení na základě JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Před připojením svazku, nejprve vytvořte účet úložiště Azure a [sdílené složky ve službě soubory Azure](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. 

Pokud chcete použít rozhraní příkazového řádku Azure místně v tomto článku, ujistěte se, že `az --version` vrátí alespoň `azure-cli (2.0.43)`.  Nainstalovat (nebo aktualizovat) rozšiřující modul Azure mřížky příkazového řádku Service Fabric pomocí těchto [pokyny](service-fabric-mesh-howto-setup-cli.md).

Přihlaste se k Azure a nastavení předplatného:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Vytvoření úložiště souborů a sdílenou složku (volitelné)
Připojení svazku služby soubory Azure vyžaduje úložiště souborů a sdílenou složku.  Můžete použít existující služby Azure storage souborů a sdílenou složku nebo vytvořit prostředky:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Získání názvu účtu úložiště a klíč a název sdílené složky souborů
Název účtu úložiště a klíč účtu úložiště, název sdílené složky souborů jsou odkazovány jako `<storageAccountName>`, `<storageAccountKey>`, a `<fileShareName>` v následujících částech. 

Seznam účtů úložiště a název účtu úložiště pomocí sdílené složky, že kterou chcete použít:
```azurecli-interactive
az storage account list
```

Získání názvu sdílené složky:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Získání klíče účtu úložiště ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Můžete také najít tyto hodnoty [webu Azure portal](https://portal.azure.com):
* `<storageAccountName>` – V části **účty úložiště**, název účtu úložiště použité k vytvoření sdílené složky.
* `<storageAccountKey>` -Výběr účtu úložiště v rámci **účty úložiště** a pak vyberte **přístupové klíče** a použijte hodnoty v rámci **key1**.
* `<fileShareName>` -Výběr účtu úložiště v rámci **účty úložiště** a pak vyberte **soubory**. Název, který má používat je název sdílené složky, kterou jste vytvořili.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarace prostředku svazku a aktualizace prostředku služby (JSON)

Přidat parametry `<fileShareName>`, `<storageAccountName>`, a `<storageAccountKey>` hodnoty, které jste našli v předchozím kroku. 

Vytvoření prostředku svazku jako partnerské aplikace prostředku. Zadejte název a zprostředkovatele ("SFAzureFile" používat soubory Azure na základě svazku). V `azureFileParameters`, zadejte parametry pro `<fileShareName>`, `<storageAccountName>`, a `<storageAccountKey>` hodnoty, které jste našli v předchozím kroku.

K připojení svazku v rámci služby, přidejte `volumeRefs` k `codePackages` element služby.  `name` název svazku deklarované v souboru prostředků volume.yaml je ID prostředku pro svazek (nebo parametr šablony nasazení prostředku svazku).  `destinationPath` je místní adresář, který svazek bude připojen k.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
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
        "description": "Folder in which to store the state. Provide a empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
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

Přidat nový *volume.yaml* soubor *prostředků aplikace* adresář pro vaši aplikaci.  Zadejte název a zprostředkovatele ("SFAzureFile" používat soubory Azure na základě svazku). `<fileShareName>`, `<storageAccountName>`, a `<storageAccountKey>` jsou na hodnoty zjištěné v předchozím kroku.

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

Aktualizace *service.yaml* ve *prostředky služeb* directory připojit svazek ve své službě.  Přidat `volumeRefs` elementu `codePackages` elementu.  `name` název svazku deklarované v souboru prostředků volume.yaml je ID prostředku pro svazek (nebo parametr šablony nasazení prostředku svazku).  `destinationPath` je místní adresář, který svazek bude připojen k.

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

## <a name="next-steps"></a>Další postup

- Zobrazit soubory Azure svazku ukázkovou aplikaci na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Další informace o modelu prostředků Service Fabric najdete v článku o [modelu prostředků Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).
