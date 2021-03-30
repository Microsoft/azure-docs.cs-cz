---
title: Traffic Manager Azure s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak používat Azure Traffic Manager s Azure Site Recovery pro zotavení po havárii a pro migraci.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bb112e0b2d1c64e65ecaf6749a25707d8632c0cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134950"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager se službou Azure Site Recovery

Azure Traffic Manager umožňuje řídit distribuci provozu napříč koncovými body vaší aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager používá službu DNS (Domain Name System) k přímému směrování požadavků klientů na nejvhodnější koncový bod, a to na základě metody směrování provozu a stavu koncových bodů. Traffic Manager poskytuje celou řadu [metod směrování provozu](../traffic-manager/traffic-manager-routing-methods.md) a [možností monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Klienti se připojí k vybranému koncovému bodu přímo. Traffic Manager není proxy nebo brána a nevidí přenos mezi klientem a službou.

Tento článek popisuje, jak můžete kombinovat inteligentní směrování v Azure Traffic Monitor s výkonnými možnostmi pro zotavení po havárii a migraci Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>Převzetí služeb při selhání z místního prostředí do Azure

V prvním scénáři zvažte **Společnost A** , která má veškerou její aplikační infrastrukturu spuštěnou v místním prostředí. V zájmu zajištění provozní kontinuity a dodržování předpisů se **Společnost** rozhodla použít Azure Site Recovery k ochraně svých aplikací.

**Společnost A** provozuje aplikace s veřejnými koncovými body a chce schopnost plynule přesměrovat provoz do Azure v případě havárie. Metoda [prioritního](../traffic-manager/traffic-manager-configure-priority-routing-method.md) směrování provozu v Azure Traffic Manager umožňuje společnosti a snadno implementovat tento vzor převzetí služeb při selhání.

Nastavení je následující:
- **Společnost A** vytvoří [profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- S využitím metody směrování **priority** **Společnost A** vytvoří dva koncové body – **primární** pro místní prostředí a **převzetí služeb při selhání** pro Azure. K **primární** je přiřazena priorita 1 a pro **převzetí služeb při selhání** je přiřazena Priorita 2.
- Vzhledem k tomu, že je **primární** koncový bod hostovaný mimo Azure, je koncový bod vytvořený jako [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncový bod.
- V Azure Site Recovery nemá lokalita Azure žádné virtuální počítače ani aplikace spuštěné před převzetím služeb při selhání. Proto se koncový bod **převzetí služeb při selhání** vytvoří také jako **externí** koncový bod.
- Ve výchozím nastavení je provoz uživatele směrován do místní aplikace, protože k tomuto koncovému bodu je přidružena nejvyšší priorita. Žádný provoz není směrován do Azure, pokud je **primárním** koncovým bodem v pořádku.

![Místní – až Azure před převzetím služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

V případě havárie může společnost A aktivovat [převzetí služeb při selhání](site-recovery-failover.md) do Azure a obnovovat své aplikace v Azure. Když Azure Traffic Manager zjistí, že **primární** koncový bod už není v pořádku, použije v odpovědi DNS automaticky koncový bod **převzetí služeb při selhání** a uživatelé se připojí k aplikaci obnovenou v Azure.

![Místní – až Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

V závislosti na podnikových požadavcích může **Společnost A** vybrat vyšší nebo nižší [četnost zjišťování](../traffic-manager/traffic-manager-monitoring.md) pro přepínání mezi místními počítači a Azure v případě havárie a zajištění minimálního výpadku uživatelů.

Když je havárie obsaženo, může **Společnost A** navracet služby po obnovení z Azure do svého místního prostředí ([VMware](vmware-azure-failback.md) nebo [Hyper-V](hyper-v-azure-failback.md)) pomocí Azure Site Recovery. Když teď Traffic Manager detekuje, že **primární** koncový bod je opět v pořádku, automaticky využívá **primární** koncový bod v odpovědích DNS.

## <a name="on-premises-to-azure-migration"></a>Migrace z místního prostředí do Azure

Kromě zotavení po havárii Azure Site Recovery taky umožňuje [migrace do Azure](migrate-overview.md). Azure Site Recovery díky výkonným funkcím testovacího převzetí služeb při selhání můžete zákazníkům vyhodnotit výkon aplikace v Azure, aniž by to mělo vliv na jejich místní prostředí. A když jsou zákazníci připraveni na migraci, můžou zvolit, že se mají migrovat celé úlohy dohromady, nebo se můžete rozhodnout migrovat a škálovat postupně.

Metodu [váženého](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) směrování v Azure Traffic Manager je možné využít k směrování některých částí příchozího provozu do Azure, a to tak, že se většina přesměruje do místního prostředí. Tento přístup může přispět k vyhodnocení výkonu škálování, protože můžete dál zvyšovat váhu přiřazenou k Azure při migraci více a dalších úloh do Azure.

Například **Společnost B** se rozhodne pro migraci ve fázích a přesunutím některých jeho aplikačního prostředí a zachování místního nastavení. V počátečních fázích, kdy je většina prostředí místní, se k místnímu prostředí přiřadí větší váha. Traffic Manager vrací koncový bod na základě vah přiřazených k dostupným koncovým bodům.

![Migrace z místního prostředí do Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Během migrace jsou oba koncové body aktivní a většina provozu je směrována do místního prostředí. Jak migrace pokračuje, může se koncovým bodem v Azure přiřadit větší váha a nakonec může být místní koncový bod deaktivovaný po migraci.

## <a name="azure-to-azure-failover"></a>Převzetí služeb při selhání Azure do Azure

V tomto příkladu zvažte **Společnost C** , která má veškerou svoji aplikační infrastrukturu běžící v Azure. V zájmu zajištění provozní kontinuity a dodržování předpisů se **Společnost C** rozhodne použít Azure Site Recovery k ochraně svých aplikací.

**Společnost C** spouští aplikace s veřejnými koncovými body a chce schopnost plynule přesměrovat provoz do jiné oblasti Azure v události po havárii. Metoda [Priorita](../traffic-manager/traffic-manager-configure-priority-routing-method.md) přenosu dat umožňuje **společnosti C** snadno implementovat tento vzor převzetí služeb při selhání.

Nastavení je následující:
- **Společnost C** vytvoří [profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Při použití metody směrování **priority** **Společnost C** vytvoří dva koncové body – **primární** pro zdrojovou oblast (Azure východní Asie) a **převzetí služeb při selhání** pro oblast obnovení (Azure jihovýchodní Asie). K **primární** je přiřazena priorita 1 a pro **převzetí služeb při selhání** je přiřazena Priorita 2.
- Vzhledem k tomu, že je **primární** koncový bod hostovaný v Azure, může být koncový bod jako koncový bod [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) .
- V Azure Site Recovery nemá lokalita Azure Recovery žádné virtuální počítače ani aplikace spuštěné před převzetím služeb při selhání. Koncový bod **převzetí služeb při selhání** se proto dá vytvořit jako [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncový bod.
- Ve výchozím nastavení je provoz uživatele směrován do aplikace zdrojové oblasti (Východní Asie), protože k tomuto koncovému bodu je přidružena nejvyšší priorita. Pokud je **primárním** koncovým bodem v pořádku, žádný provoz se nesměruje do oblasti obnovení.

![Azure-to-Azure před převzetím služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

V případě havárie může **Společnost C** aktivovat [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) a obnovovat své aplikace v oblasti Azure Recovery. Když Azure Traffic Manager zjistí, že primární koncový bod již není v pořádku, používá v odpovědi DNS automaticky koncový bod **převzetí služeb při selhání** a uživatelé se k aplikaci obnovili v oblasti obnovení Azure (jihovýchodní Asie).

![Azure-to-Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

V závislosti na podnikových požadavcích může **Společnost C** zvolit vyšší nebo nižší [četnost zjišťování](../traffic-manager/traffic-manager-monitoring.md) pro přepínání mezi zdroji a oblastmi obnovení a zajistit minimální prostoje pro uživatele.

Když je havárie obsaženo, může **Společnost C** navracet služby po obnovení z oblasti Azure Recovery do zdrojové oblasti azure pomocí Azure Site Recovery. Když teď Traffic Manager detekuje, že **primární** koncový bod je opět v pořádku, automaticky využívá **primární** koncový bod v odpovědích DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrana více oblastí podnikových aplikací

Globální podniky často zlepšují zkušenosti zákazníků tím, že přizpůsobují své aplikace pro účely regionálních potřeb. Omezení lokalizace a latence může vést k rozdělení aplikační infrastruktury mezi oblasti. Podniky jsou také vázány oblastními zákony na data v určitých oblastech a umožňují izolovat část své aplikační infrastruktury v rámci regionálních hranic.  

Podívejme se na příklad, kde **Společnost D** rozdělí své koncové body aplikace na samostatnou, která slouží jako Německo a zbytek světa. **Společnost D** používá k nastavení [geografické](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) metody směrování v Azure Traffic Manager. Veškerý provoz pocházející z Německa je směrován na **koncový bod 1** a veškerý provoz pocházející mimo Německo je směrován do **koncového bodu 2**.

Tento problém s tímto nastavením znamená, že pokud **koncový bod 1** z nějakého důvodu přestane fungovat, neexistuje žádné přesměrování provozu do **koncového bodu 2**. Provoz pocházející z Německa i nadále směřuje na **koncový bod 1** bez ohledu na stav koncového bodu, přičemž uživatelé mají přístup k aplikaci ve **společnosti D** bez přístupu. Podobně platí, že pokud **koncový bod 2** přejde do režimu offline, neexistuje žádné přesměrování provozu do **koncového bodu 1**.

![Aplikace s více oblastmi](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Aby se zabránilo tomuto problému a zajistila odolnost aplikace, **Společnost D** používá [vnořené Traffic Manager profily](../traffic-manager/traffic-manager-nested-profiles.md) s Azure Site Recovery. V nastavení vnořeného profilu se provoz nesměruje na jednotlivé koncové body, ale místo na jiné profily Traffic Manager. Tady je postup, jak tato instalace funguje:
- Místo použití geografického směrování s jednotlivými koncovými body používá **Společnost D** geografické směrování s Traffic Manager profily.
- Každý podřízený profil Traffic Manager využívá směrování **priority** s primárním a koncovým bodem obnovení, a proto vnoření směrování **priority** v rámci **geografického** směrování.
- Aby byla zajištěna odolnost aplikace, každá distribuce zatížení využívá Azure Site Recovery k převzetí služeb při selhání do oblasti obnovení založené v případě události havárie.
- Když nadřazený Traffic Manager obdrží dotaz DNS, je přesměrován na relevantní podřízenou Traffic Manager, která reaguje na dotaz s dostupným koncovým bodem.

![Aplikace s více oblastmi po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Pokud se například koncový bod v Německu (střední) nedaří, aplikace se dá rychle obnovit na Německo – severovýchod. Nový koncový bod zpracovává provoz pocházející z Německa s minimálními výpadky pro uživatele. Podobně výpadk koncového bodu v Západní Evropa může být zpracován pomocí obnovy úlohy aplikace do Severní Evropa a Azure Traffic Manager zpracovává přesměrování DNS na dostupný koncový bod.

Výše uvedené nastavení lze rozšířit tak, aby zahrnovalo, že je vyžadováno mnoho kombinací oblastí a koncových bodů. Traffic Manager umožňuje až 10 úrovní vnořených profilů a nepovoluje smyčky v rámci vnořené konfigurace.

## <a name="recovery-time-objective-rto-considerations"></a>Předpoklady pro čas obnovení (RTO)

Ve většině organizací se při přidávání a úpravách záznamů DNS využívá samostatný tým nebo někdo mimo organizaci. Díky tomu je úloha změny záznamů DNS velmi náročná. Čas potřebný k aktualizaci záznamů DNS jinými týmy nebo organizacemi, které spravují infrastrukturu DNS, se liší od organizace až po organizaci a ovlivňuje RTO aplikace.

Díky využití Traffic Manager můžete frontload práci nutnou k aktualizacím DNS. V době skutečného převzetí služeb při selhání se nevyžaduje žádná ruční nebo skriptovaná akce. Tento přístup pomáhá při rychlém přepínání (a proto snížení RTO) a také v případě výpadku nákladné chyby změn DNS v události havárie. V Traffic Manager se i krok navrácení služeb po obnovení automaticky, což by jinak bylo nutné spravovat samostatně.

Nastavení správného [intervalu zjišťování](../traffic-manager/traffic-manager-monitoring.md) prostřednictvím základních nebo krátkých kontrol stavu intervalu může výrazně RTO během převzetí služeb při selhání a omezit prostoje pro uživatele.

Navíc můžete optimalizovat hodnotu TTL (Time to Live) DNS pro profil Traffic Manager. Hodnota TTL je hodnota, pro kterou bude klient ukládat položku DNS do mezipaměti. V případě záznamu se DNS do rozsahu TTL nedotazuje dvakrát. K jednotlivým záznamům DNS je přidružena hodnota TTL. Omezením této hodnoty se vyhodnotí další dotazy DNS na Traffic Manager, ale můžou snížit RTO, protože zjistíte výpadky rychleji.

Hodnota TTL, kterou klient používá, se také nezvyšuje, pokud se zvyšuje počet překladačů DNS mezi klientem a autoritativním serverem DNS. Překladače DNS vypočítávají hodnotu TTL a předají se jenom na hodnotu TTL, která odráží uplynulý čas od chvíle, kdy byl záznam uložen v mezipaměti. Tím se zajistí, že se záznam DNS aktualizuje na klientovi po hodnotě TTL bez ohledu na počet překladačů DNS v řetězu.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o Traffic Manager [metod směrování](../traffic-manager/traffic-manager-routing-methods.md).
- Přečtěte si další informace o [vnořených Traffic Manager profilech](../traffic-manager/traffic-manager-nested-profiles.md).
- Přečtěte si další informace o [monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikace.
