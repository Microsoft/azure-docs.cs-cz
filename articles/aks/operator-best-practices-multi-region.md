---
title: Vysoká dostupnost a zotavení po havárii ve službě Azure Kubernetes Service (AKS)
description: Seznamte se s osvědčenými postupy operátora clusteru, abyste dosáhli maximální dostupnosti vašich aplikací, zajištění mno žílací a přípravy na zotavení po havárii ve službě Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 894ec4e543f0c68cc652141d2c1578cda61d7f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594735"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro kontinuitu podnikání a zotavení po havárii ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) se dostupnost aplikací stává důležitou. AKS poskytuje vysokou dostupnost pomocí více uzlů v sadě dostupnosti. Ale tyto více uzlů nechrání váš systém před selháním oblasti. Chcete-li maximalizovat dobu provozu, naplánujte si další dobu, abyste zachovali kontinuitu provozu a připravili se na zotavení po havárii.

Tento článek se zaměřuje na plánování kontinuity podnikání a zotavení po havárii v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plánujte pro clustery AKS ve více oblastech.
> * Směrovat provoz mezi více clustery pomocí Azure Traffic Manager.
> * Pro registry bitových obrázků kontejneru použijte geografickou replikaci.
> * Plánujte stav aplikace ve více clusterech.
> * Replikujte úložiště ve více oblastech.

## <a name="plan-for-multiregion-deployment"></a>Plánování pro nasazení ve více oblastech

**Osvědčený postup:** Při nasazení více clusterů AKS zvolte oblasti, kde je k dispozici AKS a použijte spárované oblasti.

Cluster AKS se nasadí do jedné oblasti. Chcete-li chránit systém před selháním oblasti, nasaďte aplikaci do více clusterů AKS v různých oblastech. Při plánování nasazení clusteru AKS zvažte:

* [**Dostupnost oblasti AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Zvolte oblasti v blízkosti uživatelů. AKS se neustále rozšiřuje do nových oblastí.
* [**Spárované oblasti Azure:**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)Pro vaši zeměpisnou oblast zvolte dvě oblasti, které jsou vzájemně spárované. Spárované oblasti koordinují aktualizace platformy a v případě potřeby upřednostňují úsilí o obnovení.
* **Dostupnost služeb**: Rozhodněte se, zda mají být vaše spárované oblasti horké/horké, horké/teplé nebo teplé/studené. Chcete spustit obě oblasti současně s jednou oblastí *připravenou* k zahájení provozu? Nebo chcete, aby jeden region měl čas připravit se na provoz?

Dostupnost oblasti AKS a spárované oblasti jsou společným aspektem. Nasaďte clustery AKS do spárovaných oblastí, které jsou navrženy tak, aby společně spravovaly zotavení po havárii oblasti. Například AKS je k dispozici ve východní USA a západní USA. Tyto oblasti jsou spárovány. Tyto dvě oblasti zvolte při vytváření strategie AKS BC/DR.

Při nasazování aplikace přidejte další krok do kanálu CI/CD k nasazení do těchto více clusterů AKS. Pokud neaktualizujete kanály nasazení, aplikace mohou být nasazeny pouze do jedné z vašich oblastí a clusterů AKS. Provoz zákazníků, který je směrován do sekundární oblasti, neobdrží nejnovější aktualizace kódu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Použití Azure Traffic Manageru ke směrování provozu

**Osvědčený postup:** Azure Traffic Manager může nasměrovat zákazníky na jejich nejbližší cluster AKS a instanci aplikace. Chcete-li dosáhnout nejlepšího výkonu a redundance, nasměrujte veškerý provoz aplikací prostřednictvím Traffic Manageru před tím, než přejde do clusteru AKS.

Pokud máte více clusterů AKS v různých oblastech, použijte Traffic Manager řídit, jak toky provozu do aplikací, které běží v každém clusteru. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) je nástroje pro vyrovnávání zatížení založený na DNS, který umí distribuovat síťový provoz napříč oblastmi. Pomocí Traffic Manageru můžete směrovat uživatele na základě doby odezvy clusteru nebo podle zeměpisné polohy.

