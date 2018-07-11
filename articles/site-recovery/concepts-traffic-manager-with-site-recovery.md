---
title: Azure Traffic Manager se službou Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak používat Azure Traffic Manageru s Azure Site Recovery pro zotavení po havárii a migrace
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 0be013a1b8d2c619d58034157240eafb241c4e59
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919032"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager se službou Azure Site Recovery

Azure Traffic Manager umožňuje řídit distribuci provozu mezi koncových bodů vaší aplikace. Koncový bod je všechny internetová služba hostovaná uvnitř nebo mimo Azure.

Traffic Manager se používá v systému DNS (Domain Name) přesměrovává klientské žádosti na nejvhodnější koncové na základě metody směrování provozu a stavu koncových bodů. Traffic Manager poskytuje řadu [metody směrování provozu](../traffic-manager/traffic-manager-routing-methods.md) a [možnosti monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md) tak, aby vyhovovaly potřebám různých aplikací a modely automatické převzetí služeb při selhání. Klienti připojovat přímo k vybraný koncový bod. Traffic Manager není proxy nebo brány a nezná provoz mezi klientem a služby předávání.

Tento článek popisuje, jak můžete kombinovat inteligentního směrování sledování provozu Azure s Azure Site Recovery efektivní zotavení po havárii a možností migrace.

## <a name="on-premises-to-azure-failover"></a>Místní převzetí služeb při selhání Azure

U prvního scénáře, zvažte **firmy A** , který má všechny své infrastruktury aplikace spuštěna v místním prostředí. Obchodní kontinuity a dodržování předpisů z důvodů **firmy A** rozhodne pomocí Azure Site Recovery můžete ochránit své aplikace.

