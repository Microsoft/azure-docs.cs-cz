---
title: Azure Traffic Manager s azure site recovery | Dokumenty společnosti Microsoft
description: Popisuje, jak používat Azure Traffic Manager s Azure Site Recovery pro zotavení po havárii a migraci
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60947742"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager se službou Azure Site Recovery

Azure Traffic Manager umožňuje řídit distribuci provozu mezi koncovými body aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager používá dns systém DNS k přesměrování požadavků klientů na nejvhodnější koncový bod na základě metody směrování provozu a stavu koncových bodů. Traffic Manager poskytuje celou řadu [metod směrování provozu](../traffic-manager/traffic-manager-routing-methods.md) a [možností monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Klienti se připojí k vybranému koncovému bodu přímo. Traffic Manager není proxy nebo brána a nevidí přenos y procházející mezi klientem a službou.

Tento článek popisuje, jak můžete kombinovat inteligentní směrování Azure Traffic Monitor s výkonnými možnostmi zotavení po havárii azure site recovery a migrace.

## <a name="on-premises-to-azure-failover"></a>Místní převzetí služeb při selhání azure

Pro první scénář zvažte **společnost A,** která má všechny své aplikační infrastruktury spuštěné v místním prostředí. Z důvodů kontinuity provozu a dodržování předpisů se **společnost A** rozhodne používat Azure Site Recovery k ochraně svých aplikací.

**Společnost A** spouštěla aplikace s veřejnými koncovými body a chce možnost bezproblémově přesměrovat provoz do Azure v případě havárie. [Metoda směrování](../traffic-manager/traffic-manager-configure-priority-routing-method.md) provozu priority ve Službě Azure Traffic Manager umožňuje společnosti A snadno implementovat tento vzor převzetí služeb při selhání.

Nastavení je následující:
- **Společnost A** vytvoří [profil traffic managera](../traffic-manager/traffic-manager-create-profile.md).
- S využitím metody směrování **priority** **vytvoří společnost A** dva koncové body – **primární** pro místní a převzetí služeb **při selhání** pro Azure. **Primární** je přiřazena priorita 1 a **převzetí služeb při selhání** je přiřazena priorita 2.
- Vzhledem k tomu, **že primární** koncový bod je hostovaný mimo Azure, koncový bod se vytvoří jako [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncový bod.
- S Azure Site Recovery web nemá web Azure žádné virtuální počítače nebo aplikace spuštěné před převzetíslužeb služeb při selhání. Koncový bod **převzetí služeb při selhání** se tedy také vytvoří jako **externí** koncový bod.
- Ve výchozím nastavení je provoz uživatelů směrován na místní aplikaci, protože tento koncový bod má nejvyšší prioritu, která je s ním spojena. Žádný provoz je směrována do Azure, pokud **primární** koncový bod je v pořádku.

![Místní Azure před převzetím služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

V případě havárie může společnost A aktivovat [převzetí služeb při selhání](site-recovery-failover.md) do Azure a obnovit své aplikace v Azure. Když Azure Traffic Manager zjistí, že **primární** koncový bod už není v pořádku, automaticky použije koncový bod **převzetí služeb při selhání** v odpovědi DNS a uživatelé se připojí k aplikaci obnovené v Azure.

![Místní Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

V závislosti na obchodních požadavcích může **společnost A** zvolit vyšší nebo nižší [frekvenci zjišťování,](../traffic-manager/traffic-manager-monitoring.md) aby přepnula mezi místním prostředím do Azure v případě havárie a zajistila minimální prostoje pro uživatele.

Když je problém omezen, **společnost A** může navrácení služeb po obnovení z Azure do místního prostředí[(VMware](vmware-azure-failback.md) nebo [Hyper-V)](hyper-v-azure-failback.md)pomocí Azure Site Recovery. Když nyní Traffic Manager zjistí, že **primární** koncový bod je opět v pořádku, automaticky využívá **primární** koncový bod ve svých odpovědích DNS.

## <a name="on-premises-to-azure-migration"></a>Místní migrace do Azure

Kromě zotavení po havárii azure site recovery také umožňuje [migrace do Azure](migrate-overview.md). Pomocí výkonných možností převzetí služeb při selhání testu Azure Site Recovery můžou zákazníci vyhodnocovat výkon aplikací v Azure, aniž by to ovlivnilo jejich místní prostředí. A když jsou zákazníci připraveni k migraci, mohou se rozhodnout migrovat celé úlohy společně nebo se rozhodnout migrovat a škálovat postupně.

Azure Traffic Manager [je vážený](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) směrování metoda slouží k přímé některé části příchozíprovoz do Azure při směrování většinu do místního prostředí. Tento přístup vám může pomoci posoudit výkon škálování, protože můžete pokračovat ve zvyšování váhy přiřazené k Azure při migraci stále více a více úloh do Azure.

**Například společnost B** se rozhodne migrovat ve fázích, přesunutí některé jeho aplikační prostředí při zachování zbytek v místním prostředí. Během počátečních fází, kdy je většina prostředí místní, je místnímprostředí přiřazena větší hmotnost. Traffic manager vrátí koncový bod na základě vahou přiřazených k dostupným koncovým bodům.

![Migrace mezi místními a virtuálními zařízeními a Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Během migrace jsou aktivní oba koncové body a většina provozu je směrována do místního prostředí. Jak migrace pokračuje, větší váhu lze přiřadit ke koncovému bodu v Azure a nakonec místní koncový bod lze deaktivovat po migraci.

## <a name="azure-to-azure-failover"></a>Převzetí služeb při selhání Azure do Azure

V tomto příkladu zvažte **společnost C,** která má všechny své aplikační infrastruktury se systémem Azure. Z důvodů kontinuity provozu a dodržování předpisů se **společnost C** rozhodne používat Azure Site Recovery k ochraně svých aplikací.

**Společnost C** spouštěla aplikace s veřejnými koncovými body a chce možnost bezproblémově přesměrovat provoz do jiné oblasti Azure v případě havárie. [Metoda směrování](../traffic-manager/traffic-manager-configure-priority-routing-method.md) provozu priority umožňuje společnosti **C** snadno implementovat tento vzor převzetí služeb při selhání.

Nastavení je následující:
- **Společnost C** vytvoří [profil traffic managera](../traffic-manager/traffic-manager-create-profile.md).
- S využitím metody směrování **priority** **vytvoří společnost C** dva koncové body – **primární** pro zdrojovou oblast (Azure East Asia) a převzetí služeb **při selhání** pro oblast obnovení (Azure Jihovýchodní Asie). **Primární** je přiřazena priorita 1 a **převzetí služeb při selhání** je přiřazena priorita 2.
- Vzhledem k tomu, **že primární** koncový bod je hostovaný v Azure, koncový bod může být jako koncový bod [Azure.](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)
- S Azure Site Recovery, obnovení webu Azure nemá žádné virtuální počítače nebo aplikace spuštěné před převzetí služeb při selhání. Koncový bod **převzetí služeb při selhání** lze tedy vytvořit jako [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncový bod.
- Ve výchozím nastavení je provoz uživatelů směrován do zdrojové oblasti (východní Asie) aplikace jako koncový bod má nejvyšší prioritu s ním spojené. Žádný provoz je směrována do oblasti **obnovení,** pokud primární koncový bod je v pořádku.

![Azure-to-Azure před převzetím služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

V případě havárie **může společnost C** aktivovat převzetí služeb při [selhání](azure-to-azure-tutorial-failover-failback.md) a obnovit své aplikace v oblasti Azure pro obnovení. Když Azure Traffic Manager zjistí, že primární koncový bod už není v pořádku, automaticky použije koncový bod **převzetí služeb při selhání** v odpovědi DNS a uživatelé se připojí k aplikaci obnovené v oblasti Azure pro obnovení (jihovýchodní Asie).

![Azure-to-Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

V závislosti na obchodních požadavcích může **společnost C** zvolit vyšší nebo nižší [frekvenci zjišťování](../traffic-manager/traffic-manager-monitoring.md) pro přepínání mezi zdrojovými oblastmi a oblastmi obnovení a zajistit minimální prostoje pro uživatele.

Když je problém omezen, **společnost C** můžete navrácení služeb po obnovení z oblasti Azure pro obnovení do oblasti source Azure pomocí Azure Site Recovery. Když nyní Traffic Manager zjistí, že **primární** koncový bod je opět v pořádku, automaticky využívá **primární** koncový bod ve svých odpovědích DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrana podnikových aplikací pro více oblastí

Globální podniky často zlepšují zákaznickou zkušenost tím, že přizpůsobují své aplikace regionálním potřebám. Lokalizace a snížení latence může vést k rozdělení aplikační infrastruktury napříč oblastmi. Podniky jsou také vázány regionálními zákony o datech v určitých oblastech a rozhodnou se izolovat část své aplikační infrastruktury v rámci regionálních hranic.  

Podívejme se na příklad, kdy **společnost D** rozdělila své koncové body aplikace tak, aby sloužily samostatně Německu a zbytku světa. **Společnost D** využívá azure traffic manageru [geografické](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) směrování metoda k nastavení tohoto nastavení. Veškerý provoz pocházející z Německa je směrován do **koncového bodu 1** a veškerý provoz pocházející mimo Německo je směrován na **koncový bod 2**.

Problém s tímto nastavením je, že pokud **koncový bod 1** přestane fungovat z nějakého důvodu, neexistuje žádné přesměrování provozu na koncový bod **2**. Provoz pocházející z Německa je i nadále směrován na **koncový bod 1** bez ohledu na stav koncového bodu, takže němečtí uživatelé nemají přístup k **aplikaci společnosti D.** Podobně pokud **koncový bod 2** přejde do offline, neexistuje žádné přesměrování provozu na **koncový bod 1**.

![Aplikace pro více oblastí před](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Aby se zabránilo spuštění do tohoto problému a zajistit odolnost aplikace, **společnost D** používá [vnořené profily Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md) s Azure Site Recovery. V nastavení vnořeného profilu není provoz směrován na jednotlivé koncové body, ale na jiné profily Traffic Manageru. Toto nastavení funguje takto:
- Namísto využití geografického směrování s jednotlivými koncovými body používá **společnost D** geografické směrování s profily Traffic Manageru.
- Každý podřízený profil Traffic Manageru využívá **směrování priorit** s primárním koncovým bodem a koncovým bodem obnovení, a proto vnoření **směrování priority** v rámci **geografického** směrování.
- Chcete-li povolit odolnost proti chybám aplikace, každá distribuce úloh využívá Azure Site Recovery k převzetí služeb při selhání do oblasti obnovení založené v případě události po havárii.
- Když nadřazený Traffic Manager obdrží dotaz DNS, je přesměrován na příslušný podřízený Traffic Manager, který odpoví na dotaz s dostupným koncovým bodem.

![Aplikace pro více oblastí po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Například pokud koncový bod v Německu Central selže, aplikace lze rychle obnovit do Německa severovýchod. Nový koncový bod zpracovává provoz y pocházející z Německa s minimálními prostoji pro uživatele. Podobně výpadek koncového bodu v západní Evropě lze zpracovat obnovením zatížení aplikace do severní Evropy, s Azure Traffic Manager zpracování přesměrování DNS do dostupného koncového bodu.

Výše uvedené nastavení lze rozšířit tak, aby zahrnovalo tolik požadovaných kombinací oblastí a koncových bodů. Traffic Manager umožňuje až 10 úrovní vnořených profilů a nepovoluje smyčky v rámci vnořené konfigurace.

## <a name="recovery-time-objective-rto-considerations"></a>Aspekty cíle doby zotavení (RTO)

Ve většině organizací je přidávání nebo úprava záznamů DNS zpracovávána samostatným týmem nebo jiným uživatelem mimo organizaci. To dělá úkol měnit záznamy DNS velmi náročné. Doba, kterou je třeba integrovat jinýmtýmů nebo organizací spravujícím infrastrukturu DNS, se v jednotlivých organizacích liší a má vliv na rto aplikace.

Využitím Traffic Manager, můžete předpětí práce potřebné pro aktualizace DNS. V době skutečného převzetí služeb při selhání není vyžadována žádná ruční nebo skriptovaná akce. Tento přístup pomáhá při rychlém přepínání (a tím i snížení RTO) a také při předcházení nákladným časově náročným chybám změny DNS v případě katastrofy. S Traffic Manager, i krok navrácení služeb po obnovení je automatizován, které by jinak musely být spravovány samostatně.

Nastavení správného [intervalu zjišťování](../traffic-manager/traffic-manager-monitoring.md) prostřednictvím základních nebo rychlých kontrol stavu intervalu může výrazně snížit RTO během převzetí služeb při selhání a snížit prostoje pro uživatele.

Můžete také optimalizovat hodnotu DNS Time to Live (TTL) pro profil Traffic Manager. TTL je hodnota, pro kterou by byla položka DNS uložena do mezipaměti klientem. Pro záznam dns by neměl být dotazován dvakrát v rámci rozsahu TTL. Ke každému záznamu DNS je přidružena ttl. Snížení této hodnoty má za následek další dotazy DNS na Traffic Manager, ale může snížit RTO rychlejším zjišťováním výpadků.

TTL zkušenosti klienta také nezvýší, pokud se zvýší počet překladačů DNS mezi klientem a autoritativní server DNS. Překladače DNS 'odpočítávat' TTL a předávat pouze hodnotu TTL, která odráží uplynulý čas od uložení záznamu do mezipaměti. Tím zajistíte, že záznam DNS se aktualizuje na straně klienta po TTL, bez ohledu na počet překladačů DNS v řetězci.

## <a name="next-steps"></a>Další kroky
- Další informace o [metodách směrování](../traffic-manager/traffic-manager-routing-methods.md)traffic manageru .
- Další informace o [vnořených profilech Traffic Manageru](../traffic-manager/traffic-manager-nested-profiles.md).
- Další informace o [monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikací.
