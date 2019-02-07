---
title: Operátor osvědčené postupy – vysoká dostupnost a zotavení po havárii ve službě Azure Kubernetes služby (AKS)
description: Podívejte se na clusteru – operátor osvědčené postupy pro maximální doby provozu pro vaše aplikace k zajištění vysoké dostupnosti a příprava pro situace, zotavení po havárii ve službě Azure Kubernetes služby (AKS)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 9958f5f0f1435af231c1426a249c745f4a2352c5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816607"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro obchodní kontinuity podnikových procesů a zotavení po havárii ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) je důležitá dobu provozu aplikací. AKS poskytuje vysokou dostupnost s využitím více uzlů ve skupině dostupnosti. Těchto více uzlů nechcete chránit v případě selhání oblasti. Pokud chcete maximalizovat dobu provozu, implementujte některé obchodní kontinuity podnikových procesů a funkce pro zotavení po havárii.

Tento článek se zaměřuje na aspekty, které vám pomůžou doporučené postupy plán obchodní kontinuity podnikových procesů a zotavení po havárii ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
* Plán pro AKS clustery ve více oblastech
* Směrování přenosu napříč více clusterů pomocí Azure Traffic Manageru
* Použití geografické replikace pro vaše registry imagí kontejneru
* Plán pro stav aplikace napříč více clusterů
* Replikace úložiště napříč několika oblastmi

## <a name="plan-for-multi-region-deployment"></a>Plán pro nasazení v různých oblastech

**Osvědčené postupy pro moduly** – když nasadíte víc clusterů AKS, zvolte oblastech, kde je k dispozici AKS a použít spárovaných oblastí.

AKS cluster je nasazený do jedné oblasti. K ochraně před selhání oblasti, nasazení aplikace do více clusterů AKS v různých oblastech. Pokud máte v úmyslu jaké oblasti k nasazení clusteru AKS, platí následující aspekty:

