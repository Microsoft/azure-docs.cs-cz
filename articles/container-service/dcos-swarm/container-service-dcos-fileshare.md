---
title: Sdílené složky pro cluster Azure DC/OS
description: Vytvořit a připojit sdílenou složku na clusteru DC/OS v Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2017
ms.locfileid: "26331198"
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Vytvořit a připojit sdílenou složku na clusteru DC/OS

V tomto kurzu podrobné informace o tom, jak vytvořit sdílenou složku v Azure a připojte ho na každého agenta a k hlavnímu serveru clusteru DC/OS. Nastavení sdílené složky usnadňuje sdílení souborů mezi vašeho clusteru, například konfigurace, přístup, protokoly a další. V tomto kurzu jsou prováděny následující úlohy:

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Vytvoření sdílené složky
> * Připojit sdílenou složku v clusteru DC/OS

Je třeba cluster služby ACS DC/OS, pokud chcete provést kroky v tomto kurzu. V případě potřeby [tento ukázkový skript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) můžete vytvořit za vás.

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Vytvoření sdílené složky v Microsoft Azure

Než sdílenou složku Azure pomocí clusteru služby ACS DC/OS, je třeba vytvořit sdílené složky pro účet a soubor úložiště. Spusťte následující skript pro vytvoření sdílené složky úložiště a souboru. Aktualizujte parametry thoes ze svého prostředí.

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

## <a name="mount-the-share-in-your-cluster"></a>Připojit sdílenou složku v clusteru

V dalším kroku sdílené složky musí připojit na každý virtuální počítač v rámci clusteru. Tato úloha skončí, pomocí nástroje/protokol cifs. Operace připojení můžete dokončit ručně na každém uzlu clusteru, nebo spuštěním skriptu na každém uzlu v clusteru.

V tomto příkladu dva skripty se spouštějí, jeden připojit sdílenou složkou Azure a druhou spusťte tento skript na každém uzlu clusteru DC/OS.

Nejdřív je potřeba název účtu úložiště Azure a přístupový klíč. Spusťte následující příkazy k získání těchto informací. Všimněte si, se používají tyto hodnoty později.

Název účtu úložiště:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Přístupový klíč účtu úložiště:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

V dalším kroku získat plně kvalifikovaný název domény hlavního serveru DC/OS a uložte ho do proměnné.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Zkopírujte privátní klíč do hlavní uzel. Tento klíč je potřeba k vytvoření ssh připojení se všechny uzly v clusteru. Aktualizujte uživatelské jméno, pokud při vytvoření clusteru se použila jiná než výchozí hodnotu. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Vytvoření připojení SSH s hlavním serverem (nebo prvního hlavního serveru) ve vašem clusteru založenými na DC/OS. Aktualizujte uživatelské jméno, pokud při vytvoření clusteru se použila jiná než výchozí hodnotu.

```azurecli-interactive
ssh azureuser@$FQDN
```

Vytvořte soubor s názvem **cifsMount.sh**a zkopírujte do něj následující obsah. 

Tento skript se používá k připojení Azure sdílené složky. Aktualizace `STORAGE_ACCT_NAME` a `ACCESS_KEY` shromážděny proměnné s informacemi dříve.

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
Vytvořte druhý soubor s názvem **getNodesRunScript.sh** a zkopírujte následující obsah do souboru. 

Tento skript zjistí všechny uzly clusteru a poté spustí **cifsMount.sh** skript pro připojení sdílené složky na každém.

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

Spusťte skript pro připojení Azure sdílené složky na všech uzlech clusteru.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Sdílené složky je nyní přístupná na `/mnt/share/dcosshare` na každém uzlu clusteru.

## <a name="next-steps"></a>Další kroky

V tomto kurzu Azure byl proveden sdílené složky k dispozici pro cluster DC/OS pomocí kroků:

> [!div class="checklist"]
> * Vytvoření účtu úložiště Azure
> * Vytvoření sdílené složky
> * Připojit sdílenou složku v clusteru DC/OS

Přechodu na v dalším kurzu se dozvíte o integraci služby Azure kontejneru registru s DC/OS v Azure.  

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení aplikací](container-service-dcos-acr.md)
