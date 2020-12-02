---
title: Principy správy úložiště Kubernetes na zařízení Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak probíhá Správa úložiště Kubernetes na zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: alkohli
ms.openlocfilehash: 34165071238ca3edf78ab9cca43639c23ce5ed2a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448696"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Správa úložiště Kubernetes na zařízení GPU Azure Stack Edge pro

Na zařízení Azure Stack Edge pro se vytvoří cluster Kubernetes při konfiguraci výpočetní role. Po vytvoření clusteru Kubernetes je možné kontejnery aplikace nasadit do clusteru Kubernetes v části lusky. Existují různé způsoby, jak poskytnout úložiště pro lusky v clusteru Kubernetes. 

Tento článek popisuje metody pro zřízení úložiště v clusteru Kubernetes obecně a konkrétně v kontextu zařízení Azure Stack Edge pro. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Požadavky na úložiště pro Kubernetes lusky

Kubernetes lusky jsou bezstavové, ale spuštěné aplikace jsou obvykle stavové. Vzhledem k tomu, že lusky mohou být krátkodobé a jejich restartování, převzetí služeb při selhání nebo přesun mezi uzly Kubernetes, musí být splněny následující požadavky pro úložiště přidružené k uzlu pod. 

Úložiště musí:

- Živá mimo rozhraní pod.
- Být nezávisle na životním cyklu pod.
- Být přístupné ze všech uzlů Kubernetes.

Pro pochopení, jak je úložiště spravované pro Kubernetes, je potřeba pochopit dva prostředky rozhraní API: 

- **PersistentVolume (PV)**: Jedná se o část úložiště v clusteru Kubernetes. Úložiště Kubernetes se dá staticky zřídit jako `PersistentVolume` . Dá se taky dynamicky zřídit jako  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: Jedná se o požadavek na úložiště uživatele. Trvalé virtuální okruhy využívají prostředky PV. Virtuální okruhy můžou požadovat konkrétní velikost a režimy přístupu. 

    Vzhledem k tomu, že uživatelé potřebují s různou `PersistentVolumes` vlastností pro různé problémy, je třeba, aby správci clusterů museli nabízet celou řadu různých `PersistentVolumes` způsobů, než je jenom režim velikosti a přístupu. Pro tyto potřeby potřebujete `StorageClass` prostředek.

Zřizování úložiště může být statické nebo dynamické. Jednotlivé typy zřizování jsou popsány v následujících částech.

## <a name="static-provisioning"></a>Statické zřizování

Správci clusteru Kubernetes můžou staticky zřídit úložiště. V takovém případě můžou použít back-end úložiště založené na systému souborů SMB/NFS nebo používat disky iSCSI připojené místně přes síť v místním prostředí nebo dokonce používat soubory Azure nebo disky Azure v cloudu. Tento typ úložiště se ve výchozím nastavení nezřizuje a Správci clusterů musí plánování a správu tohoto zřizování naplánovat. 
 
Tady je diagram, který znázorňuje, jak se v Kubernetes spotřebovávají staticky zřízené úložiště: 

