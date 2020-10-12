---
title: Konfigurace úložiště
description: Vysvětlení možností konfigurace úložiště datových služeb s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: c1560325f21fd60e6bdb2a64eb987359a7246ff2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317323"
---
# <a name="storage-configuration"></a>Konfigurace úložiště

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Koncepty úložiště Kubernetes

Kubernetes poskytuje vrstvu abstrakce infrastruktury přes základní virtualizační technický zásobník (volitelné) a hardware. Způsob, jakým Kubernetes abstrakce, je prostřednictvím **[tříd úložiště](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. V době zřizování pod je možné určit třídu úložiště, která se má použít pro každý svazek. V době, kdy je v seznamu zřízen, je zavolán **[zřizovací](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** modul třídy úložiště, který zřídí úložiště, a poté je v tomto zřízeném úložišti vytvořen **[trvalý svazek](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** a potom je připojen k trvalému svazku prostřednictvím **[deklarace identity trvalého svazku](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

Kubernetes poskytuje způsob, jak poskytovatelům infrastruktury úložiště připojit ovladače (označované také jako "doplňky"), které rozšiřuje Kubernetes. Doplňky úložiště musí odpovídat **[standardu rozhraní úložiště kontejnerů](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. V tomto nekonečném **[seznamu ovladačů CSI](https://kubernetes-csi.github.io/docs/drivers.html)** najdete spousty doplňků. Který ovladač CSI, který použijete, bude záviset na faktorech, jako je například to, jestli spouštíte v rámci spravované služby Kubernetes hostované v cloudu nebo poskytovatele OEM, kterého používáte pro svůj hardware.

Spuštěním tohoto příkazu můžete zobrazit, které třídy úložiště jsou v clusteru Kubernetes nakonfigurované:

``` terminal
kubectl get storageclass
```

Příklad výstupu z clusteru Azure Kubernetes Service (AKS):

``` terminal
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Podrobnosti o třídě úložiště můžete získat spuštěním tohoto příkazu:

``` terminal
kubectl describe storageclass\<storage class name>
```

Příklad:

``` terminal
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Aktuálně zřízené trvalé svazky a trvalé deklarace svazků můžete zobrazit spuštěním následujících příkazů:

``` terminal
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Příklad zobrazení trvalých svazků:

``` terminal

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Příklad zobrazení trvalých deklarací identity Volume:

``` terminal

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Faktory, které je potřeba vzít v úvahu při volbě konfigurace úložiště

Výběr správné třídy úložiště je velmi důležitý pro odolnost a výkon dat. Zvolíte-li špatnou třídu úložiště, mohou být vaše data ohrožena celkovou ztrátou dat v případě selhání hardwaru nebo může být výsledkem méně optimálního výkonu.

K dispozici jsou obvykle dva typy úložiště:

- Úložiště **místního úložiště** , které je zřízené na místních pevných discích daného uzlu. Tento druh úložiště může být ideální v souvislosti s výkonem, ale vyžaduje konkrétně návrh pro redundanci dat replikací dat napříč více uzly.
- **Vzdálené úložiště sdíleného úložiště** , které je zřízené na některém vzdáleném zařízení úložiště – například síť SAN, servery NAS nebo cloudového úložiště, jako je EBS nebo Azure Files. Tento druh úložiště obecně poskytuje automatickou redundanci dat, ale obecně ne tak rychle, jak může být místní úložiště.

> [!NOTE]
> Prozatím, pokud používáte systém souborů NFS, je třeba před nasazením řadiče dat ARC Azure nastavit v souboru profilu nasazení allowRunAsRoot na hodnotu true.

### <a name="data-controller-storage-configuration"></a>Konfigurace úložiště řadiče dat

Některé služby v Azure ARC pro datové služby závisí na tom, že jsou nakonfigurované tak, aby používaly vzdálené sdílené úložiště, protože služby nemají schopnost replikovat data. Tyto služby se nacházejí v kolekci v luskech řadiče dat:

|**Služba**|**Trvalé deklarace svazků**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Instance SQL kontroleru**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Služba API kontroleru**|`<namespace>/data-controller`|

V době, kdy se zřídí řadič dat, se třída úložiště, která se má použít pro každý z těchto trvalých svazků, určí buď předáním třídy--úložiště |. -SC parametr pro `azdata arc dc create` příkaz nebo nastavením tříd úložiště v control.jssouboru šablony nasazení, který se používá.

Šablony nasazení, které jsou k dispozici v poli, mají zadanou výchozí třídu úložiště, která je vhodná pro cílové prostředí, ale může být přepsána v době nasazení. Projděte si podrobné kroky pro změnu [profilu nasazení](create-data-controller.md) , aby se změnila konfigurace třídy úložiště pro řadič dat lusky v době nasazení.

Pokud jste třídu úložiště nastavili pomocí třídy--Storage-Class | -SC parametr třída úložiště bude použita pro obě třídy úložiště protokolů i dat. Pokud jste v souboru šablony nasazení nastavili třídy úložiště, můžete pro protokoly a data zadat různé třídy úložiště.

Důležité faktory, které je potřeba vzít v úvahu při výběru třídy úložiště pro lusky datového kontroleru:

- Je **nutné** použít vzdálenou, sdílenou třídu úložiště, aby se zajistila odolnost dat, a to tak, aby v případě, že uzel pod nebo uzel zemře, bylo-li vráceno do zálohy, se může znovu připojit k trvalému svazku.
- Data zapsaná do služby Controller SQL instance, metriky DB a log DB jsou obvykle poměrně nízká a nejsou citlivá na latenci, takže úložiště Ultra-rychlé výkon není důležité. Pokud máte uživatele, kteří často používají rozhraní Grafana a Kibana a máte velký počet instancí databáze, může vám vaše uživatelé vytěžit z rychlejšího provádění úložiště.
- Požadovaná kapacita úložiště je proměnná s počtem instancí databáze, které jste nasadili, protože se shromažďují protokoly a metriky pro každou instanci databáze. Data se uchovávají v protokolech a DATABÁZÍch metriky po dobu 2 týdnů, než se vyprázdní. 
- Změna po nasazení třídy úložiště je velmi obtížná, není dokumentována a není podporována. Nezapomeňte v době nasazení zvolit správně třídu úložiště.

> [!NOTE]
> Pokud není zadána žádná třída úložiště, bude použita výchozí třída úložiště. Pro každý cluster Kubernetes může existovat jenom jedna výchozí třída úložiště. Můžete [změnit výchozí třídu úložiště](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Konfigurace úložiště instance databáze

Každá instance databáze obsahuje data, protokoly a záložní trvalé svazky. Třídy úložiště pro tyto trvalé svazky lze zadat v době nasazení. Pokud není zadána žádná třída úložiště, bude použita výchozí třída úložiště.

Při vytváření instance pomocí `azdata arc sql mi create` příkazů nebo `azdata arc postgres server create` jsou k dispozici dva parametry, které lze použít k nastavení tříd úložiště:

> [!NOTE]
> Některé z těchto parametrů jsou ve vývoji a budou k dispozici `azdata arc sql mi create` v `azdata arc postgres server create` nadcházejících verzích a.

|Název parametru, krátký název|Použití|
|---|---|
|`--storage-class-data`, `-scd`|Slouží k určení třídy úložiště pro všechny datové soubory včetně souborů protokolu transakcí.|
|`--storage-class-logs`, `-scl`|Slouží k zadání třídy úložiště pro všechny soubory protokolů.|
|`--storage-class-data-logs`, `-scdl`|Slouží k zadání třídy úložiště pro soubory protokolu transakcí databáze. **Poznámka: ještě není k dispozici.**|
|`--storage-class-backups`, `-scb`|Slouží k zadání třídy úložiště pro všechny záložní soubory. **Poznámka: ještě není k dispozici.**|

Následující tabulka uvádí cesty uvnitř kontejneru spravované instance Azure SQL, který je namapovaný na trvalý svazek pro data a protokoly:

|Název parametru, krátký název|Cesta uvnitř kontejneru MSSQL-MIAA|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Obsahuje adresáře pro instalaci MSSQL a další systémové procesy. Adresář MSSQL obsahuje výchozí data (včetně transakčních protokolů), protokol chyb & záložní adresáře.|
|`--storage-class-logs`, `-scl`|/var/log|Obsahuje adresáře, které ukládají výstup konzoly (stderr, stdout), další informace o protokolování procesů uvnitř kontejneru.|

Následující tabulka uvádí cesty uvnitř kontejneru instance PostgreSQL, který je namapovaný na trvalý svazek pro data a protokoly:

|Název parametru, krátký název|Cesta uvnitř kontejneru Postgres|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Obsahuje data a adresáře protokolu pro instalaci Postgres.|
|`--storage-class-logs`, `-scl`|/var/log|Obsahuje adresáře, které ukládají výstup konzoly (stderr, stdout), další informace o protokolování procesů uvnitř kontejneru.|

Každá instance databáze bude mít samostatný trvalý svazek pro datové soubory, protokoly a zálohy. To znamená, že pro každý z těchto typů souborů se bude oddělení vstupu a výstupu vztahovat na to, jak bude zřizovat úložiště. Každá instance databáze má vlastní deklarace trvalého svazku a trvalé svazky.

Pokud je v dané instanci databáze více databází, budou všechny databáze používat stejnou deklaraci trvalého svazku, trvalý svazek a třídu úložiště. Všechna zálohování – rozdílové zálohy protokolů a úplné zálohy budou používat stejnou deklaraci trvalého svazku a trvalý svazek. V následující části jsou uvedeny deklarace trvalých svazků pro instanci databáze lusky:

|**Instance**|**Trvalé deklarace svazků**|
|---|---|
|**Spravovaná instance Azure SQL**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Instance Azure Database for PostgreSQL**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Škálování Azure PostgreSQL**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Ordinální rozsahy od 0 do m, kde W je počet pracovních procesů)*|

Důležité faktory, které je potřeba vzít v úvahu při výběru třídy úložiště pro instanci databáze lusky:

- Instance databáze se dají nasadit buď do jednoho vzoru pod, nebo podle vzoru vícenásobný pod. Příkladem jednoho pod vzorem je vývojářská instance spravované instance Azure SQL nebo obecné použití cenové úrovně Azure SQL Managed instance. Příkladem vzoru typu "více pod" je vysoce dostupná podniková cenová úroveň Azure SQL Managed instance. (Poznámka: cenové úrovně jsou ve vývoji a ještě nejsou k dispozici pro zákazníky.)  Instance databáze nasazené se vzorem jednoho pod **musí** použít vzdálenou, sdílenou třídu úložiště, aby se zajistila odolnost dat, a to tak, aby v případě, že uzel pod nebo uzel zemře, bylo možné znovu připojit k trvalému svazku. Naproti tomu vysoce dostupná spravovaná instance Azure SQL používá skupiny dostupnosti Always On k replikaci dat z jedné instance do druhé buď synchronně, nebo asynchronně. Zejména v případě, že jsou data replikována synchronně, je vždy více kopií dat – obvykle 3. Z tohoto důvodu je možné použít místní úložiště nebo vzdálené sdílené třídy úložiště pro data a soubory protokolů. V případě použití místního úložiště se data pořád uchovávají i v případě neúspěšného hardwaru pod uzlem, uzlu nebo úložiště. Vzhledem k této flexibilitě se můžete rozhodnout pro lepší výkon použít místní úložiště.
- Výkon databáze je z velké části funkcí propustnosti vstupně-výstupních operací daného úložného zařízení. Pokud je vaše databáze těžká čtení nebo silné zápisy, měli byste zvolit třídu úložiště, která má hardware, který je určený pro daný typ úlohy. Pokud se například vaše databáze používá hlavně pro zápisy, můžete zvolit místní úložiště s RAID 0. Pokud se vaše databáze nejčastěji používá pro čtení malého množství "aktivních dat", ale existuje velký objem celkového úložiště studených dat, můžete zvolit zařízení sítě SAN, které podporuje vrstvené úložiště. Výběr správné třídy úložiště se ve skutečnosti neliší od výběru typu úložiště, které byste použili pro jakoukoli databázi.
- Pokud používáte místní zařízení úložiště, měli byste zajistit, aby místní svazky zřízené pro data, protokoly a zálohy byly na různých základních úložných zařízeních, aby se zabránilo kolizi při vstupně-výstupních operacích disku. Operační systém by měl být také na svazku, který je připojen k samostatnému disku. To je v podstatě stejný návod, který by se měl vyřídit jako instance databáze na fyzickém hardwaru.
- Vzhledem k tomu, že všechny databáze v dané instanci sdílejí trvalé deklarace identity a trvalý svazek, ujistěte se, že nenajdete instance zaneprázdněné databáze ve stejné instanci databáze. Pokud je to možné, oddělte zaneprázdněné databáze na své vlastní databázové instance, abyste se vyhnuli kolizím vstupu a výstupu. Dále použijte označení uzlu jako cílení na instance databáze na samostatných uzlech tak, aby bylo možné distribuovat celkový objem vstupně-výstupních operací napříč více uzly. Pokud používáte virtualizaci, měli byste zvážit distribuci vstupně-výstupních přenosů, nikoli jenom na úrovni uzlu, ale i kombinované aktivity vstupu a výstupu, na kterých probíhají všechny virtuální počítače uzlu na daném fyzickém hostiteli.

## <a name="estimating-storage-requirements"></a>Odhad požadavků na úložiště
Každé v případě, který obsahuje stavová data, se v této verzi používá dva trvalé svazky – jeden trvalý svazek pro data a další trvalý svazek pro protokoly. Následující tabulka uvádí počet trvalých svazků vyžadovaných pro jeden datový adaptér, Azure SQL Managed instance, Azure Database for PostgreSQL instance a Azure PostgreSQL instance pro škálování na více instancí:

|Typ prostředku|Počet stavových lusků|Požadovaný počet trvalých svazků|
|---|---|---|
|Kontroler dat|4 ( `control` , `controldb` , `logsdb` , `metricsdb` )|4 * 2 = 8|
|Spravovaná instance Azure SQL|1|2|
|Instance Azure Database for PostgreSQL|1| 2|
|Škálování Azure PostgreSQL|1 + W (W = počet pracovních procesů)|2 * (1 + W)|

Následující tabulka zobrazuje celkový počet trvalých svazků potřebných pro ukázkové nasazení:

|Typ prostředku|Počet instancí|Požadovaný počet trvalých svazků|
|---|---|---|
|Kontroler dat|1|4 * 2 = 8|
|Spravovaná instance Azure SQL|5|5 * 2 = 10|
|Instance Azure Database for PostgreSQL|5| 5 * 2 = 10|
|Škálování Azure PostgreSQL|2 (počet pracovních procesů = 4 na instanci)|2 * 2 * (1 + 4) = 20|
|***Celkový počet trvalých svazků***||8 + 10 + 10 + 20 = 48|

Tento výpočet se dá použít k naplánování úložiště pro cluster Kubernetes na základě Správce úložiště nebo prostředí. Například pokud se místní zřídí úložiště používá pro cluster Kubernetes s 5 uzly, pak pro ukázkové nasazení nad každým uzlem je potřeba aspoň úložiště pro 10 trvalých svazků. Podobně při zřizování clusteru Azure Kubernetes Service (AKS) s 5 uzly vyzvednutím vhodné velikosti virtuálního počítače pro fond uzlů, aby bylo možné připojit 10 datových disků, je důležité. Další podrobnosti o tom, jak nastavit velikost uzlů pro úložiště pro uzly AKS, najdete [tady](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## <a name="choosing-the-right-storage-class"></a>Výběr správné třídy úložiště

### <a name="on-premises-and-edge-sites"></a>Místní a hraniční weby

Společnost Microsoft a její partneři OEM, OS a Kubernetes pracují na programu certifikace pro datové služby Azure ARC. Tento program poskytne zákazníkům srovnatelné výsledky testů ze sady nástrojů pro certifikaci. Testy vyhodnocují kompatibilitu funkcí, zátěžové testování a výkon a škálovatelnost. Každý z těchto výsledků testů indikuje použitý operační systém, použitou Kubernetes distribuci, použitý HW, použitý doplněk CSI a používané třídy úložiště. To zákazníkům pomůže vybrat nejlepší třídu úložiště, operační systém, Kubernetes distribuci a HW pro jejich požadavky. Další informace o tomto programu a výsledcích počátečních testů budou k dispozici blíž k obecné dostupnosti datových služeb Azure ARC.

#### <a name="public-cloud-managed-kubernetes-services"></a>Veřejný cloud, spravované služby Kubernetes Services

Pro veřejné cloudové spravované služby Kubernetes můžete provádět následující doporučení:

|Veřejné cloudové služby|Doporučení|
|---|---|
|**Azure Kubernetes Service (AKS)**|Služba Azure Kubernetes (AKS) má dva typy úložiště – soubory Azure a disky Azure. Každý typ úložiště má dvě úrovně cen a výkonu: Standard (HDD) a Premium (SSD). Proto jsou čtyři třídy úložiště poskytované v AKS `azurefile` (úroveň Azure Files úrovně Standard), `azurefile-premium` (vrstva Azure Files Premium), (disky Azure na úrovni Premium) `default` a `managed-premium` (vrstva Azure disks úrovně Premium). Výchozí třída úložiště je `default` (standardní vrstva Azure Disks). Existují výrazné **[cenové rozdíly](https://azure.microsoft.com/en-us/pricing/details/storage/)** mezi typy a úrovněmi, které by měly být přihlédnuto k vašemu rozhodnutí. Pro produkční úlohy s vysokým výkonem doporučujeme použít `managed-premium` pro všechny třídy úložiště. Pro úlohy pro vývoj a testování, testování konceptu atd., kde náklady jsou zváženy, `azurefile` je možnost nejlevnější. Všechny čtyři možnosti se dají použít v situacích, které vyžadují vzdálené sdílené úložiště, protože to jsou všechna zařízení úložiště připojená k síti v Azure. Přečtěte si další informace o [AKS Storage](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| Elastická Kubernetes služba Amazon má jednu primární třídu úložiště založenou na [ovladači úložiště EBS CSI](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Tento postup se doporučuje pro produkční úlohy. K dispozici je nový [ovladač úložiště systému souborů EFS CSI](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) , který je možné přidat do clusteru EKS, ale v tuto chvíli je ve fázi beta verze a může se změnit. I když AWS říká, že tento ovladač úložiště je pro produkční prostředí podporovaný, nedoporučujeme ho používat, protože je stále ve verzi beta a může se změnit. Třída úložiště EBS je výchozí a je volána `gp2` . Přečtěte si další informace o [EKS Storage](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Modul Google Kubernetes Engine (GKE) má jen jednu třídu úložiště nazvanou `standard` , která se používá pro [GCE trvalé disky](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Pouze jediným z nich je také výchozí hodnota. I když je pro GKE [místní a statický svazek](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) , který můžete použít s přímým připojením SSD, nedoporučujeme ho používat, protože ho nespravuje ani nepodporuje Google. Přečtěte si další informace o [GKE Storage](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
