---
title: Vytvoření serveru se systémem Ubuntu systému souborů NFS (Network File System) pro použití u podů Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek systému souborů NFS Ubuntu Linux Server pro použití s podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468496"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku systému souborů NFS (Network File System) Linux Server Azure Kubernetes Service (AKS)
Sdílení dat mezi kontejnery je často potřebné komponenty služeb založených na kontejnerech a aplikací. Obvykle mají různé podů, které potřebují přístup ke stejným informacím na externí trvalý svazek.    
Služba soubory Azure jsou možnosti, vytváření systému souborů NFS serveru na Virtuálním počítači Azure je jiná forma trvalé sdíleného úložiště. 

Tento článek vám ukáže jak vytvořit Server systému souborů NFS na virtuálním počítači s Ubuntu. A také zpřístupnit vaše AKS kontejnery pro tento sdílený systém souborů.

## <a name="before-you-begin"></a>Než začnete
Tento článek předpokládá, že máte existující Cluster AKS. Pokud potřebujete AKS Cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

To je stejný nebo partnerské virtuální sítě jako Server systému souborů NFS potřebovat clusteru AKS. Cluster musí být vytvořeny v existující virtuální síť, což může být stejné virtuální síti jako virtuální počítač.

Kroky pro konfiguraci se existující virtuální síť jsou popsány v dokumentaci: [vytvoření clusteru AKS v existující virtuální síť] [ aks-virtual-network] a [propojení virtuálních sítí pomocí VNET peering][peer-virtual-networks]

Dále předpokládá, že vytvoříte virtuální počítače s Ubuntu Linuxem (například 18.04 LTS). Nastavení velikosti může být podle vašich představ a můžou být nasazené prostřednictvím Azure. Rychlý start pro Linux, najdete v části [Správa virtuálních počítačů linux][linux-create].

Pokud nejdříve nasaďte AKS Cluster Azure se automaticky vyplní pole virtuální sítě při nasazování počítače se systémem Ubuntu, díky kterým jsou za provozu v rámci stejné virtuální síti. Ale pokud budete chtít pracovat s navázaným partnerským vztahem sítě místo toho najdete v dokumentaci výše.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Nasazení serveru systému souborů NFS na virtuální počítač
Tady je skript, který chcete nastavit Server systému souborů NFS v rámci virtuálního počítače se systémem Ubuntu:
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
Server se restartuje (z důvodu skript) a můžete připojit Server systému souborů NFS s AKS

>[!IMPORTANT]  
>Nezapomeňte nahradit **AKS_SUBNET** s varianty z vašeho clusteru nebo "*" otevře serveru systému souborů NFS na všech portech a připojení.

Po vytvoření virtuálního počítače, zkopírujte do souboru skriptu výše. Pak můžete přesunout z místního počítače nebo bez ohledu na to je skript do virtuálního počítače pomocí: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Jakmile skriptu ve virtuálním počítači, můžete ssh k virtuálnímu počítači a spouští úlohy prostřednictvím příkazu:
```console
sudo ./nfs-server-setup.sh
```
Pokud provádění selže z důvodu Chyba oprávnění, nastavte oprávnění pro provádění pomocí příkazu:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Připojení clusteru AKS na Server systému souborů NFS
Server systému souborů NFS můžete připojíme zřízením trvalý svazek a trvalý svazek deklarace identity, která určuje, jak získat přístup ke svazku do našeho clusteru.  
Propojení těchto dvou služeb ve stejné nebo partnerských virtuálních sítích je nezbytné. Pokyny k nastavení clusteru ve stejné virtuální síti jsou tady: [vytvoření clusteru AKS v existující virtuální síť][aks-virtual-network]

Jakmile jsou ve stejné virtuální síti (nebo v partnerském vztahu), budete muset zřízení trvalý svazek a trvalý svazek deklarace identity ve vašem clusteru AKS. Kontejnery můžete pak připojte jednotku systému souborů NFS pro jejich místní adresář.

Tady je definici příklad kubernetes pro trvalý svazek (Tato definice se předpokládá, že cluster a virtuálního počítače jsou ve stejné virtuální síti):

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
Nahraďte **NFS_INTERNAL_IP**, **NFS_NAME** a **NFS_EXPORT_FILE_PATH** informace serveru systému souborů NFS.

Budete také potřebovat soubor deklarací identity trvalý svazek. Tady je příklad toho, jak zahrnout:

>[!IMPORTANT]  
>**"storageClassName"** musí zůstat prázdný řetězec nebo deklarace identity nebude fungovat.

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
Pokud se nemůžete připojit k serveru z clusteru, problém může být exportovaného adresáře nebo jeho nadřazený objekt, nemá dostatečná oprávnění pro přístup k serveru.

Zkontrolujte, zda adresáři exportu a jeho nadřazené adresáře 777 oprávnění.

Spuštěním následujícího příkazu můžete zkontrolovat oprávnění a adresáře by měl mít *"drwxrwxrwx"* oprávnění:
```console
ls -l
```

## <a name="more-information"></a>Další informace
Chcete-li získat úplný návod nebo si můžete usnadnit ladění nastavení serveru systému souborů NFS, tady je podrobný kurz:
  - [Kurz systému souborů NFS][nfs-tutorial]

## <a name="next-steps"></a>Další postup

Přidružené osvědčené postupy, najdete v části [osvědčené postupy pro ukládání a zálohování ve službě AKS][operator-best-practices-storage].

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
