---
title: Osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii AKS
description: Seznamte se s osvědčenými postupy pro provozovatele clusteru, abyste dosáhli maximální doby provozu pro vaše aplikace a poskytovali vysokou dostupnost a přípravu na zotavení po havárii ve službě Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: b53c0002af3680567aabf0955f6bb4e0d99c2ab1
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093430"
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

**Osvědčený postup**: když nasadíte několik clusterů AKS, vyberte oblasti, kde je AKS k dispozici, a používejte spárované oblasti.

Cluster AKS se nasadí do jedné oblasti. Pokud chcete ochránit svůj systém před selháním, nasaďte svoji aplikaci do několika AKS clusterů napříč různými oblastmi. Při plánování nasazení clusteru AKS zvažte následující:

* [**Dostupnost oblasti AKS**](./quotas-skus-regions.md#region-availability): vyberte oblasti blízko vašim uživatelům. AKS se průběžně rozbalí do nových oblastí.
* [**Spárované oblasti Azure**](../best-practices-availability-paired-regions.md): pro vaši geografickou oblast vyberte dvě oblasti, které jsou vzájemně spárovány. Spárované oblasti koordinují aktualizace platforem a v případě potřeby stanovují prioritu úsilí při obnovení.
* **Dostupnost služby**: Rozhodněte, jestli mají spárované oblasti horkou, horkou, horkou, teplou nebo horkou a studenou. Chcete spustit obě oblasti současně s jednou oblastí *připravenou* k zahájení obsluhy provozu? Nebo chcete, aby jedna oblast měla čas na to, aby sloužila provozu?

Dostupnost oblasti AKS a spárované oblasti jsou společné. Nasaďte clustery AKS do spárovaných oblastí, které jsou navržené tak, aby bylo možné spravovat zotavení po havárii oblasti společně. Například AKS je k dispozici v Východní USA a Západní USA. Tyto oblasti jsou spárovány. Při vytváření strategie AKS BC/DR vyberte tyto dvě oblasti.

Když nasadíte aplikaci, přidejte další krok do kanálu CI/CD pro nasazení do těchto více clusterů AKS. Pokud vaše kanály nasazení neaktualizujete, můžou se aplikace nasadit jenom do jedné z vašich oblastí a clusterů AKS. Provoz zákazníků, který je směrován do sekundární oblasti, neobdrží nejnovější aktualizace kódu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Použití Azure Traffic Manager ke směrování provozu

**Osvědčený postup**: Azure Traffic Manager může směrovat zákazníky do nejbližšího clusteru AKS a instance aplikace. Abyste dosáhli nejlepšího výkonu a redundance, přesměrujte veškerý provoz aplikací prostřednictvím Traffic Manager před tím, než přejdete do clusteru AKS.

Pokud máte více clusterů AKS v různých oblastech, použijte Traffic Manager k řízení způsobu, jakým přenos toků do aplikací spuštěných v jednotlivých clusterech. [Azure Traffic Manager](../traffic-manager/index.yml) je nástroj pro vyrovnávání zatížení pro provoz založený na DNS, který může distribuovat síťový provoz napříč oblastmi. Použijte Traffic Manager ke směrování uživatelů na základě doby odezvy clusteru nebo na základě geografického umístění.

![AKS s Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Zákazníci, kteří mají jeden cluster AKS, se obvykle připojují k IP adrese služby nebo názvu DNS dané aplikace. V nasazení s více clustery by se zákazníci měli připojit k Traffic Manager názvu DNS, který odkazuje na služby na jednotlivých clusterech AKS. Tyto služby definujte pomocí koncových bodů Traffic Manager. Každý koncový bod je *IP adresa nástroje pro vyrovnávání zatížení služby*. Pomocí této konfigurace můžete směrovat síťový provoz z Traffic Manager koncového bodu v jedné oblasti do koncového bodu v jiné oblasti.

![Geografické směrování prostřednictvím Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager provede hledání DNS a vrátí nejvhodnější koncový bod uživatele. Vnořené profily mohou určovat prioritu primárního umístění. Například uživatelé by se měli obecně připojit k nejbližší geografické oblasti. Pokud má tato oblast problém, Traffic Manager místo toho uživatele přesměrují do sekundární oblasti. Tento přístup zajišťuje, že se zákazníci mohou připojit k instanci aplikace i v případě, že jejich nejbližší geografická oblast není k dispozici.

Informace o nastavení koncových bodů a směrování najdete v tématu [Konfigurace metody směrování geografického provozu pomocí Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Směrování aplikací pomocí služby Azure front-dveří

Pomocí rozděleného protokolu libovolného vysílání založeného na protokolu TCP zajišťuje [Služba front](../frontdoor/front-door-overview.md) -Endu Azure, aby se koncoví uživatelé mohli rychle připojit k nejbližšímu bodu POP na dvířka (bod přítomnosti). Mezi další funkce služby front-dveří Azure patří ukončení protokolu TLS, vlastní doména, Firewall webových aplikací, přepsání adresy URL a spřažení relací. Zkontrolujte potřeby provozu aplikace a zjistěte, které řešení je nejvhodnější.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Oblasti propojení s globálním partnerským vztahem virtuální sítě

Pokud je potřeba, aby clustery vzájemně komunikovaly, mohli byste mezi sebou vzájemně propojovat virtuální sítě, a [to prostřednictvím partnerského vztahu virtuálních sítí](../virtual-network/virtual-network-peering-overview.md). Tato technologie vzájemně propojuje virtuální sítě s vysokou šířkou pásma v páteřní síti Microsoftu, a to i v různých geografických oblastech.

Předpokladem pro partnerský vztah k virtuálním sítím, kde jsou spuštěné clustery AKS, je použití standardního Load Balancer v clusteru AKS, aby byly dostupné služby Kubernetes v rámci partnerského vztahu virtuálních sítí.

## <a name="enable-geo-replication-for-container-images"></a>Povolit geografickou replikaci pro Image kontejneru

**Osvědčený postup**: uložte image kontejneru do Azure Container registry a geograficky replikujte registr do každé oblasti AKS.

K nasazení a spuštění aplikací v AKS potřebujete způsob, jak ukládat a načítat image kontejnerů. Container Registry se integruje s AKS, takže může bezpečně ukládat image kontejneru nebo grafy Helm. Container Registry podporuje základní geografickou replikaci k automatické replikaci vašich imagí do oblastí Azure po celém světě. 

Pro zlepšení výkonu a dostupnosti použijte Container Registry geografickou replikaci k vytvoření registru v každé oblasti, kde máte cluster AKS. Každý cluster AKS pak vyžádá image kontejneru z místního registru kontejneru ve stejné oblasti:

![Container Registry geografickou replikaci pro Image kontejneru](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Když použijete Container Registry geografickou replikaci k vyžádání imagí ze stejné oblasti, výsledky jsou:

* **Rychlejší**: vyžádáte si obrázky z vysokorychlostních síťových připojení s nízkou latencí v rámci stejné oblasti Azure.
* **Spolehlivější**: Pokud je oblast nedostupná, cluster AKS si vyžádá image z dostupného registru kontejnerů.
* **Levnější**: mezi datacentry se neúčtují žádné poplatky za výstup do sítě.

Geografická replikace je funkce pro Registry kontejneru SKU úrovně *Premium* . Informace o tom, jak nakonfigurovat geografickou replikaci, najdete v tématu [Container Registry geografickou replikaci](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Odebrat stav služby z vnitřních kontejnerů

**Osvědčený postup**: Pokud je to možné, neukládají se do kontejneru stav služby. Místo toho použijte platformu Azure jako službu (PaaS), která podporuje replikaci ve více oblastech.

*Stav služby* odkazuje na data v paměti nebo na disku, která služba vyžaduje, aby fungovala. Stav zahrnuje datové struktury a proměnné členů, které služba načítá a zapisuje. V závislosti na tom, jak je služba navržená, může stav obsahovat také soubory nebo jiné prostředky, které jsou uložené na disku. Stav může například zahrnovat soubory, které databáze používá k ukládání dat a protokolů transakcí.

Stav může být buď externě, nebo společně umístěný s kódem, který zpracovává stav. Obvykle se Externalize stav pomocí databáze nebo jiného úložiště dat, které běží na různých počítačích v síti nebo které se zpracovávají na stejném počítači.

Kontejnery a mikroslužby jsou nejvíc odolné, když procesy, které jsou v nich spuštěné, neuchovávají stav. Vzhledem k tomu, že aplikace téměř vždy obsahují nějaký stav, použijte řešení PaaS, jako je Azure Cosmos DB, Azure Database for PostgreSQL, Azure Database for MySQL nebo Azure SQL Database.

Pokud chcete vytvářet přenosné aplikace, přečtěte si následující pokyny:

* [Metodologie aplikace 12 Factor](https://12factor.net/)
* [Spuštění webové aplikace ve více oblastech Azure](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Vytvoření plánu migrace úložiště

**Osvědčený postup**: pokud používáte Azure Storage, připravte a otestujte, jak se úložiště migruje z primární oblasti do oblasti zálohování.

Vaše aplikace mohou používat Azure Storage pro svá data. Vzhledem k tomu, že jsou vaše aplikace rozdělené mezi několik clusterů AKS v různých oblastech, je potřeba uchovat úložiště. Tady jsou dva běžné způsoby replikace úložiště:

* Asynchronní replikace na základě infrastruktury
* Asynchronní replikace založená na aplikaci

### <a name="infrastructure-based-asynchronous-replication"></a>Asynchronní replikace na základě infrastruktury

Vaše aplikace můžou vyžadovat trvalé úložiště i po odstranění pod. V Kubernetes můžete použít trvalé svazky k trvalému ukládání dat. Trvalé svazky jsou připojené k virtuálnímu počítači uzlu a pak jsou vystavené pro lusky. Trvalé svazky následují po částech, a to i v případě, že se lusky přesunou do jiného uzlu ve stejném clusteru.

Použitá strategie replikace závisí na řešení úložiště. Běžná řešení úložiště, jako jsou [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/), [CEPH](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [věž](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)a [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) , poskytují vlastní pokyny pro zotavení po havárii a replikaci.

Typickou strategií je poskytnout společný bod úložiště, ve kterém můžou aplikace zapisovat svá data. Tato data se pak replikují do různých oblastí a pak se k nim přistupovala místně.

![Asynchronní replikace na základě infrastruktury](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Pokud používáte Azure Managed Disks, můžete zvolit následující řešení pro replikaci a zotavení po havárii:

* [Velero v Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Backup](../backup/backup-overview.md)

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
