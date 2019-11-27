---
title: Připojení svazku souborů Azure ke skupině kontejnerů
description: Naučte se, jak připojit svazek souborů Azure k trvalému stavu pomocí Azure Container Instances
ms.topic: article
ms.date: 07/08/2019
ms.custom: mvc
ms.openlocfilehash: a258a96f5fbc0d54b6a85a780288fb9317cb1a1b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533248"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Připojení sdílené složky Azure v Azure Container Instances

Ve výchozím nastavení jsou Azure Container Instances Bezstavová. Pokud dojde k chybě nebo zastavení kontejneru, veškerý jeho stav bude ztracen. Chcete-li zachovat stav po dobu životnosti kontejneru, je nutné připojit svazek z externího úložiště. Jak je znázorněno v tomto článku, Azure Container Instances můžou připojit sdílenou složku Azure vytvořenou se [soubory Azure](../storage/files/storage-files-introduction.md). Azure Files nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní protokol Server Message Block (SMB). Použití sdílené složky Azure s Azure Container Instances poskytuje funkce pro sdílení souborů podobně jako použití sdílené složky Azure s virtuálními počítači Azure.

> [!NOTE]
> Připojení sdílené složky služby soubory Azure je aktuálně omezené na kontejnery Linux. V [přehledu](container-instances-overview.md#linux-and-windows-containers)najdete aktuální rozdíly v platformách.
>
> Připojení sdílené složky služby soubory Azure do instance kontejneru je podobné jako připojení k Docker [BIND](https://docs.docker.com/storage/bind-mounts/). Uvědomte si, že pokud připojíte sdílenou složku k adresáři kontejneru, ve kterém existují soubory nebo adresáře, tyto soubory nebo adresáře jsou po připojení skryté a nejsou přístupné při spuštění kontejneru.
>

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Předtím, než použijete sdílenou složku Azure s Azure Container Instances, je nutné ji vytvořit. Spuštěním následujícího skriptu vytvořte účet úložiště pro hostování sdílené složky a samotné sdílené složky. Název účtu úložiště musí být globálně jedinečný, takže skript přidá náhodnou hodnotu do základního řetězce.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Získat přihlašovací údaje úložiště

Pokud chcete připojit sdílenou složku Azure jako svazek v Azure Container Instances, budete potřebovat tři hodnoty: název účtu úložiště, název sdílené složky a přístupový klíč úložiště.

Pokud jste použili skript výše, název účtu úložiště byl uložený v proměnné $ACI _PERS_STORAGE_ACCOUNT_NAME. Název účtu zobrazíte tak, že zadáte:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Název sdílené složky je již známý (definován jako *acishare* ve skriptu výše), takže vše zůstane klíčem účtu úložiště, který lze najít pomocí následujícího příkazu:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Nasazení kontejneru a přípojného svazku – rozhraní příkazového řádku

Pokud chcete připojit sdílenou složku Azure jako svazek v kontejneru pomocí rozhraní příkazového řádku Azure, určete sdílenou složku a přípojný bod svazku při vytváření kontejneru pomocí [AZ Container Create][az-container-create]. Pokud jste postupovali podle předchozích kroků, můžete sdílenou složku, kterou jste vytvořili dříve, připojit pomocí následujícího příkazu k vytvoření kontejneru:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytvoříte instanci kontejneru. Pokud se při spuštění příkazu zobrazí chybová zpráva **popisku názvu DNS** , aktualizujte hodnotu v předchozím příkazu.

## <a name="manage-files-in-mounted-volume"></a>Správa souborů na připojeném svazku

Po spuštění kontejneru můžete použít jednoduchou webovou aplikaci nasazenou prostřednictvím image Microsoft [ACI-hellofiles][aci-hellofiles] k vytvoření malých textových souborů ve sdílené složce Azure v zadané cestě k připojení. Získejte plně kvalifikovaný název domény (FQDN) webové aplikace pomocí příkazu [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Po uložení textu pomocí aplikace můžete použít [Azure Portal][portal] nebo nástroj jako [Průzkumník služby Microsoft Azure Storage][storage-explorer] k načtení a zkontrolování souboru zapsaného do sdílené složky.

## <a name="deploy-container-and-mount-volume---yaml"></a>Nasazení kontejneru a připojení svazku – YAML

Můžete také nasadit skupinu kontejnerů a připojit svazek do kontejneru pomocí Azure CLI a [šablony YAML](container-instances-multi-container-yaml.md). Nasazení šablonou YAML je upřednostňovanou metodou při nasazování skupin kontejnerů, které se skládají z více kontejnerů.

Následující šablona YAML definuje skupinu kontejnerů s jedním kontejnerem vytvořeným pomocí bitové kopie `aci-hellofiles`. Kontejner připojí sdílenou složku Azure *acishare* , která byla dříve vytvořena jako svazek. Tam, kde je uvedeno, zadejte název a klíč úložiště pro účet úložiště, který hostuje sdílenou složku. 

Stejně jako v příkladu rozhraní příkazového řádku musí být hodnota `dnsNameLabel` jedinečná v rámci oblasti Azure, kde vytváříte instanci kontejneru. V případě potřeby aktualizujte hodnotu v souboru YAML.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

K nasazení se šablonou YAML uložte předchozí YAML do souboru s názvem `deploy-aci.yaml`a potom spusťte příkaz [AZ Container Create][az-container-create] s parametrem `--file`:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Nasazení kontejneru a připojení svazku – Správce prostředků

Kromě nasazení CLI a YAML můžete nasadit skupinu kontejnerů a připojit svazek do kontejneru pomocí [šablony Azure správce prostředků](/azure/templates/microsoft.containerinstance/containergroups).

Nejdřív naplňte pole `volumes` do části `properties` skupiny kontejnerů v šabloně. 

Pak u každého kontejneru, do kterého chcete svazek připojit, naplňte pole `volumeMounts` v části `properties` definice kontejneru.

Následující šablona Správce prostředků definuje skupinu kontejnerů s jedním kontejnerem vytvořeným pomocí `aci-hellofiles` image. Kontejner připojí sdílenou složku Azure *acishare* , která byla dříve vytvořena jako svazek. Tam, kde je uvedeno, zadejte název a klíč úložiště pro účet úložiště, který hostuje sdílenou složku. 

Stejně jako v předchozích příkladech musí být hodnota `dnsNameLabel` jedinečná v rámci oblasti Azure, ve které vytvoříte instanci kontejneru. V případě potřeby aktualizujte hodnotu v šabloně.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Pokud chcete nasadit šablonu Správce prostředků, uložte předchozí JSON do souboru s názvem `deploy-aci.json`a pak spusťte příkaz [AZ Group Deployment Create][az-group-deployment-create] s parametrem `--template-file`:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Připojit více svazků

Chcete-li připojit více svazků v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) nebo souboru YAML. Chcete-li použít šablonu nebo soubor YAML, zadejte podrobnosti o sdílené složce a definujte svazky naplněním pole `volumes` v části `properties` šablony. 

Pokud jste například vytvořili dvě sdílené složky Azure Files s názvem *share1* a *share2* v účtu úložiště *myStorageAccount*, `volumes` pole v šabloně správce prostředků by vypadalo podobně jako v následujícím příkladu:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Potom pro každý kontejner ve skupině kontejnerů, do kterého chcete svazky připojit, naplňte pole `volumeMounts` v části `properties` definice kontejneru. Například to připojí dva svazky, *myvolume1* a *myvolume2*, dříve definované:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Další kroky

Naučte se připojit další typy svazků v Azure Container Instances:

* [Připojení svazku emptyDir v Azure Container Instances](container-instances-volume-emptydir.md)
* [Připojení svazku Gitrepo nepodporují v Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojit tajný svazek v Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create