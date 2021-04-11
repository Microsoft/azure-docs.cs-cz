---
title: Koncepty – úložiště v Azure Kubernetes Services (AKS)
description: Seznamte se s úložištěm ve službě Azure Kubernetes (AKS), včetně svazků, trvalých svazků, tříd úložišť a deklarací identity.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105778"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Možnosti úložiště pro aplikace ve službě Azure Kubernetes (AKS)

Aplikace běžící ve službě Azure Kubernetes Service (AKS) můžou potřebovat ukládat a načítat data. I když některé úlohy aplikací můžou používat místní, rychlé úložiště na nepotřebných, vyprázdných uzlech, jiné vyžadují úložiště, které na více běžných datových svazcích v rámci platformy Azure přetrvává. 

Několik lusků může vyžadovat:
* Sdílejte stejné datové svazky. 
* Znovu připojit datové svazky, je-li uzel pod přeplánován na jiném uzlu. 

Nakonec možná budete muset do lusků vložit citlivé informace o konfiguraci dat nebo aplikací.

V tomto článku se seznámíte se základními koncepcemi, které poskytují úložiště vašim aplikacím v AKS:

- [Svazky](#volumes)
- [Trvalé svazky](#persistent-volumes)
- [Třídy úložiště](#storage-classes)
- [Deklarace identit trvalých svazků](#persistent-volume-claims)

![Možnosti úložiště pro aplikace v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Svazky

Kubernetes obvykle zpracovává jednotlivé lusky jako dočasné a nevhodné prostředky. Aplikace mají k dispozici různé přístupy pro používání a zachování dat. *Svazek* představuje způsob, jak ukládat, načítat a uchovávat data v různých luskech a v životním cyklu aplikace.

Tradiční svazky jsou vytvořeny jako Kubernetes prostředky, které jsou zajištěny pomocí Azure Storage. Můžete ručně vytvořit datové svazky, které mají být přiřazeny k luskům přímo, nebo je nechat Kubernetes automaticky vytvořit. Datové svazky můžou používat disky Azure nebo soubory Azure.

### <a name="azure-disks"></a>Disky Azure

K vytvoření prostředku Kubernetes *datadisk* použijte *disky Azure* . Disky můžou používat:
* Azure Premium Storage, zajištěné vysokým výkonem SSD nebo 
* Azure Storage úrovně Standard, zajištěný pravidelným HDDM. 

> [!TIP]
>Pro většinu produkčních a vývojových úloh použijte Premium Storage. 

Vzhledem k tomu, že jsou disky Azure připojené jako *ReadWriteOnce*, jsou dostupné jenom pro jeden pod. Pro svazky úložiště, ke kterým se dá současně přistupovat více lusků, použijte soubory Azure.

### <a name="azure-files"></a>Azure Files
Pomocí služby *soubory Azure* připojte sdílenou složku SMB 3,0 zálohovanou účtem Azure Storage do lusků. Soubory umožňují sdílet data napříč několika uzly a lusky a můžou používat:
* Azure Premium Storage, zajištěné vysokým výkonem SSD nebo 
* Služba Azure Storage úrovně Standard zálohovaná pravidelným HDDM.

### <a name="volume-types"></a>Typy svazků
Kubernetes svazky reprezentují více než jenom tradiční disk pro ukládání a načítání informací. Svazky Kubernetes lze také použít jako způsob, jak vložit data do podseznamu pro použití kontejnery. 

Mezi běžné typy svazků v Kubernetes patří:

#### <a name="emptydir"></a>emptyDir

Běžně se používá jako dočasné místo pro pod. Všechny kontejnery v rámci pod mohou přistupovat k datům na svazku. Data zapsaná do tohoto typu svazku přetrvávají pouze za životnosti pod. Po odstranění pod ním se svazek odstraní. Tento svazek obvykle používá základní úložiště disku místního uzlu, i když může existovat i v paměti uzlu.

#### <a name="secret"></a>Tajný kód

Pomocí *tajných* svazků můžete vkládat citlivé údaje do lusků, jako jsou hesla. 
1. Vytvořte tajný klíč pomocí rozhraní Kubernetes API. 
1. Definice nebo nasazení a vyžádání konkrétního tajného klíče. 
    * Tajné kódy se poskytují pouze uzlům s naplánovaným uzlem, který je vyžaduje.
    * Tajný kód je uložený v *tmpfs* a nezapisuje na disk. 
1. Když odstraníte poslední uzel pod uzlem, který vyžaduje tajný klíč, bude tajný kód odstraněn z tmpfs uzlu. 
   * Tajné kódy jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze v rámci stejného oboru názvů.

#### <a name="configmap"></a>configMap

*ConfigMap* můžete použít k vložení vlastností páru klíč-hodnota do lusků, jako jsou například informace o konfiguraci aplikace. Definujte informace o konfiguraci aplikace jako prostředek Kubernetes, snadno se aktualizují a aplikují na nové instance lusků při jejich nasazení. 

Například použití tajného klíče:
1. Vytvořte ConfigMap pomocí rozhraní Kubernetes API. 
1. Vyžádejte ConfigMap při definování pod nebo nasazováním. 
   * ConfigMaps jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze v rámci stejného oboru názvů.

## <a name="persistent-volumes"></a>Trvalé svazky

Svazky, které jsou definovány a vytvořeny jako součást životního cyklu pod, existují pouze v případě, že odstraníte pole pod. Lusky často očekávají, že jejich úložiště zůstane v případě, že je v rámci události údržby přeplánováno na jiném hostiteli, zejména v StatefulSets. *Trvalý svazek* (PV) je prostředek úložiště vytvořený a spravovaný rozhraním API Kubernetes, které může existovat po dobu života jednotlivce pod.

K poskytnutí PersistentVolume můžete použít disky nebo soubory Azure. Jak je uvedeno v části [svazky](#volumes) , je volba disků nebo souborů často určena nutností souběžného přístupu k datům nebo úrovni výkonu.

![Trvalé svazky v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

PersistentVolume je možné *staticky* vytvořit správcem clusteru nebo *dynamicky* vytvořit pomocí serveru Kubernetes API. Pokud je v seznamu naplánováno a požadavky jsou aktuálně nedostupné úložiště, Kubernetes může vytvořit základní disk Azure nebo soubory úložiště a připojit ho k poli pod. Dynamické zřizování používá *StorageClass* k identifikaci typu úložiště Azure, který je potřeba vytvořit.

## <a name="storage-classes"></a>Třídy úložiště

Pokud chcete definovat různé úrovně úložiště, jako je například Premium a Standard, můžete vytvořit *StorageClass*. 

StorageClass také definuje *reclaimPolicy*. Když odstraníte a trvalý svazek už není potřeba, reclaimPolicy řídí chování základního prostředku úložiště Azure. Základní prostředek úložiště můžete buď odstranit, nebo uchovat pro použití s budoucím pod.

V AKS se `StorageClasses` pro cluster s použitím modulů plug-in-treech modulů úložiště vytvoří čtyři iniciály:

| Oprávnění | Důvod |
|---|---|
| `default` | K vytvoření spravovaného disku používá Azure StandardSSD Storage. Zásady opětovné deklarace zajistí, že se při odstranění trvalého svazku, který ho použil, odstraní základní disk Azure. |
| `managed-premium` | Používá službu Azure Premium Storage k vytvoření spravovaného disku. Zásady opětovné deklarace zajistí, že se při odstranění trvalého svazku, který ho použil, odstraní základní disk Azure. |
| `azurefile` | K vytvoření sdílené složky Azure používá službu Azure Storage úrovně Standard. Zásady opětovné deklarace zajistí, že základní sdílená složka Azure se odstraní při odstranění trvalého svazku, který ho použil. |
| `azurefile-premium` | Pomocí služby Azure Premium Storage vytvoří sdílenou složku Azure. Zásady opětovné deklarace zajistí, že základní sdílená složka Azure se odstraní při odstranění trvalého svazku, který ho použil.|

V případě clusterů využívajících nové externí moduly plug-in (ve verzi Preview) `StorageClasses` se vytvoří následující extra:

| Oprávnění | Důvod |
|---|---|
| `managed-csi` | K vytvoření spravovaného disku používá Azure StandardSSD místně redundantní úložiště (LRS). Zásady opětovné deklarace zajistí, že se při odstranění trvalého svazku, který ho použil, odstraní základní disk Azure. Třída úložiště také nakonfiguruje trvalé svazky tak, aby se rozšířily, stačí upravit deklaraci trvalého svazku s novou velikostí. |
| `managed-csi-premium` | K vytvoření spravovaného disku používá Azure Premium místně redundantní úložiště (LRS). Zásady opětovné deklarace zajistí, že se při odstranění trvalého svazku, který ho použil, odstraní základní disk Azure. Podobně tato třída úložiště umožňuje rozšířit trvalé svazky. |
| `azurefile-csi` | K vytvoření sdílené složky Azure používá službu Azure Storage úrovně Standard. Zásady opětovné deklarace zajistí, že základní sdílená složka Azure se odstraní při odstranění trvalého svazku, který ho použil. |
| `azurefile-csi-premium` | Pomocí služby Azure Premium Storage vytvoří sdílenou složku Azure. Zásady opětovné deklarace zajistí, že základní sdílená složka Azure se odstraní při odstranění trvalého svazku, který ho použil.|

Pokud nezadáte StorageClass pro trvalý svazek, použije se výchozí StorageClass. Zajistěte, aby svazky používaly vhodné úložiště, které potřebujete při vyžadování trvalých svazků. 

StorageClass můžete vytvořit pro další potřeby pomocí `kubectl` . Následující příklad používá prémiové Managed Disks a určuje, že by se měl příslušný disk Azure při odstranění pod tím *zachovat* :

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS sjednotí výchozí třídy úložiště a přepíše všechny změny, které provedete v těchto třídách úložiště.

## <a name="persistent-volume-claims"></a>Deklarace identit trvalých svazků

PersistentVolumeClaim vyžádá buď disk nebo úložiště souborů konkrétního StorageClass, režim přístupu a velikost. Server Kubernetes API může dynamicky zřizovat základní prostředky úložiště Azure, pokud žádný existující prostředek nemůže splnit deklaraci identity na základě definovaného StorageClass. 

Definice pod zahrnuje připojení svazku, jakmile je svazek připojen k poli pod.

![Deklarace trvalých svazků v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Po přiřazení dostupného prostředku úložiště k objektu pod, který požaduje úložiště, je PersistentVolume *vázán* na PersistentVolumeClaim. Trvalé svazky jsou 1:1 namapované na deklarace identity.

V následujícím příkladu manifestu YAML se zobrazuje deklarace identity trvalého svazku, která používá StorageClassu *Managed-Premium* a který požaduje velikost disku *5Gi* :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Při vytváření definice pod můžete zadat také:
* Deklarace identity trvalého svazku pro vyžádání požadovaného úložiště 
* *VolumeMount* pro vaše aplikace pro čtení a zápis dat. 

V následujícím příkladu manifestu YAML se dozvíte, jak se dá k připojení svazku na */mnt/Azure* použít předchozí deklarace identity trvalého svazku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Pro připojení svazku v kontejneru Windows zadejte písmeno jednotky a cestu. Například:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Další kroky

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

Další informace o tom, jak vytvořit dynamické a statické svazky, které používají disky Azure nebo soubory Azure, najdete v následujících článcích s postupy:

- [Vytvoření statického svazku pomocí disků Azure][aks-static-disks]
- [Vytvoření statického svazku pomocí služby soubory Azure][aks-static-files]
- [Vytvoření dynamického svazku pomocí disků Azure][aks-dynamic-disks]
- [Vytvoření dynamického svazku pomocí služby soubory Azure][aks-dynamic-files]

Další informace o základních konceptech Kubernetes a AKS najdete v následujících článcích:

- [Clustery a úlohy Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identita Kubernetes/AKS][aks-concepts-identity]
- [Zabezpečení Kubernetes/AKS][aks-concepts-security]
- [Virtuální sítě Kubernetes/AKS][aks-concepts-network]
- [Škálování Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
