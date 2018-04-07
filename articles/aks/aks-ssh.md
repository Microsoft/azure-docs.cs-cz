---
title: SSH do uzlů clusteru Azure Container Service (AKS)
description: Vytvoření připojení SSH pomocí clusteru služby Azure Container Service (AKS) uzly
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH do uzlů clusteru Azure Container Service (AKS)

V některých případech může potřebujete přístup k uzlu Azure Container Service (AKS) pro údržby, shromáždění protokolů nebo jiné řešení potíží operace. Uzlů Azure Container Service (AKS) nejsou vystaveny v Internetu. Vytvoření připojení SSH pomocí do uzlu AKS použijte kroky popsané v tomto dokumentu.

## <a name="get-aks-node-address"></a>Získat adresu AKS uzlu

Získat IP adresu k AKS clusteru uzlu pomocí `az vm list-ip-addresses` příkaz. Nahraďte název skupiny prostředků s názvem vaší skupiny prostředků AKS.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Vytvoření připojení SSH

Spustit `debian` kontejneru bitovou kopii a k němu připojí relaci terminálu. Kontejner pak lze vytvořit relace SSH pomocí libovolného uzlu v clusteru AKS.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Instalace klienta SSH v kontejneru.

```console
apt-get update && apt-get install openssh-client -y
```

Seznam všech pracovními stanicemi soustředěnými kolem získat název nově vytvořený pod a otevřete druhý terminál.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Kopírování klíče SSH na pod, nahraďte název pod správnou hodnotu.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Aktualizace `id_rsa` souboru tak, aby se uživatel jen pro čtení.

```console
chmod 0600 id_rsa
```

Teď vytvořte připojení SSH k uzlu AKS. Je výchozí uživatelské jméno pro cluster služby AKS `azureuser`. Pokud tento účet byl změněn v okamžiku vytvoření clusteru, nahraďte správce správné uživatelské jméno.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Odebrat přístup SSH

Až budete hotoví, ukončete relaci SSH a pak kontejneru interaktivní relace. Tato akce odstraní pod používá pro přístup k SSH z AKS clusteru.