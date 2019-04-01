---
title: Naplánování nasazení clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o plánování a příprava pro produkční nasazení clusteru Service Fabric do Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663234"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Plánování a příprava pro nasazení clusteru

Plánování a příprava pro nasazení produkčního clusteru je velmi důležité.  Existuje celá řada faktorů, které byste měli zvážit.  Tento článek vás provede kroky přípravy nasazení clusteru.

## <a name="read-the-best-practices-information"></a>Přečtěte si informace o osvědčených postupů
Ke správě aplikace Azure Service Fabric a clustery úspěšně, jsou operace, důrazně doporučujeme že provést k optimalizaci spolehlivost vašeho produkčního prostředí.  Další informace najdete v článku [cluster a aplikace Service Fabric osvědčené postupy](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Vyberte operační systém clusteru
Service Fabric umožňuje vytvářet clustery Service Fabric na všechny virtuální počítače nebo počítače se systémem Windows Server nebo Linux.  Před nasazením do clusteru, musíte zvolit operační systém:  Windows nebo Linux.  Každý uzel (VM) v clusteru běží stejný operační systém, nelze kombinovat Windows a virtuální počítače s Linuxem ve stejném clusteru.

## <a name="capacity-planning"></a>Plánování kapacity
Pro každého produkčního nasazení plánování kapacity je důležitý krok. Zde je uvedeno několik bodů, které je vhodné vzít v úvahu v rámci procesu.

* Počáteční počet typů uzlů pro váš cluster 
* Vlastnosti každého typu uzlu (velikost, počet instancí, primární, internetového, počet virtuálních počítačů atd.)
* Spolehlivost a odolnost clusteru

### <a name="select-the-initial-number-of-node-types"></a>Vyberte počáteční počet typů uzlů
Nejprve budete muset zjistit, co se děje, kterou vytváříte cluster má být použit pro. Jaké typy aplikací, které máte v úmyslu nasadit do tohoto clusteru? Vaše aplikace mít několik služeb, a některý z nich musí být public nebo připojení k Internetu? Vaše služby, (, které tvoří vaši aplikaci) mají potřeby různé infrastruktury, jako je například větší paměť RAM nebo vyšší cyklů procesoru? Cluster Service Fabric se může skládat z více než jeden typ uzlu: typ primárního uzlu a jeden nebo více typů bez primárního uzlu. Každý typ uzlu je namapována na škálovací sadu virtuálních počítačů. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. [Vlastnosti uzlu a omezení umístění] [ placementconstraints] lze nastavit tak, chcete-li omezit určité služby na konkrétní uzel typy.  Další informace najdete v článku [počet typů uzlu clusteru je potřeba nejprve](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Vyberte uzel vlastnosti pro každý typ uzlu
Typy uzlů definují skladovou Položku virtuálního počítače, počet a vlastnosti virtuálních počítačů v sadě přidruženého měřítka.

Minimální velikost virtuálních počítačů pro každý typ uzlu je určená [úroveň odolnosti] [ durability] zvolíte pro typ uzlu.

Minimální počet virtuálních počítačů pro primární typ uzlu je určeno [úroveň spolehlivosti] [ reliability] zvolíte.

Zobrazit minimální doporučení pro [primárního uzlu typy](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [stavová zatížení na typy jiné než primární uzel](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), a [Bezstavová zatížení na typy jiné než primární uzel](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Žádné další než minimální počet uzlů by měla vycházet z počtu replik aplikace/služby, které chcete spustit v tomto typu uzlu.  [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md) pomáhá odhadnout prostředky, které potřebujete ke spuštění vaší aplikace. Můžete vždy škálování clusteru nebo dolů později upravit pro změnu úlohy aplikace. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Vyberte úroveň odolnosti a spolehlivosti clusteru
Úroveň odolnosti se používá k označení systému oprávnění, které mají virtuální počítače se základní infrastrukturou Azure. Toto oprávnění umožňuje v primární typ uzlu, Service Fabric na požadavek na pozastavení všech virtuálních počítačů úrovně infrastruktury (například restartování virtuálního počítače, obnovení z Image virtuálního počítače nebo migraci virtuálních počítačů), které ovlivnit požadavky kvora pro systémové služby a stavové služby. Toto oprávnění umožňuje typy jiné než primární uzel Service Fabric se pozastavit všechny virtuální počítač úrovně infrastruktury žádosti (například restartování virtuálního počítače, obnovení z Image virtuálního počítače a migraci virtuálních počítačů), které ovlivňují požadavky kvora pro stavové služby.  Výhody různých úrovních a doporučení, na kterou úroveň se mají použít a když, naleznete v tématu [vlastnosti odolnosti clusteru][durability].

Úroveň spolehlivosti se používá k nastavení počtu replik systémových služeb, které chcete spustit v tomto clusteru na primární typ uzlu. Další počet replik, vyšší spolehlivost systémové služby jsou ve vašem clusteru.  Výhody různých úrovních a doporučení, na kterou úroveň se mají použít a když, naleznete v tématu [spolehlivost clusteru][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Povolit reverzní proxy server a/nebo DNS
Služby připojení k sobě navzájem uvnitř clusteru obecně přímo přístupné koncovým bodům, dalších služeb, protože uzly v clusteru jsou ve stejné místní síti. K usnadnění připojení mezi službami Service Fabric nabízí další služby: A [služba DNS](service-fabric-dnsservice.md) a [reverzní proxy service](service-fabric-reverseproxy.md).  Při nasazování clusteru je možné povolit obě služby.

Od mnoho služeb zejména kontejnerizovaných služeb, může mít existující název adresy URL, nebudou moct vyřešíte pomocí standardní DNS protokolu (spíše než protokol pojmenování Service) je praktické, zejména ve scénářích aplikací "metodou lift and shift". To je přesně čemu služby DNS. Umožňuje mapovat názvy DNS na název služby a proto přeložit IP adresy koncového bodu.

Reverzní proxy server adresy služby v clusteru, které zpřístupňují koncové body HTTP (včetně HTTPS). Reverzní proxy server výrazně zjednodušuje volání jiných služeb tím, že poskytuje konkrétní formát identifikátoru URI.  Reverzní proxy server zpracovává také vyřešit, připojení a opakujte kroky potřebné k jedné službě komunikovat s jinou.

## <a name="prepare-for-disaster-recovery"></a>Příprava na zotavení po havárii
Důležitou součástí zajištění vysoké dostupnosti zajišťuje, že služby přežijí všechny různé druhy chyb. To je obzvláště důležité pro chyby, které neplánované a mimo vaši kontrolu. [Příprava na zotavení po havárii](service-fabric-disaster-recovery.md) popisuje některé běžné režimy selhání, které mohou být katastrofy, pokud není modelovat a spravovány správně. Také popisuje způsoby zmírnění rizik a akcí Pokud přesto došlo k havárii.

## <a name="production-readiness-checklist"></a>Kontrolní seznam připravenosti k produkci
Je vaše aplikace a clusteru, jste připraveni udělat produkční provoz? Před nasazením do produkčního prostředí vašeho clusteru, spuštění [připravenosti produkční kontrolní seznam](service-fabric-production-readiness-checklist.md). Udržujte aplikace a clusteru, které běží plynule z práce prostřednictvím položky v tomto seznamu úkolů. Důrazně doporučujeme všechny tyto položky do odškrtli před přechodem do produkčního prostředí.

## <a name="next-steps"></a>Další postup
* [Vytvoření clusteru Service Fabric se systémem Windows](service-fabric-best-practices-overview.md)
* [Vytvoření clusteru Service Fabric s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster