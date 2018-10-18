---
title: Principy – služeb úložiště v Azure Kubernetes (AKS)
description: Další informace o ukládání ve službě Azure Kubernetes Service (AKS), včetně svazky, trvalé, třídy úložiště a deklarace identity
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: c3a737bdd9978e6cb02e3e8b7a34407eb1dd8fb6
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380958"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Možnosti úložiště pro aplikace ve službě Azure Kubernetes Service (AKS)

Aplikace, které běží ve službě Azure Kubernetes Service (AKS) může potřebovat ukládat a načítat data. Pro některé úlohy aplikace můžete použít toto úložiště dat místní rychlé úložiště na uzel, který je už je nepotřebujete při odstranění tyto pody. Další úlohy aplikace mohou vyžadovat úložiště, který bude zachován v pravidelných data svazky v rámci platformy Azure. Několik podů se možná muset sdílet stejné datové svazky, nebo znovu připojit datové svazky, pokud chcete pod přeplánování na jiném uzlu. Nakonec budete muset vložit citlivé údaje nebo informace o konfiguraci aplikace do pody.

![Možnosti úložiště pro aplikace v clusteru služby Kubernetes v Azure (AKS)](media/concepts-storage/aks-storage-options.png)

Tento článek představuje základní koncepty, které poskytují úložiště pro vaše aplikace ve službě AKS:

- [Svazky](#volumes)
- [Trvalé svazky](#persistent-volumes)
- [Třídy úložiště](#storage-classes)
- [Deklarace identity trvalý svazek](#persistent-volume-claims)

## <a name="volumes"></a>Svazky

Aplikace je často potřeba mít možnost ukládat a načítat data. Jednotlivé podů Kubernetes obvykle zpracovává jako dočasné, uvolnitelné prostředky, různé přístupy jsou k dispozici pro použití aplikací a zachovat data podle potřeby. A *svazku* představuje způsob, jak uložení, načtení a uložení dat mezi pody a v životním cyklu aplikace.

Tradiční svazky k ukládání a načítání dat jsou vytvořeny jako prostředky Kubernetesu se opírá o Azure Storage. Můžete ručně vytvořit tyto datové svazky pro přiřazení podů přímo nebo mají vytvořit automaticky Kubernetes. Tyto datové svazky můžete použít Azure Disks nebo soubory Azure:

- *Disky Azure* slouží k vytvoření Kubernetes *DataDisk* prostředků. Disky můžete použít Azure Premium storage, založená na jednotkách SSD pro vysoce výkonné, nebo Azure storage úrovně Standard, se opírá o pravidelných pevných disků. Pro většinu produkčních a úlohy související s vývojem používejte Premium storage. Disky Azure jsou připojené jako *ReadWriteOnce*, takže jsou k dispozici pouze na jeden uzel. Pro svazky úložiště, kterým je možný přístup pomocí několika uzlů najednou použijte soubory Azure.
- *Služba soubory Azure* slouží k připojení sdílené složky protokolu SMB 3.0 se opírá o účtu služby Azure Storage na tyto pody. Soubory umožňují sdílet data mezi několika uzly a tyto pody. V současné době souborů lze použít pouze Azure storage úrovně Standard se opírá o pravidelných pevných disků.

V systému Kubernetes můžete svazky představují víc než jenom tradiční disk kde můžete ukládat a načíst informace. Svazky Kubernetes může také sloužit jako způsob, jak vložit data do pod pro použití podle kontejnerů. Obvyklé typy další svazek v Kubernetes zahrnují:

- *emptyDir* -tento svazek se běžně používá jako dočasné prostor pro pod. Všechny kontejnery v rámci podu můžete přístup k datům na svazku. Data zapsaná do tohoto svazku typu potrvá jenom pro životnost pod – při odstranění pod svazku se odstraní. Tento svazek obvykle používá základní místní uzel wsfc úložný prostor na disku, ale mohou také existovat pouze v paměti uzlu.
- *tajný kód* -tento svazek slouží k vložení do podů, jako jsou hesla citlivá data. Nejprve vytvoříte tajného kódu pomocí rozhraní API Kubernetes. Při definování pod nebo nasazení mohou být požadována určitého tajného klíče. Tajné kódy jsou poskytovány pouze pro uzly, které mají naplánované pod, kterého je vyžadováno, a tajný kód je uložen v *tmpfs*není napsaných na disk. Při odstranění posledního podu na uzel, který se vyžaduje tajný kód. tajný kód je odstraněn z tmpfs uzlu. Tajné klíče jsou uložené v daném oboru názvů a je přístupný pouze pomocí podů v rámci stejného oboru názvů.
- *configMap* – tento typ svazku se používá k vložení páru klíč hodnota vlastnosti do podů, jako je například informace o konfiguraci aplikace. Místo definování informace o konfiguraci aplikace v rámci image kontejneru, můžete ji definovat jako prostředek Kubernetes, který lze snadno aktualizovat a použít u nových instancí podů, protože jsou nasazené. Podobně jako pomocí tajného klíče, nejprve vytvoříte ConfigMap pomocí rozhraní API Kubernetes. Tato ConfigMap pak být vyžádány při definování pod nebo nasazení. ConfigMaps jsou uloženy v daném oboru názvů a je přístupný pouze pomocí podů v rámci stejného oboru názvů.

## <a name="persistent-volumes"></a>Trvalé svazky

Svazky jsou definovány a vytvořen jako součást životního cyklu pod existovat pouze dokud je neodstraníte pod. Podů často očekávat, že jejich úložiště zůstat, pokud pod během údržby, zejména v StatefulSets přeplánování na jiného hostitele. A *trvalý svazek* (PV) je úložiště prostředek vytvořen a spravován společností, která mohou existovat nad rámec životnost jednotlivých pod rozhraní Kubernetes API.

Azure disky nebo soubory se používají k zajištění PersistentVolume. Jak je uvedeno v předchozí části na svazcích, je potřeba souběžný přístup k data nebo úroveň výkonu často určena výběr disků nebo soubory.

![Trvalé svazků v clusteru služby Kubernetes v Azure (AKS)](media/concepts-storage/persistent-volumes.png)

Může být PersistentVolume *staticky* vytvořil správce clusteru, nebo *dynamicky* vytvořené na serveru Kubernetes API. Pokud pod je naplánováno a požadavky, které není aktuálně k dispozici, Kubernetes můžete vytvořit základního úložiště Azure Disk nebo soubory a připojte ji pod. Dynamické zřizování používá *StorageClass* k určení, jaký typ úložiště Azure je potřeba vytvořit.

## <a name="storage-classes"></a>Třídy úložiště

Chcete-li definovat různé vrstvy úložiště, například Premium a Standard, můžete vytvořit *StorageClass*. Definuje také StorageClass *reclaimPolicy*. Tato reclaimPolicy řídí chování základní prostředek služby Azure storage při pod se odstraní a trvalý svazek již může být nutná. Tento základní prostředek spravovat úložiště můžete odstranit nebo uchovávají pro použití s budoucí pod.

Ve službě AKS jsou vytvořeny dva počáteční StorageClasses:

- *výchozí* -využívá Azure storage úrovně Standard k vytvoření spravovaného disku. Zásady reclaim označuje, že základní Disk Azure se odstraní při odstranění podů, které ho používají.
- *spravované premium* -využívá Azure Premium storage k vytvoření spravovaného disku. Zásady reclaim znovu označuje, že základní Disk Azure se odstraní při odstranění podů, které ho používají.

Pokud pro trvalý svazek není zadán žádný StorageClass, použije se výchozí StorageClass. Je třeba dbát při žádosti o trvalé svazků tak, že používají příslušné úložiště, které potřebujete. Můžete vytvořit StorageClass pro další potřeby pomocí `kubectl`. Následující příklad používá Managed Disks úrovně Premium a určuje, že by měla být základní Disk Azure *uchovávají* při odstranění pod:

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

## <a name="persistent-volume-claims"></a>Deklarace identity trvalý svazek

PersistentVolumeClaim požádá o disku nebo souboru úložiště pro konkrétní StorageClass, režim přístupu a velikosti. Na serveru Kubernetes API můžete dynamicky zajišťují základní prostředek úložiště v Azure, pokud není žádný existující prostředek ke splnění deklaraci identity založenou na definované StorageClass. Definice pod zahrnuje připojení svazku, jakmile byl svazek připojen k pod.

![Trvalý svazek deklarací identity v clusteru služby Kubernetes v Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Je PersistentVolume *vázán* k PersistentVolumeClaim, jakmile byl přiřazen k prostředku úložiště k dispozici pod o to požádá. Existuje mapování 1:1 trvalé svazků na nároky.

Manifest YAML následující příklad ukazuje deklaraci trvalý svazek, který používá *spravované premium* StorageClass požadavků na Disk a *5Gi* velikost:

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

Při vytváření definice pod deklarace identity trvalý svazek je zadán požádat o požadované úložiště. Můžete také zadejte *volumeMount* pro vaše aplikace číst a zapisovat data. Manifest YAML následující příklad ukazuje, jak lze připojit svazek na předchozí deklarace identity trvalý svazek */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Další postup

Jak vytvořit statické a dynamické svazky, které používají disky Azure nebo službě soubory Azure najdete v tématu některého z těchto článků:

- [Vytvoření statické svazku pomocí Azure Disks][aks-static-disks]
- [Vytvoření statické svazku používat soubory Azure][aks-static-files]
- [Vytvořit svazek dynamické disky Azure][aks-dynamic-disks]
- [Vytvoření dynamické svazku používat soubory Azure][aks-dynamic-files]

Další informace o základní Kubernetes a AKS koncepty najdete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / identitě AKS][aks-concepts-identity]
- [Kubernetes / zabezpečení AKS][aks-concepts-security]
- [Kubernetes / virtuální sítě AKS][aks-concepts-network]
- [Kubernetes snížit nebo navýšit AKS][aks-concepts-scale]

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