![AKS s Traffic Managerem](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Zákazníci, kteří mají jeden cluster AKS, se obvykle připojují k IP nebo názvu DNS dané aplikace. V nasazení s více clustery by se zákazníci měli připojit k názvu DNS Traffic Manageru, který odkazuje na služby v každém clusteru AKS. Definujte tyto služby pomocí koncových bodů Traffic Manageru. Každý koncový bod je *IP služby pro vyrovnávání zatížení*. Tato konfigurace slouží k přímému síťovému provozu z koncového bodu Traffic Manager u jedné oblasti do koncového bodu v jiné oblasti.

![Geografické směrování přes Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager provádí vyhledávání DNS a vrátí nejvhodnější koncový bod uživatele. Vnořené profily mohou určit prioritu primárního umístění. Uživatelé by se například měli obecně připojovat ke své nejbližší zeměpisné oblasti. Pokud tato oblast má problém, Traffic Manager místo toho přesměruje uživatele do sekundární oblasti. Tento přístup zajišťuje, že zákazníci se mohou připojit k instanci aplikace i v případě, že jejich nejbližší geografická oblast není k dispozici.

Informace o nastavení koncových bodů a směrování naleznete v [tématu Konfigurace metody směrování geografického provozu pomocí Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Směrování aplikací vrstvy 7 pomocí služby Azure Front Door Service

Traffic Manager používá DNS (vrstva 3) k tvarování provozu. [Služba Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) poskytuje možnost směrování HTTP/HTTPS (vrstva 7). Mezi další funkce služby Azure Front Door Service patří ukončení SSL, vlastní doména, brána firewall webové aplikace, přepis adres URL a spřažení relací. Zkontrolujte potřeby provozu aplikace a zjistěte, které řešení je nejvhodnější.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Propojení oblastí s partnerským vztahem globální virtuální sítě

Pokud clustery potřebují mluvit mezi sebou, připojení obou virtuálních sítí k sobě lze dosáhnout prostřednictvím [partnerského vztahu virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Tato technologie vzájemně propojuje virtuální sítě a poskytuje velkou šířku pásma v páteřní síti společnosti Microsoft, a to i v různých zeměpisných oblastech.

Předpokladem pro partnerský virtuální sítě, kde jsou spuštěny clustery AKS, je použití standardního nástroje pro vyrovnávání zatížení v clusteru AKS, aby byly služby Kubernetes dostupné v rámci partnerského vztahu virtuální sítě.

## <a name="enable-geo-replication-for-container-images"></a>Povolení geografické replikace pro ifotky kontejnerů

**Osvědčený postup:** Uložte ibi kontejnerů v Azure Container Registry a geograficky replikujte registr do každé oblasti AKS.

Chcete-li nasadit a spustit aplikace v AKS, potřebujete způsob, jak uložit a vyžádat image kontejneru. Kontejner registru integruje s AKS, takže můžete bezpečně ukládat vaše image kontejneru nebo Helm grafy. Container Registry podporuje georeplikaci multimasterů k automatické replikaci imagí do oblastí Azure po celém světě. 

Chcete-li zvýšit výkon a dostupnost, použijte geografickou replikaci registru kontejnerů k vytvoření registru v každé oblasti, kde máte cluster AKS. Každý cluster AKS pak vytáhne image kontejneru z registru místního kontejneru ve stejné oblasti:

![Geografická replikace registru kontejnerů pro bitové kopie kontejnerů](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Při použití geografické replikace registru kontejnerů k vyžádat obrázky ze stejné oblasti, výsledky jsou:

* **Rychlejší:** Můžete natahovat image z vysokorychlostních síťových připojení s nízkou latencí v rámci stejné oblasti Azure.
* **Spolehlivější:** Pokud oblast není k dispozici, cluster AKS vytáhne bitové kopie z registru kontejneru k dispozici.
* **Levnější**: Mezi datovými centry není žádné síťové odchozí poplatky.

Geografická replikace je funkce registrů kontejnerů *sku služby Premium.* Informace o konfiguraci geografické replikace naleznete v [tématu Geo-replication registru kontejnerů](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Odebrat stav služby z vnitřních kontejnerů

**Osvědčený postup**: Pokud je to možné, neuklápějte stav služby uvnitř kontejneru. Místo toho použijte platformu Azure jako službu (PaaS), která podporuje replikaci více oblastí.

*Stav služby* odkazuje na data v paměti nebo na disku, která služba vyžaduje ke funkci. Stav zahrnuje datové struktury a členské proměnné, které služba čte a zapisuje. V závislosti na tom, jak je služba navržena, může stav také obsahovat soubory nebo jiné prostředky, které jsou uloženy na disku. Stav může například zahrnovat soubory, které databáze používá k ukládání dat a protokolů transakcí.

Stav může být buď externalizované nebo spoluumístěny s kódem, který manipuluje se stavem. Obvykle externalizovat stav pomocí databáze nebo jiné úložiště dat, které běží na různých počítačích v síti nebo který běží proces u stejného počítače.

Kontejnery a mikroslužby jsou nejodolnější při procesech, které běží uvnitř nich nezachovávají stav. Vzhledem k tomu, že aplikace téměř vždy obsahují nějaký stav, použijte řešení PaaS, jako je Azure Database for MySQL, Azure Database for PostgreSQL nebo Azure SQL Database.

Chcete-li vytvářet přenosné aplikace, postupujte podle následujících pokynů:

* [Metodika 12faktorových aplikací](https://12factor.net/)
* [Spuštění webové aplikace ve více oblastech Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Vytvoření plánu migrace úložiště

**Osvědčený postup:** Pokud používáte Azure Storage, připravte a otestujte, jak migrovat úložiště z primární oblasti do oblasti zálohování.

Vaše aplikace mohou používat Azure Storage pro svá data. Vzhledem k tomu, že vaše aplikace jsou rozloženy do více clusterů AKS v různých oblastech, je třeba zachovat úložiště synchronizované. Tady jsou dva běžné způsoby replikace úložiště:

* Asynchronní replikace založená na infrastruktuře
* Asynchronní replikace založená na aplikaci

### <a name="infrastructure-based-asynchronous-replication"></a>Asynchronní replikace založená na infrastruktuře

Vaše aplikace mohou vyžadovat trvalé úložiště i po odstranění podu. V Kubernetes můžete použít trvalé svazky k zachování úložiště dat. Trvalé svazky jsou připojeny k virtuálnímu virtuálnímu modulu uzlu a pak vystaveny pody. Trvalé svazky sledovat pody i v případě, že pody jsou přesunuty do jiného uzlu uvnitř stejného clusteru.

Strategie replikace, kterou používáte, závisí na vašem řešení úložiště. Běžná úložná řešení, jako jsou [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)a [Portworx,](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) poskytují vlastní pokyny týkající se zotavení po havárii a replikace.

Typickou strategií je poskytnout společný bod úložiště, kde aplikace mohou zapisovat svá data. Tato data jsou pak replikována napříč oblastmi a poté přístupná místně.

![Asynchronní replikace založená na infrastruktuře](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Pokud používáte spravované disky Azure, můžete zvolit řešení replikace a zotavení po havárii, jako jsou tato:

* [Velero v Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Obnovení webu Azure](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Asynchronní replikace založená na aplikaci

Kubernetes aktuálně poskytuje žádné nativní implementace pro asynchronní replikaci založené na aplikacích. Vzhledem k tomu, že kontejnery a Kubernetes jsou volně spojeny, jakékoli tradiční aplikace nebo jazykový přístup by měl fungovat. Aplikace samy obvykle replikují požadavky na úložiště, které se pak zapisují do základního úložiště dat každého clusteru.

![Asynchronní replikace založená na aplikaci](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na kontinuitu podnikání a aspekty zotavení po havárii pro clustery AKS. Další informace o operacích clusteru v AKS naleznete v následujících článcích o doporučených postupech:

* [Vícenásobná architektura a izolace clusteru][aks-best-practices-cluster-isolation]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
