---
title: Připojení svazku služby soubory Azure ve službě Azure Container Instances
description: Zjistěte, jak připojení svazku služby soubory Azure k uložení stavu pomocí služby Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 83c86d8310aff80f148e878261ba33b01846006b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441319"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Připojení sdílené složky Azure ve službě Azure Container Instances

Ve výchozím nastavení Azure Container Instances jsou bezstavové. Pokud kontejner dojde k chybě nebo zastaví, veškerý její stav bude ztracena. Zachovat stav i mimo dobu života kontejneru, musí připojit svazek z externího úložiště. Tento článek ukazuje, jak připojit sdílenou složku Azure pro použití se službou Azure Container Instances.

> [!NOTE]
> Připojením sdílené složky služby soubory Azure je momentálně omezené jenom na Linuxové kontejnery. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

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

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Získat přihlašovací údaje úložiště

Připojení sdílené složky Azure jako svazku ve službě Azure Container Instances, tři hodnoty budete potřebovat: název účtu úložiště, název sdílené složky a přístupový klíč úložiště.

Pokud jste použili výše uvedené skriptu, název účtu úložiště byl vytvořen s náhodnou hodnotu na konci. Dotaz posledním řetězci uvedené (včetně náhodného části), použijte následující příkazy:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

Název sdílené složky je již znám (definované jako *acishare* ve skriptu výše), takže všechny, zůstane je klíč účtu úložiště, které lze nalézt pomocí následujícího příkazu:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Nasazení kontejneru a připojení svazku

Připojení sdílené složky Azure jako svazek v kontejneru, zadejte do sdílené složky a svazek přípojný bod při vytváření kontejneru s [az container vytvořit][az-container-create]. Pokud jste postupovali podle předchozích kroků, můžete připojit sdílenou složku, kterou jste vytvořili dříve, pomocí následujícího příkazu vytvořte kontejner:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image microsoft/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci kontejneru. Aktualizujte hodnotu v předchozím příkazu, pokud se zobrazí **popisku názvu DNS** při spuštění příkazu zobrazí chybová zpráva.

## <a name="manage-files-in-mounted-volume"></a>Správa souborů ve svazku připojeném

Po spuštění kontejneru můžete jednoduché webové aplikace nasazené prostřednictvím [microsoft/aci-hellofiles] [ aci-hellofiles] image ke správě souborů ve sdílené složky Azure v zadané cestě připojení. Získat webové aplikace plně kvalifikovaný název domény (FQDN) se [az container show] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Můžete použít [webu Azure portal] [ portal] nebo nástroj, jako je [Microsoft Azure Storage Explorer] [ storage-explorer] načíst a zkontrolovat zapsána do souboru sdílení souborů.

## <a name="mount-multiple-volumes"></a>Připojení více svazků

Připojení více svazků v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve zadejte podrobnosti o sdílené složce a definovat svazky naplněním `volumes` obsahuje pole `properties` část šablony. Například, pokud jste vytvořili dva sdílených složek Azure s názvem *sdílená_složka1* a *share2* v účtu úložiště *myStorageAccount*, `volumes` pole by se mělo zobrazit podobný následujícímu:

```json
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

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Příklad nasazením instance kontejneru pomocí šablony Azure Resource Manageru najdete v tématu [nasazení skupin více kontejnerů ve službě Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit další typy svazku ve službě Azure Container Instances:

* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojit svazek gitRepo ve službě Azure Container Instances](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/microsoft/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show