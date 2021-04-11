---
title: Osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii AKS
description: Seznamte se s osvědčenými postupy pro provozovatele clusteru, abyste dosáhli maximální doby provozu pro vaše aplikace a poskytovali vysokou dostupnost a přípravu na zotavení po havárii ve službě Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105081"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii ve službě Azure Kubernetes Service (AKS)

Když spravujete clustery ve službě Azure Kubernetes (AKS), bude důležitá doba provozu aplikací. Ve výchozím nastavení poskytuje AKS vysokou dostupnost pomocí více uzlů ve [virtuálním počítači VMSS (Virtual Machine Scale set)](../virtual-machine-scale-sets/overview.md). Ale u těchto více uzlů se systém nechrání před selháním oblasti. Aby se maximalizovala doba provozu, plánujte dopředu, abyste zachovali provozní kontinuitu a připravili zotavení po havárii.

Tento článek se zaměřuje na plánování kontinuity podnikových aplikací a zotavení po havárii v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plánování clusterů AKS v několika oblastech.
> * Směrování provozu napříč několika clustery pomocí Azure Traffic Manager.
> * Pro Registry imagí kontejneru použijte geografickou replikaci.
> * Plánování stavu aplikace napříč několika clustery.
> * Replikujte úložiště napříč několika oblastmi.

## <a name="plan-for-multiregion-deployment"></a>Plánování nasazení ve více oblastech

> **Osvědčený postup**
>
> Když nasadíte několik clusterů AKS, vyberte oblasti, kde je AKS k dispozici. Použijte spárované oblasti.

Cluster AKS se nasadí do jedné oblasti. Pokud chcete ochránit svůj systém před selháním, nasaďte svoji aplikaci do několika AKS clusterů napříč různými oblastmi. Při plánování nasazení clusteru AKS zvažte následující:

