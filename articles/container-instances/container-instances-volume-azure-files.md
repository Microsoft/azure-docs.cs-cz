---
title: Připojení svazku služby soubory Azure ve službě Azure Container Instances
description: Zjistěte, jak připojení svazku služby soubory Azure k uložení stavu pomocí služby Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657626"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Připojení sdílené složky Azure ve službě Azure Container Instances

Ve výchozím nastavení Azure Container Instances jsou bezstavové. Pokud kontejner dojde k chybě nebo zastaví, veškerý její stav bude ztracena. Zachovat stav i mimo dobu života kontejneru, musí připojit svazek z externího úložiště. Tento článek popisuje, jak připojit sdílenou složku Azure vytvořené pomocí [Azure Files](../storage/files/storage-files-introduction.md) pro použití se službou Azure Container Instances. Azure Files nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní protokol Server Message Block (SMB). Sdílené složky Azure pomocí Azure Container Instances nabízí sdílení souborů funkce podobně jako u sdílené složky Azure s virtuálními počítači Azure.

> [!NOTE]
> Připojením sdílené složky služby soubory Azure je momentálně omezené jenom na Linuxové kontejnery. Pracujeme na všechny funkce byly do kontejnerů Windows, můžete najít aktuální rozdíly pro tyto platformy v [přehled](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Než začnete používat sdílené složky Azure pomocí služby Azure Container Instances, je třeba ji vytvořit. Spusťte následující skript k vytvoření účtu úložiště pro hostování sdílené složky a sdílené složky, samotného. Název účtu úložiště musí být globálně jedinečný, takže skript přidá do řetězce base náhodnou hodnotu.

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

Připojení sdílené složky Azure jako svazku ve službě Azure Container Instances, tři hodnoty budete potřebovat: název účtu úložiště, název sdílené složky a přístupový klíč úložiště.

Pokud jste použili výše uvedené skriptu, název účtu úložiště byl uložen v proměnné ACI_PERS_STORAGE_ACCOUNT_NAME $. Pokud chcete zobrazit název účtu, zadejte:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Název sdílené složky je již znám (definované jako *acishare* ve skriptu výše), takže všechny, zůstane je klíč účtu úložiště, které lze nalézt pomocí následujícího příkazu:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Nasazení kontejneru a připojit svazek – rozhraní příkazového řádku

Připojení sdílené složky Azure jako svazek v kontejneru pomocí rozhraní příkazového řádku Azure, zadejte do sdílené složky a svazek přípojný bod při vytváření kontejneru s [az container vytvořit][az-container-create]. Pokud jste postupovali podle předchozích kroků, můžete připojit sdílenou složku, kterou jste vytvořili dříve, pomocí následujícího příkazu vytvořte kontejner:

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

`--dns-name-label` Hodnota musí být jedinečný v rámci oblasti Azure, ve kterém vytváříte instanci kontejneru. Aktualizujte hodnotu v předchozím příkazu, pokud se zobrazí **popisku názvu DNS** při spuštění příkazu zobrazí chybová zpráva.

## <a name="manage-files-in-mounted-volume"></a>Správa souborů ve svazku připojeném

Po spuštění kontejneru můžete jednoduché webové aplikace nasazené prostřednictvím Microsoft [aci hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Po uložení textu s použitím aplikace, můžete použít [webu Azure portal][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] načíst a zkontrolovat soubor zapsán do sdílené složky.

## <a name="deploy-container-and-mount-volume---yaml"></a>Nasazení kontejneru a připojit svazek - YAML

Můžete také nasadit skupinu kontejnerů a připojení svazku v kontejneru pomocí Azure CLI a [šablon YAML](container-instances-multi-container-yaml.md). Nasazení pomocí šablon YAML je upřednostňovanou metodou při nasazování skupiny kontejnerů, který se skládá z několika kontejnerů.

Následující šablony YAML definuje skupinu kontejnerů s vytvořené pomocí jednoho kontejneru `aci-hellofiles` bitové kopie. Kontejner připojí sdílená složka Azure *acishare* nevytvořili jako svazek. Uvedeno, zadejte název a úložiště klíč pro účet úložiště, který je hostitelem sdílené složky. 

Jako příklad rozhraní příkazového řádku `dnsNameLabel` hodnota musí být jedinečný v rámci oblasti Azure, ve kterém vytváříte instanci kontejneru. V případě potřeby aktualizujte hodnotu v souboru YAML.

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

K nasazení pomocí šablony YAML, uložit do souboru s názvem předchozí YAML `deploy-aci.yaml`, provádějí [az container vytvořit][az-container-create] příkazů `--file` parametr:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Nasazení kontejneru a připojení svazku - Resource Manageru

Kromě rozhraní příkazového řádku a YAML nasazení, můžete nasadit skupinu kontejnerů a připojení svazku v kontejneru s použitím Azure [šablony Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplnit `volumes` pole ve skupině kontejnerů `properties` část šablony. 

Naplnit pro každý kontejner, ve kterém chcete připojení svazku, `volumeMounts` obsahuje pole `properties` část definice kontejneru.

Následující šablony Resource Manageru definuje skupinu kontejnerů s vytvořené pomocí jednoho kontejneru `aci-hellofiles` bitové kopie. Kontejner připojí sdílená složka Azure *acishare* nevytvořili jako svazek. Uvedeno, zadejte název a úložiště klíč pro účet úložiště, který je hostitelem sdílené složky. 

Stejně jako v předchozích příkladech `dnsNameLabel` hodnota musí být jedinečný v rámci oblasti Azure, ve kterém vytváříte instanci kontejneru. V případě potřeby aktualizujte hodnotu v šabloně.

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

K nasazení pomocí šablony Resource Manageru uložit do souboru s názvem výše uvedeného kódu JSON `deploy-aci.json`, provádějí [vytvořit nasazení skupiny pro az][az-group-deployment-create] příkazů `--template-file` parametr:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Připojení více svazků

Připojení více svazků v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups) nebo soubor YAML. Chcete-li použít šablonu nebo soubor YAML, zadejte podrobnosti o sdílené složce a definovat svazky naplněním `volumes` obsahuje pole `properties` část šablony. 

Například, pokud jste vytvořili dva sdílených složek Azure s názvem *sdílená_složka1* a *share2* v účtu úložiště *myStorageAccount*, `volumes` pole v Resource Manageru Šablona by vypadat přibližně takto:

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

V dalším kroku pro každý kontejner ve skupině kontejnerů, ve kterém byste chtěli připojit svazky naplnit `volumeMounts` obsahuje pole `properties` část definice kontejneru. Například to připojí dva svazky *myvolume1* a *myvolume2*, dříve definovanou:

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

Zjistěte, jak připojit další typy svazku ve službě Azure Container Instances:

* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojit svazek gitRepo ve službě Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create