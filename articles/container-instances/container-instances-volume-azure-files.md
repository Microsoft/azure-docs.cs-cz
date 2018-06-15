---
title: Připojit Azure Files svazek v Azure kontejner instancí
description: Zjistěte, jak připojit Azure Files svazek k uchování stavu s instancemi Azure kontejneru
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 239150c1e752ce6a4f2a19fa1192cd1a910ebea9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166792"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Připojit sdílenou složku Azure v Azure kontejner instancí

Ve výchozím nastavení jsou bezstavové instancí kontejnerů Azure. Pokud kontejner dojde k chybě nebo zastaví, všechny její stav bude ztracena. K zachování stavu nad rámec životnost kontejneru, je nutné připojit svazek z externího úložiště. Tento článek ukazuje, jak připojit sdílenou složku Azure pro použití s instancemi Azure kontejneru.

> [!NOTE]
> Připojení Azure Files složky je aktuálně omezeno na kontejnery Linux. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Před použitím sdílenou složku Azure s Azure kontejner instancí, je třeba ji vytvořit. Spusťte následující skript pro vytvoření účtu úložiště pro hostování sdílené složky a sdílené složky sám sebe. Název účtu úložiště musí být globálně jedinečné, takže skript přidá do základní řetězec náhodná hodnota.

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

## <a name="get-storage-credentials"></a>Získání přihlašovacích údajů úložiště

Chcete-li jako svazek v instancí kontejnerů Azure připojit sdílenou složku Azure, je třeba tří hodnot: název účtu úložiště, název sdílené složky a přístupový klíč úložiště.

Pokud jste použili výše uvedený skript, název účtu úložiště byl vytvořen s hodnotou náhodných na konci. Chcete-li prohledávat posledním řetězci (včetně náhodných část), použijte následující příkazy:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

Název sdílené složky je již znám (definován jako *acishare* ve skriptu výše), takže všechny, že zůstanou je klíč účtu úložiště, které lze najít pomocí následujícího příkazu:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Nasazení kontejneru a připojení svazku

Chcete-li připojit sdílenou složku Azure jako svazek v kontejneru, zadejte do sdílené složky a svazek přípojný bod, když vytvoříte kontejner s [vytvořit kontejner az][az-container-create]. Pokud jste postupovali podle předchozích kroků, můžete připojit sdílenou složku, kterou jste dříve vytvořili pomocí následujícího příkazu k vytvoření kontejneru:

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

Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci kontejneru. Aktualizujte hodnotu v předchozím příkazu, pokud se zobrazí **Popisek názvu DNS** chybová zpráva při spuštění příkazu.

## <a name="manage-files-in-mounted-volume"></a>Správa souborů v připojeného svazku

Po spuštění kontejneru, můžete jednoduché webové aplikace nasazené prostřednictvím [microsoft/aci-hellofiles] [ aci-hellofiles] image ke správě souborů v Azure sdílené složky v cestě připojení jste zadali. Získat webové aplikace plně kvalifikovaný název domény (FQDN) s [az kontejneru zobrazit] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Můžete použít [portál Azure] [ portal] nebo nástroje, jako [Microsoft Azure Storage Explorer] [ storage-explorer] k načtení a zkontrolujte soubor zapsána do sdílené složky.

## <a name="mount-multiple-volumes"></a>Připojení více svazků

Připojení více svazků v instanci kontejneru, je nutné nasadit pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejdřív zadejte podrobnosti sdílené složky a definování svazky naplněním `volumes` pole v `properties` část šablony. Například pokud jste vytvořili dvou sdílených složek soubory Azure s názvem *sdílená_složka1* a *share2* v účtu úložiště *Můj_účet_úložiště*, `volumes` se zobrazí pole podobný následujícímu:

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

V dalším kroku pro každý kontejner ve skupině kontejner, ve kterém byste chtěli připojit svazky naplnit `volumeMounts` pole v `properties` části definici kontejneru. Například to připojí dva svazky *myvolume1* a *myvolume2*, dříve definovaném:

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

Příklad nasazení kontejneru instance pomocí šablony Azure Resource Manager, najdete v sekci [nasazení skupiny více kontejnerů v Azure kontejner instancí](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit jiné typy svazku v Azure kontejner instancí:

* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojení svazku gitRepo v Azure kontejner instancí](container-instances-volume-gitrepo.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/microsoft/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show