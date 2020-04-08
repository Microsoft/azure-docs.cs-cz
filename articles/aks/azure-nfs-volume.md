---
title: Vytvoření svazku nfs ubuntu linuxového serveru
titleSuffix: Azure Kubernetes Service
description: Naučte se ručně vytvořit svazek NFS Ubuntu Linux Server pro použití s pody ve službě Azure Kubernetes Service (AKS).
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 7db3f806df88e5b23012e97ba5c2f14ca65b2508
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803462"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku serveru Linux Serveru nfs (network file system) se službou Azure Kubernetes Service (AKS)
Sdílení dat mezi kontejnery je často nezbytnou součástí služeb a aplikací založených na kontejnerech. Obvykle máte různé pody, které potřebují přístup ke stejným informacím na externím trvalém svazku.    
Zatímco soubory Azure jsou možnost, vytvoření nfs serveru na virtuálním počítači Azure je další forma trvalé sdílené úložiště. 

Tento článek vám ukáže, jak vytvořit nfs server na virtuálním stroji Ubuntu. A také poskytnout aks kontejnery přístup k tomuto sdílenému systému souborů.

## <a name="before-you-begin"></a>Než začnete
Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Cluster AKS bude muset žít ve stejných nebo partnerských virtuálních sítích jako server NFS. Cluster musí být vytvořen v existující virtuální síti, která může být stejná virtuální síť jako virtuální počítač.

Postup konfigurace s existující virtuální sítí je popsán v dokumentaci: [vytvoření clusteru AKS v existující virtuální síti][aks-virtual-network] a připojení [virtuálních sítí s partnerským vztahem virtuální sítě][peer-virtual-networks]

To také předpokládá, že jste vytvořili Ubuntu Linux Virtual Machine (například 18.04 LTS). Nastavení a velikost může být podle vašich představ a lze je nasadit prostřednictvím Azure. Informace o rychlém startu linuxu najdete v [tématu Správa virtuálních počítačů v Linuxu][linux-create].

Pokud nejprve nasadíte cluster AKS, Azure automaticky naplní pole virtuální sítě při nasazování počítače Ubuntu, takže budou žít ve stejné virtuální síti. Ale pokud chcete pracovat s partnerskými sítěmi místo toho, podívejte se do dokumentace výše.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Nasazení serveru NFS do virtuálního počítače
Zde je skript pro nastavení nfs serveru ve vašem virtuálním stroji Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Server se restartuje (z důvodu skriptu) a můžete připojit nfs server na AKS.

>[!IMPORTANT]  
>Ujistěte se, že **nahradit AKS_SUBNET** s ten správný z clusteru, jinak "*" otevře server NFS na všechny porty a připojení.

Po vytvoření virtuálního počítače zkopírujte výše uvedený skript do souboru. Potom můžete přesunout z místního počítače nebo kdekoli skript je do virtuálního počítače pomocí: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Jakmile je váš skript ve vašem virtuálním počítači, můžete ssh do virtuálního počítače a spustit jej pomocí příkazu:
```console
sudo ./nfs-server-setup.sh
```
Pokud se jeho spuštění nezdaří z důvodu chyby odepření oprávnění, nastavte oprávnění k spuštění pomocí příkazu:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Připojení clusteru AKS k serveru nfs
Server nfs můžeme připojit k našemu clusteru zřízením trvalého svazku a trvalé deklarace svazku, která určuje způsob přístupu ke svazku.

Připojení dvou služeb ve stejných nebo partnerských virtuálních sítích je nezbytné. Pokyny pro nastavení clusteru ve stejné virtuální síti jsou tady: [Vytvoření clusteru AKS v existující virtuální síti][aks-virtual-network]

Jakmile jsou ve stejné virtuální síti (nebo peered), je třeba zřídit trvalý svazek a trvalé deklarace svazku v clusteru AKS. Kontejnery pak můžete připojit jednotku nfs do svého místního adresáře.

Tady je příklad Kubernetesdefinice pro trvalý svazek (Tato definice předpokládá, že cluster a virtuální počítač jsou ve stejné virtuální síti):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Nahraďte **NFS_INTERNAL_IP**, **NFS_NAME** a **NFS_EXPORT_FILE_PATH** informacemi o serveru systému sdílení sítí NFS.

Budete také potřebovat trvalý soubor deklarací svazku. Zde je příklad toho, co zahrnout:

>[!IMPORTANT]  
>**"storageClassName"** musí zůstat prázdný řetězec nebo deklarace nebude fungovat.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Řešení potíží
Pokud se nemůžete připojit k serveru z clusteru, problém může být exportovaný adresář nebo jeho nadřazený adresář nemá dostatečná oprávnění pro přístup k serveru.

Zkontrolujte, zda exportní i nadřazený adresář mají oprávnění 777.

Oprávnění můžete zkontrolovat spuštěním níže uvedeného příkazu a adresáře by měly mít oprávnění *"drwxrwxrwx":*
```console
ls -l
```

## <a name="more-information"></a>Další informace
Chcete-li získat úplný návod nebo vám pomoci s laděním nastavení serveru NFS, je zde podrobný kurz:
  - [Výuka nfs][nfs-tutorial]

## <a name="next-steps"></a>Další kroky

Doporučené postupy najdete v [tématu Doporučené postupy pro ukládání a zálohování v AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
