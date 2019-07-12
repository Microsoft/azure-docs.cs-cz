---
title: Vysoká dostupnost a zotavení po havárii ve službě Azure Kubernetes Service (AKS)
description: Podívejte se na clusteru operátor osvědčené postupy pro dosažení maximální doby provozu pro vaše aplikace poskytuje vysokou dostupnost a příprava na zotavení po havárii ve službě Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.openlocfilehash: 4d4535af1814ab1250bbd56c989b4849013adff6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614844"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro obchodní kontinuity podnikových procesů a zotavení po havárii ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) je důležitá dobu provozu aplikací. AKS poskytuje vysokou dostupnost s využitím více uzlů ve skupině dostupnosti. Ale tyto více uzly není ochranu vašeho systému před selhání oblasti. Pokud chcete maximalizovat dobu provozu, Plánujte dopředu a udržovat kontinuita podnikových procesů a příprava na zotavení po havárii.

Tento článek se zaměřuje na tom, jak plánovat pro provozní kontinuitu a zotavení po havárii ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plánování pro AKS clustery ve více oblastech.
> * Směrování přenosu napříč více clusterů pomocí Azure Traffic Manageru.
> * Použití geografické replikace pro vaše registry imagí kontejneru.
> * Plán pro stav aplikace napříč více clusterů.
> * Replikace úložiště v několika oblastech.

## <a name="plan-for-multiregion-deployment"></a>Plánování nasazení ve více oblastech

**Osvědčený postup**: Při nasazení více clusterů AKS, zvolte oblastech, kde je k dispozici AKS a použít spárovaných oblastí.

AKS cluster je nasazený do jedné oblasti. Na ochranu vašeho systému před selháním oblast, nasaďte aplikaci do více clusterů AKS napříč různými oblastmi. Když budete plánovat, kde chcete nasadit AKS cluster, vezměte v úvahu:

