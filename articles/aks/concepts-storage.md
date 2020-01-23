---
title: Koncepty – úložiště v Azure Kubernetes Services (AKS)
description: Seznamte se s úložištěm ve službě Azure Kubernetes (AKS), včetně svazků, trvalých svazků, tříd úložišť a deklarací identity.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: d6c14f78fd3cefa5ec41a686ca385639eb3fcb67
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549270"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Možnosti úložiště pro aplikace ve službě Azure Kubernetes (AKS)

Aplikace, které běží ve službě Azure Kubernetes Service (AKS), můžou potřebovat ukládat a načítat data. U některých aplikačních úloh může toto úložiště dat používat místní a rychlé úložiště v uzlu, který už není potřebný při odstraňování lusků. Další úlohy aplikací můžou vyžadovat úložiště, které na více běžných datových svazcích v rámci platformy Azure přetrvává. Více lusků může potřebovat sdílet stejné datové svazky nebo znovu připojit datové svazky, pokud je uzel pod přeplánování na jiném uzlu. Nakonec možná budete muset do lusků vložit citlivé informace o konfiguraci dat nebo aplikací.

![Možnosti úložiště pro aplikace v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

V tomto článku se seznámíte se základními koncepcemi, které poskytují úložiště vašim aplikacím v AKS:

- [Svazků](#volumes)
- [Trvalé svazky](#persistent-volumes)
- [Třídy úložiště](#storage-classes)
- [Deklarace identit trvalých svazků](#persistent-volume-claims)

## <a name="volumes"></a>Svazky

Aplikace často potřebují mít schopnost ukládat a načítat data. Jelikož Kubernetes obvykle považuje jednotlivé lusky za dočasné a nepoužité prostředky, různé přístupy jsou k dispozici pro aplikace, které používají a uchovávají data podle potřeby. *Svazek* představuje způsob, jak ukládat, načítat a uchovávat data v různých luskech a v životním cyklu aplikace.

Tradiční svazky pro ukládání a načítání dat jsou vytvářeny jako Kubernetes prostředky, které jsou zajištěny Azure Storage. Tyto datové svazky můžete ručně vytvořit, aby je bylo možné přiřazovat do lusků přímo, nebo je nechat Kubernetes automaticky vytvořit. Tyto datové svazky můžou používat disky Azure nebo soubory Azure:

- *Disky Azure* můžete použít k vytvoření prostředku Kubernetes *datadisk* . Disky můžou využívat službu Azure Premium Storage, která je zajištěná vysokým výkonem SSD nebo Azure Storage úrovně Standard, zajištěná pravidelným HDD. Pro většinu produkčních a vývojových úloh použijte Premium Storage. Disky Azure jsou připojené jako *ReadWriteOnce*, takže jsou dostupné jenom pro jeden pod. Pro svazky úložiště, ke kterým se dá současně přistupovat více lusků, použijte soubory Azure.
- *Soubory Azure* můžete použít k připojení sdílené složky SMB 3,0 s účtem Azure Storage do lusků. Soubory umožňují sdílet data napříč více uzly a lusky. Soubory můžou používat úložiště Azure Standarded založené na běžných HDD nebo Azure Premium Storage založené na vysoce výkonném SSD.
> [!NOTE] 
> Služba soubory Azure podporuje Premium Storage v clusterech AKS se systémem Kubernetes 1,13 nebo vyšším.

V Kubernetes můžou svazky reprezentovat více než jenom tradiční disk, kde se můžou informace ukládat a načítat. Svazky Kubernetes lze také použít jako způsob, jak vložit data do podseznamu pro použití kontejnery. Mezi běžné další typy svazků v Kubernetes patří:

- *emptyDir* – tento svazek se běžně používá jako dočasné místo pro objekt pod. Všechny kontejnery v rámci pod mohou přistupovat k datům na svazku. Data zapsaná do tohoto typu svazku přetrvávají jenom za životnosti pod a, svazek se odstraní. Tento svazek obvykle používá základní úložiště disku místního uzlu, i když může existovat i v paměti uzlu.
- *tajný kód* – tento svazek se používá pro vkládání citlivých dat do lusků, jako jsou hesla. Nejdřív vytvoříte tajný klíč pomocí rozhraní Kubernetes API. Pokud definujete pod nebo nasazením, může se požadovat konkrétní tajný klíč. Tajné kódy se poskytují pouze uzlům, které mají naplánovanou hodnotu typu, která ji vyžaduje, a tajný klíč je uložený v *tmpfs*, který není zapsaný na disk. Když se poslední uzel pod uzlem, který vyžaduje tajný klíč, odstraní, tajný kód se odstraní z tmpfs uzlu. Tajné kódy jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze v rámci stejného oboru názvů.
- *configMap* – tento typ svazku slouží k vložení vlastností páru klíč-hodnota do lusků, jako jsou například informace o konfiguraci aplikace. Místo definování informací o konfiguraci aplikace v rámci image kontejneru ho můžete definovat jako prostředek Kubernetes, který se dá snadno aktualizovat a použít na nové instance lusků při jejich nasazení. Podobně jako při použití tajného kódu je třeba nejprve vytvořit ConfigMap pomocí rozhraní Kubernetes API. Tato ConfigMap se pak může vyžádat při definování pod nebo nasazováním. ConfigMaps jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze v rámci stejného oboru názvů.

## <a name="persistent-volumes"></a>Trvalé svazky

Svazky, které jsou definovány a vytvořeny jako součást životního cyklu pod, existují pouze v případě, že je pole pod odstraněno. Lusky často očekávají, že jejich úložiště zůstane v případě, že je v rámci události údržby přeplánováno na jiném hostiteli, zejména v StatefulSets. *Trvalý svazek* (PV) je prostředek úložiště vytvořený a spravovaný rozhraním API Kubernetes, které může existovat po dobu života jednotlivce pod.

K poskytnutí PersistentVolume se používají disky nebo soubory Azure. Jak je uvedeno v předchozí části na svazcích, je volba disků nebo souborů často určena nutností souběžného přístupu k datům nebo úrovni výkonu.

![Trvalé svazky v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

PersistentVolume je možné *staticky* vytvořit správcem clusteru nebo *dynamicky* vytvořit pomocí serveru Kubernetes API. Pokud je v seznamu naplánovaná a požaduje úložiště, které není aktuálně dostupné, Kubernetes může vytvořit základní disk Azure nebo soubory úložiště a připojit ho k poli pod. Dynamické zřizování používá *StorageClass* k identifikaci typu úložiště Azure, který je potřeba vytvořit.

## <a name="storage-classes"></a>Třídy úložiště

Pokud chcete definovat různé úrovně úložiště, jako je například Premium a Standard, můžete vytvořit *StorageClass*. StorageClass také definuje *reclaimPolicy*. Tato reclaimPolicy řídí chování základní prostředek služby Azure storage při pod se odstraní a trvalý svazek již může být nutná. Tento základní prostředek spravovat úložiště můžete odstranit nebo uchovávají pro použití s budoucí pod.

V AKS se vytvoří dvě počáteční StorageClasses:

- *výchozí* – používá službu Azure Storage Standard k vytvoření spravovaného disku. Zásady opětovné deklarace označují, že základní disk Azure se odstraní při odstranění trvalého svazku, který ho použil.
- *Managed Premium* – využívá Azure Premium Storage k vytvoření spravovaného disku. Zásady opětovné deklarace označují, že základní disk Azure se odstraní při odstranění trvalého svazku, který ho použil.

Pokud pro trvalý svazek není zadána žádná StorageClass, použije se výchozí StorageClass. Při žádosti o trvalé svazky postupujte opatrně, aby používaly vhodné úložiště, které potřebujete. StorageClass můžete vytvořit pro další potřeby pomocí `kubectl`. Následující příklad používá prémiové Managed Disks a určuje, že základní disk Azure by měl být *zachován* při odstranění části pod.

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

## <a name="persistent-volume-claims"></a>Trvalé deklarace svazků

PersistentVolumeClaim vyžádá buď disk nebo úložiště souborů konkrétního StorageClass, režim přístupu a velikost. Server Kubernetes API může dynamicky zřizovat základní prostředky úložiště v Azure, pokud neexistuje žádný prostředek k splnění deklarace identity na základě definovaného StorageClass. Definice pod zahrnuje připojení svazku, jakmile je svazek připojen k poli pod.

![Trvalý svazek deklarací identity v clusteru služby Kubernetes v Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

PersistentVolume je *vázán* na PersistentVolumeClaim, jakmile je dostupný prostředek úložiště přiřazený pod tím, aby na něj požadoval. Existuje 1:1 mapování trvalých svazků na deklarace identity.

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

Při vytváření definice pod je určena deklarace identity trvalého svazku pro vyžádání požadovaného úložiště. Pak zadáte *volumeMount* , ve kterém budou vaše aplikace číst a zapisovat data. V následujícím příkladu manifestu YAML se dozvíte, jak se dá k připojení svazku na */mnt/Azure*použít předchozí deklarace identity trvalého svazku:

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