**Firmy A** běží aplikace s veřejnými koncovými body a chce, aby možnost bez problémů přesměrovat provoz do Azure v případě havárie. [Priority](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metody směrování provozu ve službě Azure Traffic Manager umožňuje společnosti A snadno implementace tohoto modelu převzetí služeb při selhání.

Instalační program je následujícím způsobem:
- **Firmy A** vytvoří [profil služby Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Využívají **Priority** metodu směrování, **firmy A** vytvoří dva koncové body – **primární** pro místní a **převzetí služeb při selhání** pro Azure. **Primární** je přiřazené Priority 1 a **převzetí služeb při selhání** je přiřazené Priority 2.
- Protože **primární** koncový bod je hostovaný mimo Azure, jako je vytvořen koncový bod [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncového bodu.
- Pomocí Azure Site Recovery Azure web nemá žádné virtuální počítače nebo aplikace spuštěné před převzetí služeb při selhání. Proto **převzetí služeb při selhání** jako se taky vytvoří koncový bod **externí** koncový bod.
- Ve výchozím nastavení uživatele provoz se směřuje do místní aplikace protože tohoto koncového bodu má nejvyšší prioritu, s ním spojená. Žádný provoz se směřuje na Azure, pokud **primární** koncový bod je v pořádku.

![V místní – Azure před převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

V události po havárii mohou firmy A aktivovat [převzetí služeb při selhání](site-recovery-failover.md) do Azure a obnovit své aplikace v Azure. Když Azure Traffic Manager zjistí, že **primární** koncového bodu již není v pořádku, automaticky použije **převzetí služeb při selhání** koncový bod v odpovědi DNS a uživatelé připojit k aplikaci obnovit na Azure.

![V místní – Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

V závislosti na obchodních požadavcích **firmy A** můžete vybrat a vyšší nebo nižší [zjišťování frekvence](../traffic-manager/traffic-manager-monitoring.md) přepínání mezi místní do Azure v události po havárii a zajistit minimální výpadku pro uživatele.

Pokud je obsažen po havárii, **firmy A** můžete navrácení služeb po obnovení z Azure v místním prostředí ([VMware](vmware-azure-failback.md) nebo [Hyper-V](hyper-v-azure-failback.md)) pomocí služby Azure Site Recovery. Teď, když Traffic Manager zjistí, že **primární** koncového bodu je opět v pořádku, automaticky využívá **primární** koncový bod v odpovědi DNS.

## <a name="on-premises-to-azure-migration"></a>Místní migrace do Azure

Kromě zotavení po havárii, Azure Site Recovery umožňuje [migrace do Azure](migrate-overview.md). Pomocí Azure Site Recovery výkonné testovací převzetí služeb při selhání funkcí, Zákazníci můžete vyhodnotit výkon aplikace v Azure bez ovlivnění jejich v místním prostředí. A jakmile jsou připravené k migraci zákazníků, můžete zvolit společně migraci celé úlohy nebo vyberte k migraci a postupně škálovat.

Azure Traffic Manager [vážená](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) metodu směrování, která slouží ke směrování některá část příchozí provoz do Azure při směrování většinou v místním prostředí. Tento přístup může pomoct posoudit škálování výkonu, jak můžete dál zvýšit váhy přiřazené do Azure při migraci víc úloh do Azure.

Například **společnosti B** zvolí ve fázích, přesunutí některých svého prostředí aplikace při zachování rest místní migrace. V počátečních fázích po většinu prostředí v místním větší váhu se přiřadí v místním prostředí. Traffic manager vrátí koncový bod podle váhy přiřazené k dispozici koncové body.

![Migrace na místní – Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Během migrace oba koncové body jsou aktivní a většinu provoz se směřuje do místního prostředí. Jak pokračuje migrace větší váhu se dají přiřadit ke koncovému bodu v Azure a nakonec místní koncový bod může být deaktivované po migraci.

## <a name="azure-to-azure-failover"></a>Převzetí služeb při selhání Azure do Azure

V tomto příkladu zvažte **C společnosti** , který má všechny jeho infrastruktury aplikací s Azure. Obchodní kontinuity a dodržování předpisů z důvodů **C společnosti** rozhodne pomocí Azure Site Recovery můžete ochránit své aplikace.

**Společnost C** běží aplikace s veřejnými koncovými body a chce, aby se schopnost bezproblémově přesměrování provozu do jiné oblasti Azure v případě havárie. [Priority](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metody směrování provozu umožňuje **C společnosti** snadno implementace tohoto modelu převzetí služeb při selhání.

Instalační program je následujícím způsobem:
- **Společnost C** vytvoří [profil služby Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Využívají **Priority** metodu směrování, **C společnosti** vytvoří dva koncové body – **primární** pro zdrojové oblasti (východní Asie Azure) a **převzetíslužebpřiselhání** pro oblasti pro zotavení (Azure jihovýchodní Asie). **Primární** je přiřazené Priority 1 a **převzetí služeb při selhání** je přiřazené Priority 2.
- Protože **primární** koncový bod je hostovaný v Azure, koncový bod může být jako [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) koncového bodu.
- Azure Site Recovery Azure site recovery nemá žádné virtuální počítače nebo aplikace spuštěné před převzetí služeb při selhání. Tedy **převzetí služeb při selhání** koncového bodu je možné vytvořit jako [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncového bodu.
- Ve výchozím nastavení uživatele provoz se směřuje do aplikace zdrojové oblasti (východní Asie) podle tohoto koncového bodu má nejvyšší prioritu, s ním spojená. Žádný provoz se směřuje do oblasti pro zotavení, pokud **primární** koncový bod je v pořádku.

![Azure do Azure před převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

V události po havárii **C společnosti** můžete aktivovat [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) a obnovit své aplikace na obnovení oblasti Azure. Když Azure Traffic Manager zjistí, že primární koncový bod již není v pořádku, automaticky použije **převzetí služeb při selhání** koncový bod v odpovědi DNS a uživatelé připojit k aplikaci v oblasti Azure (obnovení Jihovýchodní Asie).

![Azure do Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

V závislosti na obchodních požadavcích **C společnosti** můžete vybrat a vyšší nebo nižší [zjišťování frekvence](../traffic-manager/traffic-manager-monitoring.md) přepínání mezi oblastmi zdroje a obnovení a zajistit minimální výpadku pro uživatele.

Pokud je obsažen po havárii, **C společnosti** můžete navrácení služeb po obnovení z obnovení oblasti Azure do zdrojové oblasti Azure pomocí Azure Site Recovery. Teď, když Traffic Manager zjistí, že **primární** koncového bodu je opět v pořádku, automaticky využívá **primární** koncový bod v odpovědi DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrana více oblastí podnikových aplikací

Globální podniky, které často vylepšení zkušeností uživatelů přizpůsobením sloužit místní potřeby svých aplikací. Lokalizace a snížení latence může vést k aplikační infrastruktury rozdělit mezi oblastmi. Podniky jsou také vázané zákony místní data v určitých oblastech a zvolte izolovat infrastrukturu aplikace v rámci hranice místní část.  

Zvažte následující příklad kde **společnosti D** rozdělil jeho aplikačními koncovými body k poskytování samostatně Německo a zbytek světa. **Společnost D** využívá Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) metodu směrování pro toto nastavení. Veškerý provoz pocházející z Německa směřuje na **1 koncový bod** a veškerý provoz přicházející odjinud než z Německa směřuje na **koncový bod 2**.

Problém s tímto nastavením je, že pokud **1 koncový bod** přestane pracovat z nějakého důvodu není žádná přesměrování provozu do **koncový bod 2**. Přenos dat pocházejících z Německa nadále přesměrováni na **1 koncový bod** bez ohledu na stav koncového bodu, byste museli opustit německé uživatelé, kteří nemají přístup k **společnosti D**vaší aplikace. Podobně pokud **koncový bod 2** přejde do režimu offline, neexistuje žádný přesměrování provozu do **1 koncový bod**.

![Aplikace v několika oblastech před](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Pokud chcete zabránit spuštění na tento problém a zajistit odolnost aplikace **společnosti D** používá [vnořené profily Traffic Manageru](../traffic-manager/traffic-manager-nested-profiles.md) pomocí Azure Site Recovery. V nastavení vnořeného profilu není přenášená do jednotlivých koncových bodů, ale místo toho na ostatní profily Traffic Manageru. Zde je, jak toto nastavení funguje:
- Místo využívání geografické směrování s jednotlivé koncové body **společnosti D** používá geografické směrování s profily Traffic Manageru.
- Využívá jednotlivých podřízených, profil služby Traffic Manager **Priority** směrování s primární a koncový bod obnovení, proto vnoření **Priority** směrování v rámci **Geographic** směrování.
- Povolit odolnost aplikace, každé distribuci úloh využívá Azure Site Recovery k převzetí služeb při selhání v oblasti obnovení na základě v případě události po havárii.
- Když nadřazený Traffic Manager obdrží dotaz DNS, se přesměruje na příslušné podřízené Traffic Manageru, který odpoví dotaz dostupný koncový bod.

![Aplikace v několika oblastech po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Například pokud dojde k selhání koncového bodu v Německo – střed, aplikace můžete rychle obnovit pro Německo – severovýchod. Nový koncový bod zpracovává přenosy pocházející z Německa s minimálními výpadky pro uživatele. Podobně může být zpracována výpadku koncový bod v oblasti západní Evropa obnovení úlohy aplikace do Severní Evropa, s Azure Traffic Manager zpracování, které DNS přesměruje dostupný koncový bod.

Výše uvedené nastavení dají rozšířit, aby zahrnují tolik kombinací oblasti a koncový bod vyžaduje. Traffic Manager umožňuje až do 10 úrovně vnořené profily a nepovoluje smyčky v rámci vnořených konfigurace.

## <a name="recovery-time-objective-rto-considerations"></a>Aspekty zotavení cíle času (RTO)

Ve většině organizací se zpracovává přidání nebo úprava záznamů DNS samostatný tým nebo někým mimo organizaci. Díky tomu Změna záznamů DNS velmi náročné úlohy. Čas potřebný k aktualizaci záznamů DNS pomocí jiných týmů nebo organizací správu infrastruktury DNS se liší mezi organizacemi a ovlivňuje RTO aplikace.

S využitím Traffic Manageru, můžete frontload práce potřebné pro aktualizace služby DNS. V době skutečné převzetí služeb při selhání není nutná žádná ruční nebo skriptované akce. Tento přístup pomáhá v rychlé přepínání (a tím snížit RTO) a také vyhnout nákladným časově náročné DNS změnu chybám v případě havárie. S Traffic Managerem i krok navrácení služeb po obnovení je automatické, který byste jinak museli spravovat samostatně.

Nastavení správné [interval testování](../traffic-manager/traffic-manager-monitoring.md) prostřednictvím služby health základní kurzory a rychlými interval kontroly můžete značně snížilo RTO během převzetí služeb při selhání a snížit prostoje pro uživatele.

Můžete také optimalizovat DNS Time to Live (TTL) hodnotu pro profil Traffic Manageru. Hodnota TTL je hodnota, pro kterou záznam DNS by uložit do mezipaměti klienta. Pro záznam nebude se dvakrát v rozpětí TTL dotazovat DNS. Každý záznam DNS má hodnotu TTL s ním spojená. Nastavení této hodnoty výsledkem další dotazy DNS pro Traffic Manager, ale může omezit RTO zjištěním výpadku rychlejší.

Interval TTL, ZÍSKÁ postihla klient také nezvyšuje počet překladače DNS mezi klientem a autoritativní server DNS se zvyšuje. Překladače DNS "odpočet' Hodnota TTL a předat pouze na hodnotu TTL, který odráží uplynulého času, protože záznam ukládá do mezipaměti. Tím se zajistí, že záznam DNS se aktualizuje na straně klienta a po hodnotu TTL, bez ohledu na počet překladače DNS v řetězci.

## <a name="next-steps"></a>Další postup
- Další informace o Traffic Manageru [metod směrování](../traffic-manager/traffic-manager-routing-methods.md).
- Další informace o [vnořené profily Traffic Manageru](../traffic-manager/traffic-manager-nested-profiles.md).
- Další informace o [monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