* [**Dostupnost v oblastech AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Zvolte oblastí blízko vašim uživatelům. AKS neustále rozšiřuje do nových oblastí.
* [**Spárovaných oblastech Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Určit vaši zeměpisnou polohu zvolte dvou oblastech, které jsou párovány mezi sebou. Spárované oblasti koordinovat aktualizace platformy a stanovení priorit o obnovení místech.
* **Dostupnost služeb**: Rozhodněte, jestli spárovaných oblastí by měl být horké/za běhu, horké/teplé nebo horké/studené. Chcete spustit obě oblasti současně s jednou oblastí *připravené* spustit obsluhující provoz? Nebo si přejete jedné oblasti mít čas na přípravu k obsluze provozu?

Dostupnost v oblastech AKS a spárované oblasti jsou společné potřeba. Nasazení clusterů AKS do spárovaných oblastí, které jsou určeny ke společné správě oblasti zotavení po havárii. Například AKS je k dispozici v oblastech východní USA a západní USA. Tyto oblasti jsou spárovány. Při vytváření strategie AKS BC/zotavení po Havárii, vyberte tyto dvě oblasti.

Při nasazení vaší aplikace přidáte další krok do kanálu CI/CD pro nasazení do těchto více clusterů AKS. Pokud nechcete aktualizovat vaše kanály nasazení, lze nasadit aplikace do pouze jednu z oblastí a AKS clustery. Provozu zákazníka, který se přesměruje do sekundární oblasti nebudou získávat nejnovější aktualizace kódu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Umožňuje směrovat provoz Azure Traffic Manageru

**Osvědčený postup**: Azure Traffic Manager může směrovat zákazníkům jejich nejbližší instanci aplikací a clusteru AKS. Pro nejlepší výkon a redundance směrovat všechen provoz aplikace pomocí Traffic Manageru předtím, než přejde do clusteru AKS.

Pokud máte více clusterů AKS v různých oblastech, použijte Traffic Manager řídit tok provozu pro aplikace, které běží v každém clusteru. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) je Vyrovnávání zatížení provozu na základě DNS, které můžete distribuovat síťový provoz mezi oblastmi. Traffic Manager můžete používat ke směrování uživatelů na základě doby odezvy clusteru nebo podle zeměpisné oblasti.

![AKS pomocí Traffic Manageru](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Zákazníci, kteří mají jeden cluster AKS obvykle připojení k service IP nebo DNS název dané aplikace. V multicluster nasazení by měl zákazníkům připojit k názvu DNS Traffic Manageru, který odkazuje na službu na každém clusteru AKS. Definujte tyto služby s použitím koncových bodů Traffic Manageru. Každý koncový bod je *služby IP adresu nástroje pro vyrovnávání zatížení*. Pomocí této konfigurace ke směrování síťového provozu z koncových bodů Traffic Manageru v jedné oblasti do koncového bodu v jiné oblasti.

![Geografické směrování pomocí Traffic Manageru](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager provede vyhledávání DNS a vrátí nejvhodnější koncové uživatele. Vnořené profily můžete určit prioritu primární umístění. Uživatelé by měly obecně připojit k jejich nejbližší geografické oblasti. Pokud tuto oblast dojde k problému, Traffic Manager místo toho bude směrovat uživatele do sekundární oblasti. Tento přístup zajišťuje, že zákazníci mohou připojit k instanci aplikace, i když jejich nejbližší geografické oblasti k dispozici.

Informace o tom, jak nastavit koncové body a směrování najdete v tématu [nakonfigurovat geografickou metodu směrování provozu pomocí Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Směrování vrstvy 7 aplikace s branou služby Azure

Traffic Manager používá službu DNS (úroveň 3) tvar provoz. [Služba Azure branou](https://docs.microsoft.com/azure/frontdoor/front-door-overview) nabízí možnost směrování protokolu HTTP/HTTPS (vrstva 7). Další funkce branou služby Azure zahrnují SSL ukončení, vlastní doménu, brány firewall webových aplikací, přepisování adres URL a spřažení relace. Zkontrolujte potřebuje provozu aplikace pochopit, jaké řešení je nejvhodnější.

## <a name="enable-geo-replication-for-container-images"></a>Povolte geografickou replikaci pro Image kontejneru

**Osvědčený postup**: Store imagí kontejnerů v Azure Container Registry a geografická replikace registru do každé oblasti AKS.

K nasazení a spouštění aplikací ve službě AKS, potřebujete způsob, jak ukládat a načítat Image kontejneru. Container Registry se integruje se službou AKS mohli bezpečně uložit imagí kontejneru nebo helmu. Container Registry podporuje více hlavních serverů geografickou replikaci, která váš obrázky automaticky replikovat do oblastí Azure po celém světě. 

Pokud chcete zlepšit výkon a dostupnost, použijte k vytvoření registru v jednotlivých oblastech, kde máte AKS cluster geografická replikace služby Container Registry. Každý cluster AKS potom stáhne Image kontejneru z registru místní kontejner ve stejné oblasti:

![Geografická replikace služby Container Registry pro Image kontejneru](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Při použití geografická replikace služby Container Registry k vyžádání imagí ze stejné oblasti, jsou výsledky:

* **Faster**: Přetahování imagí z vysokorychlostní sítě s nízkou latencí připojení v rámci stejné oblasti Azure.
* **Spolehlivější**: Pokud není k dispozici v oblasti, clusteru AKS načítá obrázky z registr kontejneru k dispozici.
* **Cheaper**: Neplatí žádné poplatky odchozího přenosu dat sítě mezi datacentry.

Geografická replikace je funkce *Premium* registry kontejnerů SKU. Informace o tom, jak konfigurace geografické replikace najdete v tématu [geografická replikace služby Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Odebrat službu stavu z uvnitř kontejnerů

**Osvědčený postup**: Kde je to možné, neukládejte stav služby uvnitř kontejneru. Místo toho použijte platformu Azure jako služba (PaaS), který podporuje replikace ve více oblastech.

*Služba stavu* odkazuje na data v paměti nebo na disku, které vyžaduje služba pro funkce. Stav zahrnuje datových struktur a členské proměnné, které Služba čte a zapisuje. V závislosti na tom, jak je navržený službě stavu také mohou zahrnovat souborům nebo jiným prostředkům, které jsou uloženy na disku. Stav může například obsahovat soubory, které používá databázi k ukládání dat a transakční protokoly.

Stav můžete externalized nebo umístěny společně s kódem, který provádí úpravy stavu. Obvykle využívajícím stavu s využitím databáze nebo jiného úložiště dat, která běží na různých počítačích v síti nebo mimo proces, který běží na stejném počítači.

Kontejnery a mikroslužby jsou nejvíce odolné, když procesy, které běží v nich obsažené neuchovává stavu. Vzhledem k tomu, že aplikace téměř vždy obsahují některé stavu, použijte řešení PaaS, jako je například databáze Azure pro MySQL, Azure Database pro PostgreSQL a Azure SQL Database.

K vytvoření přenosné aplikace, najdete v následujících pokynů:

* [Metodologie 12-factor app.](https://12factor.net/)
* [Spuštění webové aplikace v několika oblastech Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Vytvořte plán migrace úložiště

**Osvědčený postup**: Pokud používáte službu Azure Storage, připravit a otestovat migrace úložiště z primární oblasti do oblasti zálohování.

Vaše aplikace může používat služby Azure Storage pro svá data. Vzhledem k tomu, že vaše aplikace jsou rozděleny mezi více clusterů AKS v různých oblastech, budete muset zachovat úložiště synchronizované. Tady jsou dvě běžné způsoby replikace úložiště:

* Asynchronní replikace na základě infrastruktury
* Asynchronní replikace na základě aplikace

### <a name="infrastructure-based-asynchronous-replication"></a>Asynchronní replikace na základě infrastruktury

Vaše aplikace může vyžadovat trvalého úložiště i po odstranění pod. V systému Kubernetes můžete použít trvalé svazky do trvalého úložiště dat. Trvalé svazky jsou připojené k virtuálnímu počítači uzlu a pak vystavit tyto pody. Trvalé svazky podle podů i v případě, že tyto pody přesunou do jiného uzlu ve stejném clusteru.

Strategie replikace, kterou použijete, závisí na řešení úložiště. Běžné řešení úložiště, jako například [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [věž](https://rook.io/docs/rook/master/disaster-recovery.html), a [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) poskytovat vlastní pokyny k zotavení po havárii a replikace.

Typické strategie je poskytnout společný bod úložiště, kde aplikace může zapisovat data. Tato data se pak replikují napříč oblastmi a pak přistupovat místně.

![Asynchronní replikace na základě infrastruktury](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Pokud používáte Azure Managed Disks, zvolte možnost replikace a řešení zotavení po Havárii, jako je:

* [Velero v Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Asynchronní replikace na základě aplikace

Kubernetes v současné době poskytuje nativní implementaci pro asynchronní replikace na základě aplikace. Vzhledem k tomu, že kontejnery a Kubernetes jsou volně propojené a by měl pracovat tradiční přístup aplikace nebo jazyka. Obvykle samotnými aplikacemi replikovat požadavků na úložiště, které jsou pak zapsány do základního datového úložiště každý cluster.

![Asynchronní replikace na základě aplikace](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na provozní kontinuitu a aspekty zotavení po havárii pro AKS clustery. Další informace o operacích clusteru ve službě AKS najdete v článcích o osvědčených postupech:

* [Izolace víceklientskou architekturu a clusteru][aks-best-practices-cluster-isolation]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