![Statické zřizování prostřednictvím PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Dojde k následujícím krokům: 

1. **Zřízení úložiště**: Správce clusteru zřídí úložiště. V tomto příkladu správce clusteru vytvoří jednu nebo více sdílených složek SMB, které automaticky vytvoří trvalé objekty svazků v clusteru Kubernetes odpovídající těmto sdíleným složkám. 

1. **Úložiště deklarací identity**: odešlete nasazení virtuálního okruhu, které požaduje úložiště. Tato deklarace identity pro úložiště je PersistentVolumeClaim (PVC). Pokud se velikost a režim přístupu PV shodují s virtuálním okruhem (PVC), pak je trvalý virtuální okruh vázaný na souč_hod. Virtuální okruh (PVC) a PV jsou propojeni.

1. **Připojit PVC k kontejneru**: Jakmile je trvalý virtuální okruh VÁZANÝ na souč_hod, můžete připojit tento virtuální okruh k cestě ve vašem kontejneru. Když logika aplikace v kontejneru přečte/zapisuje z nebo do této cesty, data se zapisují do úložiště SMB.
 

## <a name="dynamic-provisioning"></a>Dynamické zřizování

Tady je diagram, který znázorňuje, jak se v Kubernetes spotřebovávají staticky zřízené úložiště: 

![Dynamické zřizování prostřednictvím StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Dojde k následujícím krokům: 

1. **Definování třídy úložiště**: Správce clusteru definuje třídu úložiště v závislosti na operačním prostředí pro cluster Kubernetes. Správce clusteru také nasadí zřizovacího, který je v clusteru Kubernetes nasazený pod jiným nebo nasazeným systémem. K zajištění dynamického zřizování sdílených složek zřídí zřídí všechny podrobnosti.  

1. **Úložiště deklarací**: odešlete aplikaci, která by vyvolala úložiště. Po vytvoření virtuálního okruhu s tímto odkazem na třídu úložiště se zřídí. 

1. **Úložiště se dynamicky zřídí**: zřizovací modul dynamicky vytvoří sdílenou složku přidruženou k úložišti místního disku. Po vytvoření sdílené složky se vytvoří také objekt SOUČHODNOTA, který je automaticky odpovídající této sdílené složce.

1. **Připojit virtuální okruh k kontejneru**: Jakmile je virtuální okruh VÁZANÝ na souč_hod, můžete k němu připojit trvalé připojení k kontejneru na cestě stejným způsobem jako statické zřizování a čtení nebo zápis do sdílené složky.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Zřizování úložiště na Azure Stack Edge pro

Na zařízení Azure Stack Edge pro se staticky zřízené `PersistentVolumes` vytvoří pomocí možností úložiště pro zařízení. Když zřídíte sdílenou složku a povolíte možnost **sdílet s Edge COMPUTE** , vytvoří tato akce v clusteru Kubernetes automaticky prostředek PV.

![Vytvoření místní sdílené složky v Azure Portal pro statické zřizování](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Pokud chcete použít vrstvení cloudu, můžete vytvořit sdílenou složku Edge s povoleným použitím možnosti sdílet s Edge Compute. Pro tuto sdílenou složku se automaticky vytvoří souč_hod. Všechna data aplikace, která zapisujete do sdílené složky Edge, jsou vrstvena do cloudu. 

![Vytvoření sdílené složky v cloudu v Azure Portal pro statické zřizování](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Sdílené složky SMB i NFS můžete vytvořit tak, aby staticky zřídily PVs pro zařízení s Azure Stack Edgem pro. Po zřízení PV odešlete virtuální okruh, který bude toto úložiště uplatňovat. Tady je příklad nasazení okruhu PVC `yaml` , který využívá úložiště a používá sdílené složky, které jste zřídili.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Další informace najdete v tématu [nasazení stavové aplikace prostřednictvím statického zřizování na Azure Stack Edge pro prostřednictvím kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Pro přístup ke stejnému staticky zřízenému úložišti jsou příslušné možnosti připojení svazku pro vazby úložiště pro IoT následující. `/home/input`Je cesta, ke které je svazek přístupný v rámci kontejneru.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge pro má taky Kubernetes s `StorageClass` názvem `ase-node-local` , který používá úložiště datového disku připojené k uzlu. To `StorageClass` podporuje dynamické zřizování. `StorageClass`V aplikacích pod můžete vytvořit odkaz a pro vás bude automaticky vytvořena souč_hod. Další informace najdete v tématu [Kubernetes na řídicím panelu](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) pro dotaz na `ase-node-local StorageClass` .

![Integrovaná třída úložiště v řídicím panelu Kubernetes](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Další informace najdete v tématu [nasazení stavové aplikace prostřednictvím dynamického zřizování na Azure Stack Edge pro přes kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Zvolit typ úložiště

V závislosti na zatížení, které nasazujete, možná budete muset zvolit typ úložiště. 

- Pokud chcete `ReadWriteMany` mít přístup k režimu `PersistentVolumes` , ve kterém jsou svazky připojené jako čtení i zápis pomocí mnoha uzlů nasazování, použijte pro sdílené složky SMB/NFS statické zřizování.

- Pokud aplikace, které nasazujete, mají požadavek na dodržování předpisů POSIX, například aplikace jako MongoDB, PostgreSQL, MySQL nebo Prometheus, používejte integrované StorageClass. Režimy přístupu jsou `ReadWriteOnce` nebo je svazek připojen jako čtení i zápis v jednom uzlu. 


Další informace o režimech přístupu najdete v tématu [režim přístupu k Kubernetes svazkům](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Další kroky

Informace o tom, jak můžete statisticky zřídit `PersistentVolume` , najdete v těchto tématech:

- [Nasaďte stavovou aplikaci prostřednictvím statického zřizování na Azure Stack Edge pro přes kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Další informace o tom, jak dynamicky zřizovat `StorageClass` , najdete tady:

- [Nasaďte stavovou aplikaci prostřednictvím dynamického zřizování na Azure Stack Edge pro přes kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
