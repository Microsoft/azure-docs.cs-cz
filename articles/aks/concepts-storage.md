---
title: Koncepty – úložiště ve službách Azure Kubernetes Services (AKS)
description: Další informace o úložišti ve službě Azure Kubernetes Service (AKS), včetně svazků, trvalých svazků, tříd úložiště a deklarací identity
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4bb19d7da971a82aef9c0e1fc092cc648ac49c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595990"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Možnosti úložiště pro aplikace ve službě Azure Kubernetes Service (AKS)

Aplikace, které běží ve službě Azure Kubernetes Service (AKS), může být nutné ukládat a načítat data. Pro některé úlohy aplikace toto úložiště dat můžete použít místní, rychlé úložiště na uzlu, který již není potřeba při odstranění podů. Jiné úlohy aplikací může vyžadovat úložiště, které přetrvává na pravidelnějších datových svazcích v rámci platformy Azure. Více podů může být nutné sdílet stejné datové svazky nebo znovu připojit datové svazky, pokud je pod přeplánována na jiném uzlu. Nakonec budete muset vložit citlivá data nebo informace o konfiguraci aplikace do podů.

![Možnosti úložiště pro aplikace v clusteru Služeb Azure Kubernetes (AKS)](media/concepts-storage/aks-storage-options.png)

Tento článek představuje základní koncepty, které poskytují úložiště pro vaše aplikace v AKS:

- [Svazky](#volumes)
- [Trvalé svazky](#persistent-volumes)
- [Třídy úložiště](#storage-classes)
- [Deklarace identit trvalých svazků](#persistent-volume-claims)

## <a name="volumes"></a>Svazky

Aplikace často potřebují být schopni ukládat a načítat data. Vzhledem k tomu, že Kubernetes obvykle zachází s jednotlivými pody jako s dočasnými, disponibilními prostředky, jsou pro aplikace k dispozici různé přístupy k použití a uchovávání dat podle potřeby. *Svazek* představuje způsob, jak ukládat, načítat a uchovávat data napříč pody a prostřednictvím životního cyklu aplikace.

Tradiční svazky pro ukládání a načítání dat se vytvářejí jako prostředky Kubernetes podporované službou Azure Storage. Tyto datové svazky můžete ručně vytvořit tak, aby byly přiřazeny k podům přímo, nebo je kubernetes automaticky vytvoří. Tyto datové svazky můžou používat Disky Azure nebo Soubory Azure:

- *Disky Azure* lze použít k vytvoření prostředku Kubernetes *DataDisk.* Disky můžou používat úložiště Azure Premium, podporované vysoce výkonnými ssd disky nebo úložištěm Azure Standard, podporované běžnými pevnými disky. Pro většinu produkčních a vývojových úloh použijte úložiště Premium. Disky Azure jsou připojené jako *ReadWriteOnce*, takže jsou k dispozici pouze pro jeden pod. Pro svazky úložiště, ke kterým má přístup více podů současně, použijte Soubory Azure.
- *Soubory Azure* se můžou použít k připojení sdílené složky SMB 3.0 podporované účtem Azure Storage do podů. Soubory umožňují sdílet data mezi více uzly a pody. Soubory můžou používat úložiště Azure Standard podporované běžnými pevnými disky nebo úložištěm Azure Premium, které je podpořeno vysoce výkonnými disky SSD.
> [!NOTE] 
> Soubory Azure podporují úložiště úrovně Premium v clusterech AKS, které běží kubernetes 1.13 nebo vyšší.

V Kubernetes mohou svazky představovat více než jen tradiční disk, kde mohou být informace uloženy a načteny. Svazky Kubernetes lze také použít jako způsob, jak vložit data do podu pro použití v kontejnerech. Běžné další typy svazků v Kubernetes patří:

- *emptyDir* - Tento svazek se běžně používá jako dočasné místo pro pod. Všechny kontejnery v rámci pod udály data na svazku. Data zapsaná do tohoto typu svazku přetrvávají pouze po dobu životnosti podu - při odstranění podu je svazek odstraněn. Tento svazek obvykle používá úložiště disků místního uzlu, i když může existovat pouze v paměti uzlu.
- *tajný klíč* – Tento svazek se používá k vkládání citlivých dat do podů, jako jsou hesla. Nejprve vytvoříte tajný klíč pomocí rozhraní API Kubernetes. Při definování pod u. nebo nasazení, konkrétní tajný klíč lze požadovat. Tajné klíče jsou k dispozici pouze uzly, které mají naplánované pod, který to vyžaduje, a tajný klíč je uložen v *tmpfs*, není zapsán na disk. Při odstranění posledního podu v uzlu, který vyžaduje tajný klíč, je tajný klíč odstraněn z tmpfs uzlu. Tajné klíče jsou uloženy v rámci daného oboru názvů a lze přistupovat pouze pody ve stejném oboru názvů.
- *configMap* - Tento typ svazku se používá k vložení vlastností páru klíč-hodnota do podů, jako jsou informace o konfiguraci aplikace. Místo definování informací o konfiguraci aplikace v rámci image kontejneru, můžete definovat jako prostředek Kubernetes, který lze snadno aktualizovat a použít na nové instance podů, jak jsou nasazeny. Stejně jako pomocí tajné, nejprve vytvořit ConfigMap pomocí Kubernetes API. Tento ConfigMap pak lze požadovat při definování pod nebo nasazení. ConfigMaps jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze pody ve stejném oboru názvů.

## <a name="persistent-volumes"></a>Trvalé svazky

Svazky, které jsou definovány a vytvořeny jako součást životního cyklu podu existují pouze do odstranění podu. Pody často očekávají, že jejich úložiště zůstane, pokud pod je přeplánována na jiného hostitele během události údržby, zejména v StatefulSets. *Trvalý svazek* (PV) je prostředek úložiště vytvořený a spravovaný rozhraním API Kubernetes, který může existovat i po dobu životnosti jednotlivých podů.

Disky Azure nebo soubory se používají k poskytování PersistentVolume. Jak je uvedeno v předchozí části o svazcích, volba disků nebo souborů je často určena potřebou souběžného přístupu k datům nebo úrovni výkonu.

![Trvalé svazky v clusteru Služeb Azure Kubernetes (AKS)](media/concepts-storage/persistent-volumes.png)

Trvalý svazek může být *staticky* vytvořen správcem clusteru nebo *dynamicky* vytvořen serverem rozhraní API Kubernetes. Pokud pod je naplánováno a požaduje úložiště, které není aktuálně k dispozici, Kubernetes můžete vytvořit základní úložiště Azure Disk nebo soubory a připojit k podu. Dynamické zřizování používá *StorageClass* k určení, jaký typ úložiště Azure je potřeba vytvořit.

## <a name="storage-classes"></a>Třídy úložiště

Chcete-li definovat různé úrovně úložiště, jako je například Premium a Standard, můžete vytvořit *StorageClass*. StorageClass také definuje *reclaimPolicy*. Toto zásady rekultivace řídí chování základního prostředku úložiště Azure při odstranění podu a trvalý svazek již nemusí být vyžadován. Základní prostředek úložiště lze odstranit nebo zachovat pro použití s budoucí pod.

V AKS jsou vytvořeny dvě počáteční StorageClasses:

- *výchozí* – používá úložiště Azure Standard k vytvoření spravovaného disku. Zásady obnovení označuje, že základní disk Azure se odstraní při odstranění trvalého svazku, který jej použil.
- *spravované premium* – používá úložiště Azure Premium k vytvoření spravovaného disku. Zásady obnovení znovu označuje, že základní disk Azure se odstraní při odstranění trvalého svazku, který jej použil.

Pokud pro trvalý svazek není zadán žádný storageclass, použije se výchozí třída StorageClass. Při požadování trvalých svazků dbát na to, aby používaly odpovídající úložiště, které potřebujete. Můžete vytvořit StorageClass pro další `kubectl`potřeby pomocí . Následující příklad používá spravované disky Premium a určuje, že základní disk Azure by měl být *zachován* při odstranění podu:

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

## <a name="persistent-volume-claims"></a>Deklarace identit trvalých svazků

A PersistentVolumeClaim požaduje disk nebo soubor úložiště určité StorageClass, režim přístupu a velikost. Kubernetes API server můžete dynamicky zřídit základní prostředek úložiště v Azure, pokud neexistuje žádný existující prostředek ke splnění deklarace na základě definované StorageClass. Definice podu zahrnuje připojení svazku po připojení svazku k podu.

![Trvalé deklarace svazku v clusteru Služeb Azure Kubernetes (AKS)](media/concepts-storage/persistent-volume-claims.png)

A PersistentVolume je *vázán* na PersistentVolumeClaim, jakmile byl dostupný prostředek úložiště přiřazen podu, který o něj požádal. Existuje mapování trvalých svazků 1:1 na deklarace identity.

Následující příklad manifestu YAML ukazuje trvalé deklarace svazku, která používá *storageclass spravované ho spravovaného premium* a požaduje velikost disku *5Gi:*

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

Při vytváření definice podu je určena trvalá deklarace svazku, která požaduje požadované úložiště. Můžete také zadat *volumeMount* pro vaše aplikace ke čtení a zápisu dat. Následující příklad manifestu YAML ukazuje, jak lze použít předchozí deklaraci trvalého svazku k připojení svazku na */mnt/azure*:

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

## <a name="next-steps"></a>Další kroky

Doporučené postupy najdete v [tématu Doporučené postupy pro ukládání a zálohování v AKS][operator-best-practices-storage].

Postup v článku najdete v následujících článcích, jak vytvářet dynamické a statické svazky, které používají disky Azure nebo soubory Azure:

- [Vytvoření statického svazku pomocí disků Azure Disky][aks-static-disks]
- [Vytvoření statického svazku pomocí souborů Azure][aks-static-files]
- [Vytvoření dynamického svazku pomocí disků Azure Disky][aks-dynamic-disks]
- [Vytvoření dynamického svazku pomocí souborů Azure][aks-dynamic-files]

Další informace o základních konceptech Kubernetes a AKS naleznete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS identita][aks-concepts-identity]
- [Kubernetes / AKS zabezpečení][aks-concepts-security]
- [Kubernetes / AKS virtuální sítě][aks-concepts-network]
- [Kubernetes / AKS váha][aks-concepts-scale]

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
