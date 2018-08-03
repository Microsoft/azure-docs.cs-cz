---
title: Vytvoření clusteru Azure Kubernetes Service (AKS)
description: Vytvoření clusteru AKS pomocí rozhraní příkazového řádku nebo na webu Azure portal.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5c133c61c989bf19be3e84287cb76a7d110dccc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440469"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Vytvoření clusteru Azure Kubernetes Service (AKS)

Cluster Azure Kubernetes Service (AKS) lze vytvořit pomocí rozhraní příkazového řádku Azure nebo na webu Azure portal.

## <a name="azure-cli"></a>Azure CLI

Použití [az aks vytvořit] [ az-aks-create] příkaz pro vytvoření clusteru AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Tyto možnosti jsou dostupné `az aks create` příkazu. Zobrazit [referenční informace k Azure CLI] [ az-aks-create] pro AKS Další informace o každé z těchto argumentů.

| Argument | Popis | Požaduje se |
|---|---|:---:|
| `--name` `-n` | Název prostředku pro spravovaný cluster. | ano |
| `--resource-group` `-g` | Název skupiny prostředků Azure Kubernetes Service. | ano |
| `--admin-username` `-u` | Uživatelské jméno pro Linux Virtual Machines.  Výchozí: azureuser. | ne |
| `--aad-client-app-id` | (PREVIEW) ID klienta aplikace Azure Active Directory typu "Nativní". | ne |
| `--aad-server-app-id` | (PREVIEW) ID aplikace Azure Active Directory server typu "Aplikace webového rozhraní API". | ne |
| `--aad-server-app-secret` | (PREVIEW) Tajný klíč serveru aplikace Azure Active Directory. | ne |
| `--aad-tenant-id` | (PREVIEW) ID tenanta služby Azure Active Directory. | ne |
| `--admin-username` `-u` | Uživatelský účet k vytvoření na virtuálních počítačích uzlů pro přístup přes SSH.  Výchozí: azureuser. | ne |
| ` --client-secret` | Tajný klíč přidružený k instančnímu objektu. | ne |
| `--dns-name-prefix` `-p` | Předpona DNS pro veřejnou ip adresu clustery. | ne |
| `--dns-service-ip` | IP adresa přiřazená ke službě Kubernetes DNS. | ne |
| `--docker-bridge-address` | IP adresa a síťová maska přiřazené ke mostu Docker. | ne |
| `--enable-addons` `-a` | Povolte addons Kubernetes v seznamu odděleném čárkami. | ne |
| `--enable-rbac` `-r` | Povolte řízení přístupu na základě rolí Kubernetes. | ne |
| `--generate-ssh-keys` | Generovat soubory veřejného a privátního klíče SSH, pokud chybí. | ne |
| `--kubernetes-version` `-k` | Verze Kubernetes pro použití při vytváření clusteru, například '1.7.9' nebo "otázku 1.9.6". | ne |
| `--locaton` `-l` | Umístění pro skupinu automaticky vytvořený prostředek | ne |
| `--max-pods` `-m` | Maximální počet podů nasaditelný k uzlu. | ne |
| `--network-plugin` | Použití modulu plug-in sítě Kubernetes. | ne |
| `--no-ssh-key` `-x` | Použít nebo vytvořit místní klíč SSH. | ne |
| `--no-wait` | Nechcete čekat na dokončení dlouho běžící operace. | ne |
| `--node-count` `-c` | Výchozí počet uzlů pro fondy uzlů.  Výchozí: 3. | ne |
| `--node-osdisk-size` | OsDisk velikost v GB fond uzlů virtuálních počítačů. | ne |
| `--node-vm-size` `-s` | Velikost virtuálního počítače.  Výchozí: Standard_D1_v2. | ne |
| `--pod-cidr` | Zápis CIDR IP oblast ze kterého chcete přiřadit pod IP adresy, když se používá kubenet. | ne |
| `--service-cidr` | CIDR rozsahu IP zápis ze kterého chcete přiřadit cluster service IP adresy. | ne |
| `--service-principal` | Instanční objekt služby používat k ověřování clusteru. | ne |
| `--ssh-key-value` | SSH hodnotu nebo klíč souboru cesta k souboru klíče.  Výchozí hodnota: ~ /.ssh/id_rsa.pub. | ne |
| `--tags` | Místo oddělený značek v "[= value] klíčem" formátu. Použití "Vymazat existující značky. | ne |
| `--vnet-subnet-id` | ID podsítě v existující virtuální síť, do které chcete nasadit cluster. | ne |
| `--workspace-resource-id` | ID prostředku existující pracovní prostor Log Analytics k ukládání dat monitorování. | ne |

## <a name="azure-portal"></a>portál Azure

Pokyny k nasazení clusteru AKS pomocí webu Azure portal, najdete v části Azure Kubernetes Service (AKS) [rychlém startu pro Azure portal][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