* [**Dostupnost oblasti AKS**](./quotas-skus-regions.md#region-availability)
    * Vyberte oblasti blízko vašim uživatelům. 
    * AKS se průběžně rozbalí do nových oblastí.
* [**Spárované oblasti Azure**](../best-practices-availability-paired-regions.md)
    * Pro vaši geografickou oblast vyberte dvě oblasti spárované dohromady.
    * Spárované oblasti koordinují aktualizace platforem a v případě potřeby stanovují prioritu úsilí při obnovení.
* **Dostupnost služby**
    * Rozhodněte se, jestli mají spárované oblasti horkou, horkou, horkou, teplou nebo horkou a studenou.
    * Chcete spustit obě oblasti současně s jednou oblastí *připravenou* k zahájení obsluhy provozu? Nebo:
    * Chcete pořídit provoz pro poskytování provozu jednomu regionu?

Dostupnost oblasti AKS a spárované oblasti jsou společné. Nasaďte clustery AKS do spárovaných oblastí určených ke správě oblasti zotavení po havárii společně. Například AKS je k dispozici v Východní USA a Západní USA. Tyto oblasti jsou spárovány. Při vytváření strategie AKS BC/DR vyberte tyto dvě oblasti.

Když nasadíte aplikaci, přidejte další krok do kanálu CI/CD pro nasazení do těchto více clusterů AKS. Aktualizace kanálů nasazení zabraňuje aplikacím v nasazení pouze do jedné z vašich oblastí a AKS clusterů. V takovém scénáři nebudou přenosy zákazníků směrované do sekundární oblasti dostávat nejnovější aktualizace kódu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Použití Azure Traffic Manager ke směrování provozu

> **Osvědčený postup**
>
> Azure Traffic Manager vás může směrovat k nejbližšímu clusteru AKS a instanci aplikace. Abyste dosáhli nejlepšího výkonu a redundance, přesměrujte veškerý provoz aplikací prostřednictvím Traffic Manager před tím, než přejdete do clusteru AKS.

Pokud máte více clusterů AKS v různých oblastech, použijte Traffic Manager k řízení toku provozu do aplikací spuštěných v každém clusteru. [Azure Traffic Manager](../traffic-manager/index.yml) je nástroj pro vyrovnávání zatížení pro provoz založený na DNS, který může distribuovat síťový provoz napříč oblastmi. Použijte Traffic Manager ke směrování uživatelů na základě doby odezvy clusteru nebo na základě geografického umístění.

![AKS s Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Pokud máte jeden cluster AKS, obvykle se k IP adrese služby nebo názvu DNS dané aplikace připojíte. V nasazení s více clustery byste se měli připojit k Traffic Manager názvu DNS, který odkazuje na služby na jednotlivých clusterech AKS. Tyto služby definujte pomocí koncových bodů Traffic Manager. Každý koncový bod je *IP adresa nástroje pro vyrovnávání zatížení služby*. Pomocí této konfigurace můžete směrovat síťový provoz z Traffic Manager koncového bodu v jedné oblasti do koncového bodu v jiné oblasti.

![Geografické směrování prostřednictvím Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager provede hledání DNS a vrátí nejvhodnější koncový bod. Vnořené profily mohou určovat prioritu primárního umístění. Například byste se měli připojit k nejbližší geografické oblasti. Pokud má tato oblast problém, Traffic Manager vás směrovat do sekundární oblasti. Tento přístup zajišťuje, že se můžete připojit k instanci aplikace i v případě, že vaše nejbližší geografická oblast není k dispozici.

Informace o nastavení koncových bodů a směrování najdete v tématu [Konfigurace metody směrování geografického provozu pomocí Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Směrování aplikací pomocí služby Azure front-dveří

Při použití rozděleného protokolu libovolného vysílání založeného na protokolu TCP se koncovým uživatelům zobrazí výzva k okamžitému připojení koncových uživatelů k nejbližšímu bodu POP na [přední dveře (](../frontdoor/front-door-overview.md) bod přítomnosti). Další funkce služby Azure front-dveří:
* Ukončení protokolu TLS
* Vlastní doména
* Firewall webových aplikací
* Přepsání adresy URL
* Spřažení relací 

Zkontrolujte potřeby provozu aplikace a zjistěte, které řešení je nejvhodnější.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Oblasti propojení s globálním partnerským vztahem virtuální sítě

Propojit obě virtuální sítě mezi sebou prostřednictvím [partnerského vztahu virtuálních sítí](../virtual-network/virtual-network-peering-overview.md) , aby bylo možné povolit komunikaci mezi clustery. Partnerský vztah virtuálních sítí vzájemně propojuje virtuální sítě a poskytuje velkou šířku pásma napříč páteřní sítí Microsoftu, a to i v různých geografických oblastech.

Před vytvořením partnerského vztahu virtuálních sítí se spuštěnými clustery AKS použijte standardní Load Balancer v clusteru AKS. Tento požadavek zpřístupňuje služby Kubernetes v rámci partnerského vztahu virtuálních sítí.

## <a name="enable-geo-replication-for-container-images"></a>Povolit geografickou replikaci pro Image kontejneru

> **Osvědčený postup**
> 
> Uložte image kontejneru do Azure Container Registry a geograficky replikujte registr do každé oblasti AKS.

K nasazení a spuštění aplikací v AKS potřebujete způsob, jak ukládat a načítat image kontejnerů. Container Registry se integruje s AKS, takže může bezpečně ukládat image kontejneru nebo grafy Helm. Container Registry podporuje základní geografickou replikaci k automatické replikaci vašich imagí do oblastí Azure po celém světě. 

Zlepšení výkonu a dostupnosti:
1. K vytvoření registru v každé oblasti, kde máte cluster AKS, použijte Container Registry geografickou replikaci. 
1. Každý cluster AKS pak vyžádá image kontejneru z místního registru kontejneru ve stejné oblasti:

![Container Registry geografickou replikaci pro Image kontejneru](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Když použijete Container Registry geografickou replikaci k vyžádání imagí ze stejné oblasti, výsledky jsou:

* **Rychlejší**: vyžádání imagí z vysokorychlostních síťových připojení s nízkou latencí v rámci stejné oblasti Azure.
* **Spolehlivější**: Pokud je oblast nedostupná, cluster AKS si vyžádá image z dostupného registru kontejnerů.
* **Levnější**: mezi datacentry se neúčtují žádné poplatky za výstup do sítě.

Geografická replikace je funkce registru kontejneru SKU úrovně *Premium* . Informace o tom, jak nakonfigurovat geografickou replikaci, najdete v tématu [Container Registry geografickou replikaci](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Odebrat stav služby z vnitřních kontejnerů

> **Osvědčený postup**
> 
> Vyhněte se ukládání stavu služby uvnitř kontejneru. Místo toho použijte platformu Azure jako službu (PaaS), která podporuje replikaci ve více oblastech.

*Stav služby* odkazuje na data v paměti nebo na disku, která služba potřebuje k fungování. Stav zahrnuje datové struktury a proměnné členů, které služba načítá a zapisuje. V závislosti na tom, jak je služba navržená, může stav obsahovat také soubory nebo jiné prostředky uložené na disku. Stav může například zahrnovat soubory, které databáze používá k ukládání dat a protokolů transakcí.

Stav může být buď externě, nebo společně umístěný s kódem, který pracuje s tímto stavem. Obvykle se Externalize stav pomocí databáze nebo jiného úložiště dat, které běží na různých počítačích v síti nebo které se zpracovávají na stejném počítači.

Kontejnery a mikroslužby jsou nejvíc odolné, když procesy, které jsou v nich spuštěné, neuchovávají stav. Vzhledem k tomu, že aplikace téměř vždy obsahují nějaký stav, použijte řešení PaaS, například:
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL Database

Pokud chcete vytvářet přenosné aplikace, přečtěte si následující pokyny:

* [Metodologie aplikace 12 Factor](https://12factor.net/)
* [Spuštění webové aplikace ve více oblastech Azure](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Vytvoření plánu migrace úložiště

> **Osvědčený postup**
>
> Pokud používáte Azure Storage, připravte a otestujte, jak se vaše úložiště migruje z primární oblasti do oblasti zálohování.

Vaše aplikace mohou používat Azure Storage pro svá data. Pokud ano, jsou vaše aplikace rozdělené mezi několik clusterů AKS v různých oblastech. Je nutné uchovat úložiště jako synchronizované. Tady jsou dva běžné způsoby replikace úložiště:

* Asynchronní replikace na základě infrastruktury
* Asynchronní replikace založená na aplikaci

### <a name="infrastructure-based-asynchronous-replication"></a>Asynchronní replikace na základě infrastruktury

Vaše aplikace můžou vyžadovat trvalé úložiště i po odstranění pod. V Kubernetes můžete použít trvalé svazky k trvalému ukládání dat. Trvalé svazky jsou připojené k virtuálnímu počítači uzlu a pak jsou vystavené pro lusky. Trvalé svazky následují po částech, a to i v případě, že se lusky přesunou do jiného uzlu ve stejném clusteru.

Použitá strategie replikace závisí na řešení úložiště. Následující běžná řešení úložiště poskytují vlastní pokyny k zotavení po havárii a replikaci:
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

Obvykle zadáváte společný bod úložiště, ve kterém aplikace zapisují svá data. Tato data se pak replikují napříč různými oblastmi a přistupovaly v místním prostředí.

![Asynchronní replikace na základě infrastruktury](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Pokud používáte Azure Managed Disks, můžete použít [Velero v Azure][velero] a [Kasten][kasten] pro zpracování replikace a zotavení po havárii. Tyto možnosti jsou zálohování řešení nativní, ale nepodporované nástrojem Kubernetes.

### <a name="application-based-asynchronous-replication"></a>Asynchronní replikace založená na aplikaci

Kubernetes aktuálně neposkytuje žádnou nativní implementaci pro asynchronní replikaci založenou na aplikaci. Vzhledem k tomu, že kontejnery a Kubernetes jsou volně propojeny, měly by všechny tradiční metody použití nebo jazyka fungovat. Obvykle aplikace samy replikují požadavky na úložiště, které jsou pak zapsány do základního úložiště dat jednotlivých clusterů.

![Asynchronní replikace založená na aplikaci](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na požadavky na provozní kontinuitu a zotavení po havárii pro clustery AKS. Další informace o operacích clusteru v AKS najdete v těchto článcích o osvědčených postupech:

* [Víceklientská architektura a izolace clusteru][aks-best-practices-cluster-isolation]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/