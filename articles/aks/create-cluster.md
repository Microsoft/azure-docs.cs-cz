---
title: Vytvoření clusteru Azure Kubernetes služby (AKS)
description: Vytvoření clusteru AKS pomocí rozhraní příkazového řádku nebo portálu Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 684bf44c6c3c67ce1d5c798f3406270d76a8e2fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029228"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Vytvoření clusteru Azure Kubernetes služby (AKS)

Cluster služby Azure Kubernetes služby (AKS) lze vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu Azure.

## <a name="azure-cli"></a>Azure CLI

Použití [vytvořit az aks] [ az-aks-create] příkaz k vytvoření clusteru AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Jsou k dispozici následující možnosti `az aks create` příkaz. Najdete v článku [referenční dokumentace rozhraní příkazového řádku Azure] [ az-aks-create] pro AKS pro další informace o každém z těchto argumentů.

| Argument | Popis | Požaduje se |
|---|---|:---:|
| `--name` `-n` | Název prostředku pro spravovaný cluster. | ano |
| `--resource-group` `-g` | Název skupiny prostředků Azure Kubernetes Service. | ano |
| `--admin-username` `-u` | Uživatelské jméno pro virtuální počítače se systémem Linux.  Výchozí: azureuser. | ne |
| `--aad-client-app-id` | (PREVIEW) ID klienta aplikace Azure Active Directory typu "Nativní". | ne |
| `--aad-server-app-id` | (PREVIEW) ID aplikace Azure Active Directory serveru typu "Aplikace webového rozhraní API". | ne |
| `--aad-server-app-secret` | (PREVIEW) Tajný klíč serveru aplikace Azure Active Directory. | ne |
| `--aad-tenant-id` | (PREVIEW) ID klienta služby Azure Active Directory. | ne |
| `--admin-username` `-u` | Uživatelský účet, chcete-li vytvořit v uzlu virtuálních počítačů pro přístup SSH.  Výchozí: azureuser. | ne |
| ` --client-secret` | Tajný klíč přidružený k instančnímu objektu. | ne |
| `--dns-name-prefix` `-p` | Předpona DNS pro clustery veřejnou ip adresu. | ne |
| `--dns-service-ip` | Ve službě Kubernetes DNS přiřazené IP adresy. | ne |
| `--docker-bridge-address` | IP adresa a přiřazené k mostu Docker síťová maska. | ne |
| `--enable-addons` `-a` | Povolte rozšíření Kubernetes v seznamu odděleném čárkami. | ne |
| `--enable-rbac` `-r` | Povolení řízení přístupu na základě rolí Kubernetes. | ne |
| `--generate-ssh-keys` | Pokud chybí, generovat SSH soubory veřejného a privátního klíče. | ne |
| `--kubernetes-version` `-k` | Verze Kubernetes používat pro vytváření clusteru, například '1.7.9' nebo 'otázku 1.9.6'. | ne |
| `--locaton` `-l` | Umístění pro skupinu prostředků automaticky vytvořené | ne |
| `--max-pods` `-m` | Maximální počet pracovními stanicemi soustředěnými kolem nasadit do uzlu. | ne |
| `--network-plugin` | Modul plug-in síťové Kubernetes používat. | ne |
| `--no-ssh-key` `-x` | Použít nebo vytvořit místní klíč SSH. | ne |
| `--no-wait` | Není počkejte na dokončení operace časově náročné. | ne |
| `--node-count` `-c` | Výchozí počet uzlů pro fondy uzlu.  Výchozí: 3. | ne |
| `--node-osdisk-size` | OsDisk velikost v GB fondu uzlu virtuálního počítače. | ne |
| `--node-vm-size` `-s` | Velikost virtuálního počítače.  Výchozí: Standard_D1_v2. | ne |
| `--pod-cidr` | Zápis CIDR IP rozsah ze kterého chcete přiřadit pod IP adres, pokud se používá kubenet. | ne |
| `--service-cidr` | Zápis CIDR IP rozsah ze kterého chcete přiřadit adresy IP služby clusteru. | ne |
| `--service-principal` | Objekt zabezpečení služby použít k ověření clusteru. | ne |
| `--ssh-key-value` | SSH soubor klíče hodnotu nebo klíč cesta k souboru.  Výchozí hodnota: ~ /.ssh/id_rsa.pub. | ne |
| `--tags` | Místo oddělené značky v "klíčem [= value]" formátu. Použití s vymažete stávající značky. | ne |
| `--vnet-subnet-id` | ID podsíť ve stávající virtuální síť, do které chcete nasadit cluster. | ne |
| `--workspace-resource-id` | ID prostředku existujícímu pracovnímu prostoru analýzy protokolů pro ukládání dat monitorování. | ne |

## <a name="azure-portal"></a>Azure Portal

Pokyny k nasazení clusteru služby AKS pomocí portálu Azure, najdete v části služby Kubernetes Azure (AKS) [Azure rychlý start portálu][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
