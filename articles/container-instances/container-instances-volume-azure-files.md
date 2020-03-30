---
title: Připojení svazku Souborů Azure do skupiny kontejnerů
description: Zjistěte, jak připojit svazek Souborů Azure, aby se udál stav s instancemi kontejneru Azure
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561333"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Připojení sdílené složky ve službě Azure Container Instances

Ve výchozím nastavení je služba Azure Container Instances bezstavová. Pokud u kontejneru dojde k chybě nebo se kontejner zastaví, veškerý jeho stav se ztratí. Pokud chcete zachovat stav i po skončení doby života kontejneru, je nutné připojit svazek z externího úložiště. Jak je znázorněno v tomto článku, Instance kontejnerů Azure můžete připojit sdílené složky Azure vytvořené pomocí [souborů Azure](../storage/files/storage-files-introduction.md). Azure Files nabízí plně spravované sdílené složky hostované ve službě Azure Storage, které jsou přístupné prostřednictvím standardního protokolu Server Message Block (SMB). Použití sdílené složky Azure s instancemi kontejnerů Azure poskytuje funkce pro sdílení souborů podobné použití sdílené složky Azure s virtuálními počítači Azure.

> [!NOTE]
> Připojení sdílené složky Azure Files je aktuálně omezeno na kontejnery Linuxu. Aktuální rozdíly v platformě naleznete v [přehledu](container-instances-overview.md#linux-and-windows-containers).
>
> Připojení sdílené složky Azure Files do instance kontejneru je podobné [připojení vazby](https://docs.docker.com/storage/bind-mounts/)Dockeru . Uvědomte si, že pokud připojíte sdílenou složku do adresáře kontejneru, ve kterém existují soubory nebo adresáře, tyto soubory nebo adresáře jsou zakryty připojením a nejsou přístupné při spuštění kontejneru.
>

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Abyste mohli ve službě Azure Container Instances použít sdílenou složku Azure, je potřeba ji napřed vytvořit. Spusťte následující skript a vytvořte účet úložiště pro hostování sdílené složky a samotné sdílené složky. Název účtu úložiště musí být globálně jedinečný, a proto skript přidá k základnímu řetězci náhodnou hodnotu.

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Získání přihlašovacích údajů úložiště

K připojení sdílené složky Azure jako svazku ve službě Azure Container Instances potřebujete tři hodnoty: název účtu úložiště, název sdílené složky a přístupový klíč k úložišti.

* **Název účtu úložiště** – Pokud jste použili předchozí skript, `$ACI_PERS_STORAGE_ACCOUNT_NAME` byl název účtu úložiště uložen v proměnné. Chcete-li zobrazit název účtu, zadejte:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Název sdílené položky** – Tato `acishare` hodnota je již známa (definována jako v předchozím skriptu)

* **Klíč účtu úložiště** – tuto hodnotu lze nalézt pomocí následujícího příkazu:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Nasazení kontejneru a připojení svazku – CLI

Chcete-li připojit sdílenou složku Azure jako svazek v kontejneru pomocí příkazového příkazu k řešení Azure, zadejte při vytváření kontejneru s [kontejnerem az][az-container-create]bod připojení svazku. Pokud jste postupovali podle předchozích kroků, můžete připojit sdílenou složku, kterou jste vytvořili dříve, pomocí následujícího příkazu k vytvoření kontejneru:

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

Hodnota `--dns-name-label` musí být jedinečný v rámci oblasti Azure, kde můžete vytvořit instanci kontejneru. Aktualizujte hodnotu v předchozím příkazu, pokud se při spuštění příkazu zobrazí chybová zpráva **popisku názvu DNS.**

## <a name="manage-files-in-mounted-volume"></a>Správa souborů v připojeném svazku

Po spuštění kontejneru můžete použít jednoduchou webovou aplikaci nasazenou prostřednictvím image Microsoft [aci-hellofiles][aci-hellofiles] k vytvoření malých textových souborů ve sdílené složce Azure na cestě připojení, kterou jste zadali. Získejte plně kvalifikovaný název domény (FQDN) webové aplikace pomocí příkazu [az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Po uložení textu pomocí aplikace můžete použít [portál Azure][portal] nebo nástroj, jako je Microsoft Azure [Storage Explorer][storage-explorer] k načtení a kontrole souboru nebo souborů zapsaného do sdílené složky.

## <a name="deploy-container-and-mount-volume---yaml"></a>Nasazení kontejneru a připojení svazku – YAML

Můžete také nasadit skupinu kontejnerů a připojit svazek v kontejneru pomocí azure CLI a [šablony YAML](container-instances-multi-container-yaml.md). Nasazení šablonou YAML je upřednostňovanou metodou při nasazování skupin kontejnerů skládajících se z více kontejnerů.

Následující šablona YAML definuje skupinu kontejnerů `aci-hellofiles` s jedním kontejnerem vytvořeným pomocí bitové kopie. Kontejner připojí sdílené sdílení souborů Azure *acishare* vytvořené dříve jako svazek. Tam, kde je uvedeno, zadejte název a klíč úložiště pro účet úložiště, který je hostitelem sdílené složky. 

Stejně jako v příkladu příkazového příkazového příkazu `dnsNameLabel` musí být hodnota jedinečná v rámci oblasti Azure, kde vytvoříte instanci kontejneru. V případě potřeby aktualizujte hodnotu v souboru YAML.

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

Chcete-li nasadit pomocí šablony YAML, uložte předchozí `deploy-aci.yaml`hodnotu YAML do souboru s názvem , poté spusťte příkaz [az container create][az-container-create] s parametrem: `--file`

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Nasazení kontejneru a připojení svazku – Správce prostředků

Kromě nasazení cli a YAML můžete nasadit skupinu kontejnerů a připojit svazek v kontejneru pomocí šablony Azure [Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplňte `volumes` pole v `properties` části skupiny kontejnerů šablony. 

Potom pro každý kontejner, ve kterém chcete připojit svazek, naplňte `volumeMounts` pole v `properties` části definice kontejneru.

Následující šablona Správce prostředků definuje skupinu kontejnerů `aci-hellofiles` s jedním kontejnerem vytvořeným pomocí bitové kopie. Kontejner připojí sdílené sdílení souborů Azure *acishare* vytvořené dříve jako svazek. Tam, kde je uvedeno, zadejte název a klíč úložiště pro účet úložiště, který je hostitelem sdílené složky. 

Stejně jako v předchozích příkladech `dnsNameLabel` musí být hodnota jedinečná v rámci oblasti Azure, kde vytvoříte instanci kontejneru. V případě potřeby aktualizujte hodnotu v šabloně.

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

Chcete-li nasadit pomocí šablony Správce prostředků, uložte `deploy-aci.json`předchozí json do souboru s `--template-file` názvem , pak spusťte příkaz create nasazení [skupiny az][az-group-deployment-create] s parametrem:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Připojení více svazků

Chcete-li připojit více svazků v instanci kontejneru, musíte nasadit pomocí [šablony Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), souboru YAML nebo jiné programové metody. Chcete-li použít šablonu nebo soubor YAML, zadejte podrobnosti o `volumes` sdílení `properties` a definujte svazky vyplněním pole v části souboru. 

Pokud jste například vytvořili dvě sdílené složky Azure Files s názvem `volumes` *share1* a *share2* v účtu úložiště *myStorageAccount*, pole v šabloně Správce prostředků se bude jevit podobně jako následující:

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

Dále pro každý kontejner ve skupině kontejnerů, ve kterém chcete připojit `volumeMounts` svazky, naplňte pole v `properties` části definice kontejneru. Například to připojí dva svazky, *myvolume1* a *myvolume2*, dříve definované:

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

Zjistěte, jak připojit další typy svazků v instanci kontejneru Azure:

* [Připojení svazku emptyDir v instancích kontejnerů Azure](container-instances-volume-emptydir.md)
* [Připojení svazku gitRepo v instanci kontejneru Azure](container-instances-volume-gitrepo.md)
* [Připojení tajného svazku v instancích kontejnerů Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create