* [Dostupnost v oblastech AKS](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Zvolte oblastí blízko vašim uživatelům. AKS neustále rozšiřuje do nových oblastí.
* [Spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * Určit vaši zeměpisnou polohu zvolte dvou oblastech, které jsou párovány mezi sebou. Tyto oblasti koordinovat aktualizace platformy a určit prioritu o obnovení místech.
* Dostupnost úrovně služby (horká nebo horkou, horká/záložním, horké/studené)
  * Chcete spustit obě oblasti současně s jednou oblastí *připravené* spustit obsluhující provoz nebo jedné oblasti, který je čas na přípravu k obsluze provozu.

Dostupnost v oblastech AKS a spárované oblasti jsou společné pozornost. Nasazení clusterů AKS do spárovaných oblastí, které jsou určeny ke společné správě oblasti zotavení po havárii. Například je k dispozici v AKS *USA – východ* a *USA – západ*. Tyto oblasti jsou také spárovat. Tyto dvě oblasti doporučenou při vytváření strategie AKS BC/zotavení po Havárii.

Když nasadíte aplikaci, musíte taky přidat další krok do kanálu CI/CD pro nasazení do těchto více clusterů AKS. Pokud nechcete aktualizovat vaše kanály nasazení, nasazení aplikace může nasadit jenom do jedné oblasti a AKS clustery. Provozu zákazníka, který se přesměruje do sekundární oblasti nebudou získávat nejnovější aktualizace kódu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Umožňuje směrovat provoz Azure Traffic Manageru

**Osvědčené postupy pro moduly** -Azure Traffic Manager může směrovat zákazníkům jejich nejbližší instanci aplikací a clusteru AKS. Pro nejlepší výkon a redundance směrovat všechen provoz aplikace pomocí Traffic Manageru před přechodem k vašemu clusteru AKS.

S více clustery AKS v různých oblastech je nutné určit, jak provoz se směřuje do aplikace, které běží v každém clusteru. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) je Vyrovnávání zatížení provozu na základě DNS, které můžete distribuovat síťový provoz mezi oblastmi. Může směrovat uživatele na základě doby odezvy clusteru, nebo podle zeměpisné oblasti.

![AKS pomocí Azure Traffic Manageru](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

S jediným clusterem AKS, zákazníkům se obvykle připojují k *IP služby* nebo název DNS dané aplikace. V nasazení více clusterů by měl zákazníkům připojit k názvu DNS Traffic Manageru, který odkazuje na službu na každém clusteru AKS. Tyto služby jsou definovány pomocí koncových bodů Traffic Manageru. Každý koncový bod je *IP adresu nástroje pro vyrovnávání zatížení Service*. Tato konfigurace umožňuje směrovat síťový provoz z koncových bodů Traffic Manageru v jedné oblasti do koncového bodu v jiné oblasti.

![Geografické směrování pomocí Azure Traffic Manageru](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager slouží k vyhledávání DNS a vrátit nejvhodnější koncové uživatele. Vnořené profily můžete použít s prioritou používá pro primární umístění. Uživatel by měl třeba primárně připojit k jejich nejbližší geografické oblasti. Pokud tuto oblast dojde k problému, Traffic Manager místo toho přesměruje do sekundární oblasti. Tento přístup zajistí, že zákazníci mohou vždy připojit k instanci aplikace, i když jejich nejbližší geografické oblasti k dispozici.

Pokyny k nastavení těchto koncových bodů a směrování, najdete v článku [nakonfigurovat geografickou metodu směrování provozu pomocí služby Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Směrování vrstvy 7 aplikace s Azure branou

Azure Traffic Manageru pomocí služby DNS (úroveň 3) tvar provoz. [Přední dveře Azure (preview)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) nabízí možnost směrování protokolu HTTP/HTTPS (vrstva 7). Další funkce branou zahrnují SSL ukončení, vlastní doménu, brány Firewall webových aplikací, přepisování adres URL a spřažení relace.

Zkontrolujte potřebuje provozu aplikace pochopit, jaké řešení je nejvhodnější.

## <a name="enable-geo-replication-for-container-images"></a>Povolte geografickou replikaci pro Image kontejneru

**Osvědčené postupy pro moduly** -Store imagí kontejnerů v Azure Container Registry a geografická replikace registru do každé oblasti AKS.

K nasazení a spouštění aplikací ve službě AKS, potřebujete způsob, jak ukládat a načítat Image kontejneru. Azure Container Registry (ACR) můžete integrovat s AKS pro bezpečné ukládání vašich imagí kontejneru nebo grafy Helm. ACR podporuje multimasterovou geografickou replikaci, která váš obrázky automaticky replikovat do oblastí Azure po celém světě. Pokud chcete zlepšit výkon a dostupnost, použijte k vytvoření registru v jednotlivých oblastech, kde máte AKS cluster geografické replikace ACR. Každý cluster AKS potom stáhne Image kontejneru z místního registru ACR ve stejné oblasti:

![Azure Container Registry geografické replikace pro Image kontejneru](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Mezi výhody používání geografické replikace ACR, patří:

* **Načítají se obrázky ze stejné oblasti je rychlejší.** Vyžádání imagí z vysokorychlostní s nízkou latencí síťová připojení v rámci stejné oblasti Azure.
* **Načítají se obrázky ze stejné oblasti je spolehlivější.** Pokud není k dispozici v oblasti, clusteru AKS stáhne image z různých registru ACR, které zůstává k dispozici.
* **Načítají se obrázky ze stejné oblasti je levnější.** Neplatí žádné poplatky odchozího přenosu dat sítě mezi datacentry.

Geografická replikace je funkce *Premium* registry skladové položky služby ACR. Pokyny ke konfiguraci replikace, najdete v článku [geografická replikace registru kontejneru Azure](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Odebrat službu stavu z uvnitř kontejnerů

**Osvědčené postupy pro moduly** – je to možné, neukládejte stav služby uvnitř kontejneru. Místo toho pomocí služby Azure PaaS, které podporují replikace ve více oblastech.

Stav služby odkazuje na data v paměti nebo na disku, které vyžaduje služba pro funkce. Stav zahrnuje datových struktur a členské proměnné, které Služba čte a zapisuje. V závislosti na tom, jak je navržený službu může stav také zahrnovat souborům nebo jiným prostředkům, které jsou uložené na disku. Například soubory byste použili databázi k ukládání dat a transakční protokoly.

Stav můžete externalized nebo umístěny společně s kódem, který je manipulaci se stavem. Externalizace stavu se obvykle provádí pomocí databáze nebo jiného úložiště dat, která běží na různých počítačích v síti nebo mimo proces ve stejném počítači.

Kontejnery a mikroslužby jsou nejvíce odolné, když procesy, které běží v nich obsažené nezachovávají si stav. Aplikace téměř vždy obsahovat u některých stavů, využijte platformu jako službu řešení, jako je Azure Database pro MySQL/Postgres nebo Azure SQL.

Podrobnosti o jak vytvářet aplikace, které jsou větší přenositelnost najdete v následujících pokynů:

* [Metodologie 12 Factor App](https://12factor.net/).
* [Spuštění webové aplikace v několika oblastech Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Vytvořte plán migrace úložiště

**Osvědčené postupy pro moduly** – Pokud používáte službu Azure Storage, připravit a otestovat migrace úložiště z primárního na záložní oblasti.

Vaše aplikace může pro svá data pomocí služby Azure Storage. Jak vaše aplikace jsou rozděleny mezi více clusterů AKS v různých oblastech, je potřeba nechat úložiště synchronizované. Dvě běžné způsoby replikace úložiště zahrnují následujících postupů:

* Asynchronní replikace na základě aplikace
* Asynchronní replikace na základě infrastruktury

### <a name="infrastructure-based-asynchronous-replication"></a>Asynchronní replikace na základě infrastruktury

Vaše aplikace můžou vyžadovat trvalého úložiště i po odstranění pod. V systému Kubernetes můžete použít trvalé svazky do trvalého úložiště dat. Tyto trvalé svazky jsou připojené k virtuálnímu počítači uzlu a pak vystavit tyto pody. Trvalé svazky podle podů, i v případě, že chcete pod se přesune do jiného uzlu ve stejném clusteru.

V závislosti na využití řešení úložiště může být jiný strategie replikace. Běžné řešení úložiště, jako například [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [věž](https://rook.io/docs/rook/master/disaster-recovery.html), a [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) všechny mají své vlastní pokyny.

Centrální přístup je společný bod úložiště pro aplikace pro zápis svá data. Tato data se pak replikují napříč oblastmi a pak přistupovat místně.

![Asynchronní replikace na základě infrastruktury](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Pokud používáte Azure Managed Disks, budou k dispozici replikace a řešení zotavení po Havárii pomocí jedné z následujících dvou přístupů patří:

* [Ark v Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Asynchronní replikace na základě aplikace

Aktuálně neexistuje žádná Kubernetes nativní implementace asynchronní replikace na základě aplikace. Tradiční přístup aplikace nebo jazyka volně povaze kontejnery a Kubernetes, by měl pracovat. Centrální přístup je pro samotnými aplikacemi k replikaci úložiště požadavků, které jsou pak zapsány do každý cluster základní datové úložiště.

![Asynchronní replikace na základě aplikace](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Další postup

Tento článek zaměřuje na provozní kontinuitu a po havárii aspekty zotavení v clusteru AKS. Další informace o operacích clusteru ve službě AKS najdete v následující osvědčené postupy:

* [Izolace více tenantů a clusteru][aks-best-practices-cluster-isolation]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
