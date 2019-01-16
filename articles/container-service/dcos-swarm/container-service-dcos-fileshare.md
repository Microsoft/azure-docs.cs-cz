---
title: (NEPOUŽÍVANÉ) Sdílení souborů pro cluster Azure DC/OS
description: Vytvoření sdílené složky a její připojení ke clusteru DC/OS ve službě Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329816"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(NEPOUŽÍVANÉ) Vytvořit a připojit sdílenou složku na clusteru DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Tento kurz podrobně popisuje vytvoření sdílené složky v Azure a její připojení ke všem agentům a hlavnímu uzlu clusteru DC/OS. Nastavení sdílené složky usnadňuje sdílení souborů, jako jsou konfigurační a přístupové soubory, protokoly a další, v rámci clusteru. V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Vytvoření sdílené složky
> * Připojení sdílené složky v clusteru DC/OS

K provedení kroků v tomto kurzu potřebujete cluster DC/OS ACS. V případě potřeby si ho můžete nechat vytvořit pomocí [tohoto ukázkového skriptu](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgradovat, přečtěte si článek [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Vytvoření sdílené složky v Microsoft Azure

Než začnete používat sdílenou složku Azure s clusterem ACS DC/OS, je potřeba vytvořit účet úložiště a sdílenou složku. Spuštěním následujícího skriptu vytvořte úložiště a sdílenou složku. Aktualizujte parametry s těmi, která z vašeho prostředí.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Připojení sdílené složky v clusteru

Dále je potřeba sdílenou složku připojit ke všem virtuálním počítačům v rámci clusteru. Tuto úlohu provedete pomocí nástroje a protokolu cifs. Operaci připojení je možné provést ručně na jednotlivých uzlech clusteru nebo spuštěním skriptu pro každý uzel v clusteru.

V tomto příkladu se spustí dva skripty. Jeden připojí sdílenou složku Azure a druhý tento skript spustí na každém uzlu clusteru DC/OS.

Nejprve je potřeba název a přístupový klíč účtu úložiště Azure. Tyto informace získáte spuštěním následujících příkazů. Obě hodnoty si poznamenejte, protože je použijete v dalším kroku.

Název účtu úložiště:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Přístupový klíč účtu úložiště:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Nejprve získejte plně kvalifikovaný název domény hlavního uzlu DC/OS a uložte ho jako proměnnou.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Zkopírujte do hlavního uzlu svůj privátní klíč. Tento klíč je potřeba k vytvoření připojení SSH ke všem uzlům v clusteru. Pokud se při vytváření clusteru použila jiná než výchozí hodnota, aktualizujte uživatelské jméno. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Vytvořte připojení SSH k této předloze (nebo první předloze) clusteru založeného na DC/OS. Pokud se při vytváření clusteru použila jiná než výchozí hodnota, aktualizujte uživatelské jméno.

```azurecli-interactive
ssh azureuser@$FQDN
```

Vytvořte soubor **cifsMount.sh** a zkopírujte do něj následující obsah. 

Tento skript slouží k připojení sdílené složky Azure. Aktualizujte proměnné `STORAGE_ACCT_NAME` a `ACCESS_KEY` s použitím informací získaných dříve.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Vytvořte druhý soubor **getNodesRunScript.sh** a zkopírujte do něj následující obsah. 

Tento skript vyhledá všechny uzly clusteru a pak spustí skript **cifsMount.sh**, který ke každému z nich připojí sdílenou složku.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Spusťte skript, aby se sdílená složka Azure připojila ke všem uzlům clusteru.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Sdílená složka je teď na každém uzlu clusteru dostupná na adrese `/mnt/share/dcosshare`.

## <a name="next-steps"></a>Další postup

V tomto kurzu se pomocí následujících kroků zpřístupnila sdílená složka Azure pro cluster DC/OS:

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Vytvoření sdílené složky
> * Připojení sdílené složky v clusteru DC/OS

V dalším kurzu se dozvíte, jak integrovat službu Azure Container Registry s DC/OS v Azure.  

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení aplikací](container-service-dcos-acr.md)
