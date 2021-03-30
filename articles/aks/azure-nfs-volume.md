---
title: Vytvoření svazku Ubuntu Linuxho serveru NFS
titleSuffix: Azure Kubernetes Service
description: Naučte se ručně vytvořit svazek Ubuntu Linuxho serveru NFS pro použití s lusky ve službě Azure Kubernetes Service (AKS).
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86251990"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku serveru NFS (Network File System) se systémem Linux pomocí služby Azure Kubernetes Service (AKS)
Sdílení dat mezi kontejnery je často nezbytnou součástí služeb a aplikací založených na kontejnerech. Obvykle máte různá lusky, které potřebují přístup ke stejným informacím na vnějším trvalém svazku.    
I když jsou soubory Azure možnosti, vytvoření serveru NFS na virtuálním počítači Azure je další forma trvalého sdíleného úložiště. 

Tento článek vám ukáže, jak vytvořit server NFS na virtuálním počítači s Ubuntu. A také udělte vašim kontejnerům AKS přístup k tomuto sdílenému systému souborů.

## <a name="before-you-begin"></a>Než začnete
V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Váš cluster AKS bude muset být živý ve stejných nebo partnerských virtuálních sítích jako server NFS. Cluster se musí vytvořit v existující virtuální síti, což může být stejná virtuální síť jako virtuální počítač.

Postup pro konfiguraci existující virtuální sítě je popsaný v dokumentaci: [Vytvoření clusteru AKS v existující virtuální][aks-virtual-network] [síti a propojení virtuálních sítí s partnerským vztahem VNet][peer-virtual-networks]

Také předpokládá, že jste vytvořili virtuální počítač Ubuntu Linux (například 18,04 LTS). Nastavení a velikost můžou být míru a můžou být nasazeny prostřednictvím Azure. Informace o rychlém startu pro Linux najdete v tématu [Správa virtuálních počítačů Linux][linux-create].

Pokud nasadíte svůj cluster AKS jako první, Azure při nasazení Ubuntu počítače automaticky vyplní pole virtuální síť, takže budou živá v rámci stejné virtuální sítě. Pokud ale chcete pracovat s partnerskými sítěmi, Projděte si dokumentaci výše.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Nasazení serveru NFS na virtuální počítač
Tady je skript pro nastavení serveru NFS na vašem virtuálním počítači s Ubuntu:
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
Server se restartuje (kvůli skriptu) a server pro systém souborů NFS můžete připojit k AKS.

>[!IMPORTANT]  
>Nezapomeňte nahradit **AKS_SUBNET** správným z vašeho clusteru nebo jinak "*" otevřete Server NFS na všechny porty a připojení.

Po vytvoření virtuálního počítače zkopírujte tento skript do souboru. Pak ho můžete přesunout z místního počítače nebo kdekoli do virtuálního počítače pomocí: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Jakmile je váš virtuální počítač ve vašem virtuálním počítači, můžete k němu použít SSH a spustit ho pomocí příkazu:
```console
sudo ./nfs-server-setup.sh
```
Pokud se jeho spuštění nepovede kvůli chybě odepření oprávnění, nastavte oprávnění ke spouštění prostřednictvím příkazu:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Připojení clusteru AKS k serveru NFS
Server systému souborů NFS můžeme připojit k našemu clusteru zřízením trvalého svazku a deklarace identity trvalého svazku, který určuje, jak se má přistoupit ke svazku.

Připojení ke dvěma službám ve stejných nebo partnerských virtuálních sítích je nezbytné. Pokyny pro nastavení clusteru ve stejné virtuální síti najdete tady: [Vytvoření clusteru AKS v existující virtuální][aks-virtual-network] síti.

Až budou ve stejné virtuální síti (nebo partnerském vztahu), musíte zřídit trvalý svazek a trvalou deklaraci identity na trvalém svazku v clusteru AKS. Kontejnery pak mohou připojit jednotku NFS do svého místního adresáře.

Tady je příklad definice Kubernetes trvalého svazku (Tato definice předpokládá, že váš cluster a virtuální počítač jsou ve stejné virtuální síti):

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
Nahraďte **NFS_INTERNAL_IP**, **NFS_NAME** a **NFS_EXPORT_FILE_PATH** informace o serveru NFS.

Budete také potřebovat soubor deklarace identity trvalého svazku. Tady je příklad toho, co se má zahrnout:

>[!IMPORTANT]  
>**"storageClassName"** musí zůstat v prázdném řetězci, jinak deklarace nebude fungovat.

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
Pokud se nemůžete připojit k serveru z clusteru, může být problém exportovaným adresářem nebo jeho nadřazeným adresářem, nemá dostatečná oprávnění pro přístup k serveru.

Ověřte, zda má váš adresář exportu i jeho nadřazený adresář oprávnění 777.

Oprávnění můžete ověřit spuštěním následujícího příkazu a adresáře by měly mít oprávnění *"drwxrwxrwx"* :
```console
ls -l
```

## <a name="more-information"></a>Další informace
Úplný návod nebo pomoc při ladění nastavení serveru pro systém souborů NFS najdete v podrobném kurzu:
  - [Kurz NFS][nfs-tutorial]

## <a name="next-steps"></a>Další kroky

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
