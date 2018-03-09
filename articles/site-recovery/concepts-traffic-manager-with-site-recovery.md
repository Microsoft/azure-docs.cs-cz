---
title: "Azure Traffic Manageru službou Azure Site Recovery | Microsoft Docs"
description: "Popisuje, jak používat Azure Traffic Manageru službou Azure Site Recovery pro zotavení po havárii a migrace"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 8305a354588875926cab52a55d99d3a29bcfb509
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager se službou Azure Site Recovery

Azure Traffic Manager umožňuje řízení distribuce provozu napříč koncovými body vaší aplikace. Koncový bod je všechny internetové služby hostované uvnitř nebo mimo Azure.

Traffic Manager používá v systému DNS (Domain Name) pro přesměrování požadavků klientů nejvhodnější koncového bodu, na základě metodu směrování provozu a stav koncových bodů. Traffic Manager poskytuje řadu [metody směrování provozu](../traffic-manager/traffic-manager-routing-methods.md) a [koncový bod možnosti monitorování](../traffic-manager/traffic-manager-monitoring.md) tak, aby vyhovovala potřebám různé aplikace a modely automatické převzetí služeb při selhání. Klienti připojovat přímo k vybrané koncový bod. Traffic Manager není serveru proxy nebo brány a nezná provozu mezi klientem a služby předávání.

Tento článek popisuje, jak můžete kombinovat inteligentního směrování sledování provozu Azure s možnostmi migrace a zotavení po havárii výkonné Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>Místním nasazením a Azure převzetí služeb při selhání

U prvního scénáře, zvažte **společnosti A** má všechny jeho infrastrukturu aplikace spuštěna v jeho místní prostředí. Z pracovních důvodů kontinuitu a dodržování předpisů **společnosti A** rozhodne používat Azure Site Recovery k ochraně jeho aplikace.

**Společnosti A** běží aplikace s veřejné koncové body a chce schopnost bezproblémově přesměrování provozu na Azure v případě havárie. [s prioritou](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metoda směrování provozu v Azure Traffic Manager umožňuje společnosti A snadno implementovat tento vzor převzetí služeb při selhání.

Instalační program je následujícím způsobem:
- **Společnosti A** vytvoří [profil služby Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Použití **s prioritou** metody směrování **společnosti A** vytvoří dva koncové body – **primární** pro místní a **převzetí služeb při selhání** pro Azure. **Primární** je přiřazena priorita 1 a **převzetí služeb při selhání** je přiřazen s prioritou 2.
- Vzhledem k tomu **primární** koncový bod nachází mimo Azure, jako je vytvořen koncový bod [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncový bod.
- S Azure Site Recovery webu Azure nemá žádné virtuální počítače nebo aplikace spuštěné před převzetí služeb při selhání. Ano **převzetí služeb při selhání** koncový bod se také vytvoří jako **externí** koncový bod.
- Ve výchozím nastavení provoz uživatele přesměruje na místní aplikace, protože tohoto koncového bodu má nejvyšší prioritu s ním spojená. Žádný provoz se přesměruje, když se Azure **primární** koncový bod je v pořádku.

![Na místní do Azure před převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

V případě havárie, můžete aktivovat společnosti A [převzetí služeb při selhání](site-recovery-failover.md) do Azure a obnovit jeho aplikace na platformě Azure. Když Azure Traffic Manager rozpozná, že **primární** koncového bodu již není v pořádku, automaticky použije **převzetí služeb při selhání** koncového bodu v odpovědi DNS a uživatelé připojit k aplikaci obnovit na Azure.

![Na místní do Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

V závislosti na obchodní požadavky **společnosti A** můžete vybrat a vyšší nebo nižší [zjišťování frekvence](../traffic-manager/traffic-manager-monitoring.md) přepnout mezi místním nasazením a Azure v případě havárie, a pak zajistěte minimální dobou výpadku pro uživatele.

Pokud je obsažen po havárii, **společnosti A** můžete navrácení služeb po obnovení z Azure do jeho místní prostředí ([VMware](vmware-azure-failback.md) nebo [technologie Hyper-V](hyper-v-azure-failback.md)) pomocí Azure Site Recovery. Teď, když Traffic Manager rozpozná, že **primární** koncový bod je v pořádku znovu, automaticky využívá **primární** koncového bodu v jeho odpovědí DNS.

## <a name="on-premises-to-azure-migration"></a>Místní migrace Azure

Kromě zotavení po havárii, taky umožňuje Azure Site Recovery [migrace na Azure](migrate-overview.md). Pomocí Azure Site Recovery výkonné testovací převzetí služeb při selhání funkcí, můžete zákazníků bez ovlivnění jejich místní prostředí vyhodnocení výkonu aplikace na platformě Azure. A Pokud zákazníci jsou připravení migrovat, můžete zvolit k migraci celé zatížení dohromady, nebo můžete migrovat a škálovat postupně.

Azure Traffic Manager [vážená](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) metody směrování umožňuje přímé některé části příchozí provoz do Azure při odkazovat většinu do místního prostředí. Tento přístup může pomoci vyhodnocení měřítka výkonu, jak můžete dál zvýšit váhy přiřazené do Azure, jak migrovat informace a další úlohy do Azure.

Například **společnosti B** se rozhodne pro migraci v rámci fáze, přesunutí některých jeho aplikace prostředí a přitom zachovat rest na místě. Během počáteční fáze na místních počítačích po většinu prostředí je větší váhu přiřazený k místním prostředí. Správce provozu vrátí koncový bod podle váhy přiřazené koncové body k dispozici.

![Migrace na místní do Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Během migrace oba koncové body jsou aktivní a většina dat je přesměrováni na místním prostředí. Jak migrace pokračuje, větší váhu lze přiřadit ke koncovému bodu v Azure a nakonec místní koncový bod může být deaktivované po migraci.

## <a name="azure-to-azure-failover"></a>Převzetí služeb při selhání Azure do Azure

V tomto příkladu zvažte **společnosti C** má všechny jeho infrastruktury aplikací s Azure. Z pracovních důvodů kontinuitu a dodržování předpisů **společnosti C** rozhodne používat Azure Site Recovery k ochraně jeho aplikace.

**Společnosti C** běží aplikace s veřejné koncové body a chce schopnost bezproblémově přesměrovat provoz na jiné oblasti Azure v případě havárie. [s prioritou](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metoda směrování provozu umožňuje **společnosti C** snadno implementovat tento vzor převzetí služeb při selhání.

Instalační program je následujícím způsobem:
- **Společnosti C** vytvoří [profil služby Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Použití **s prioritou** metody směrování, **společnosti C** vytvoří dva koncové body – **primární** pro oblast zdroj (Azure východní Asie) a **převzetíslužebpřiselhání** pro oblast pro obnovení (Azure jihovýchodní Asie). **Primární** je přiřazena priorita 1 a **převzetí služeb při selhání** je přiřazen s prioritou 2.
- Vzhledem k tomu **primární** koncový bod je hostovaný v Azure, koncový bod může být jako [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) koncový bod.
- S Azure Site Recovery Azure site recovery nemá žádné virtuální počítače nebo aplikace spuštěné před převzetí služeb při selhání. Ano **převzetí služeb při selhání** koncový bod se dá vytvořit jako [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) koncový bod.
- Ve výchozím nastavení provoz uživatele přesměruje na aplikace zdroj oblast (východní Asie) jako tohoto koncového bodu má nejvyšší prioritu s ním spojená. Žádný provoz se přesměruje do oblasti obnovení, pokud **primární** koncový bod je v pořádku.

![Azure do Azure před převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

V případě havárie **společnosti C** můžete aktivovat [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) a obnovit jeho aplikace na obnovení oblast Azure. Když Azure Traffic Manager zjistí, že primární koncový bod již není v pořádku, automaticky použije **převzetí služeb při selhání** koncového bodu v odpovědi DNS a uživatelé připojit k aplikaci obnovit na obnovení (oblast Azure Jihovýchodní Asie).

![Azure do Azure po převzetí služeb při selhání](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

V závislosti na obchodní požadavky **společnosti C** můžete vybrat a vyšší nebo nižší [zjišťování frekvence](../traffic-manager/traffic-manager-monitoring.md) přepnout mezi oblastmi zdroje a obnovení, a pak zajistěte minimální dobou výpadku pro uživatele.

Pokud je obsažen po havárii, **společnosti C** můžete navrácení služeb po obnovení z oblasti Azure recovery ke zdroji oblast Azure pomocí Azure Site Recovery. Teď, když Traffic Manager rozpozná, že **primární** koncový bod je v pořádku znovu, automaticky využívá **primární** koncového bodu v jeho odpovědí DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrana více oblast podnikové aplikace

Globální podniky často zlepšovat prostředí zákazníků přizpůsobení svých aplikací k obsluze regionální potřebám. Lokalizace a snížení latence může vést k rozdělení v oblastech infrastruktury aplikací. Podniky jsou také svázaná s zákony místní data v určité oblasti a zvolte izolovat součástí infrastruktury svých aplikací v rámci regionální hranice.  

Zvažte příklad kde **společnosti D** se rozdělila své koncové body aplikace samostatně poskytovat Německu a zbytek na světě. **Společnosti D** využívá Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) metody směrování chcete nastavit tuto možnost. Přenosy pocházející z Německo směřuje na **koncový bod 1** a přenosy dat pocházejících mimo Německo nasměruje **koncový bod 2**.

Problém s tímto nastavením je, že pokud **koncový bod 1** přestane pracovat z jakéhokoliv důvodu neexistuje žádné přesměrování provozu **koncový bod 2**. Přenosy pocházející z Německo nadále přesměrováni na **koncový bod 1** bez ohledu na stav koncového bodu, a německé uživatelé bez přístupu k **společnosti D**na aplikaci. Podobně pokud **koncový bod 2** přejde do režimu offline, neexistuje žádný přesměrování provozu **koncový bod 1**.

![Aplikace s více oblast před](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Vyhněte se spuštěná na tento problém a zajistit odolnost aplikace **společnosti D** používá [vnořené profily Traffic Manageru](../traffic-manager/traffic-manager-nested-profiles.md) s Azure Site Recovery. V nastavení vnořených profilů není přenášená do jednotlivých koncových bodů, ale místo toho na další profily Traffic Manageru. Zde je, jak funguje tato instalace:
- Místo použití geografické směrování s jednotlivých koncových bodů, **společnosti D** používá geografické směrování s profily Traffic Manageru.
- Využívá jednotlivých podřízených profil služby Traffic Manager **s prioritou** směrování s primární a koncový bod obnovení, proto vnoření **s prioritou** směrování v rámci **Geographic** směrování.
- Povolit odolnost aplikace, využívá každý rozdělení pracovního vytížení Azure Site Recovery k převzetí služeb při selhání v oblasti obnovení na základě v případě událostí po havárii.
- Nadřazené Traffic Manager obdrží dotaz DNS, se přesměruje relevantní podřízené Traffic Manager, který odpoví na dotaz s koncový bod k dispozici.

![Aplikace s více oblast po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Například v případě selhání koncového bodu v Německu centrální aplikace rychle obnovit až Německo – severovýchod. Nový koncový bod zpracovává přenosy pocházející z Německo s minimální dobou výpadku pro uživatele. Podobně jako mohou být zpracována výpadku koncový bod v oblasti západní Evropa obnovení úlohy aplikace Severní Evropa, s Azure Traffic Manager zpracování, které DNS přesměruje na koncový bod k dispozici.

Výše uvedené nastavení můžete rozšířit, aby obsahovaly tolik kombinací oblasti a koncového bodu vyžaduje. Traffic Manager umožňuje až 10 úrovní vnořených profilů a nepovoluje smyčky v rámci vnořené konfigurace.

## <a name="recovery-time-objective-rto-considerations"></a>Aspekty čas cíl (RTO) obnovení

V většina organizací se zpracovává přidání nebo úprava záznamů DNS pomocí samostatný tým nebo pomocí někdo mimo organizaci. Díky tomu změna záznamy DNS, které jsou velmi náročné úlohy. Čas potřebný k aktualizaci záznamů DNS pomocí jiné týmy nebo organizace Správa infrastruktury služby DNS se liší podle organizace a má to dopad RTO aplikace.

S využitím Traffic Manager, můžete frontload práce potřebné pro aktualizace služby DNS. V době skutečné převzetí služeb při selhání není nutná žádná ruční nebo skriptované akce. Tento přístup pomáhá v rychlé přepínání (a proto snížit RTO) a také zabraňující nákladná časově náročné chyb DNS změnu v případě havárie. S nástrojem Traffic Manager i krok navrácení služeb po obnovení je automatizované, který byste jinak museli pracně spravovány samostatně.

Nastavení správné [zjišťování interval](../traffic-manager/traffic-manager-monitoring.md) prostřednictvím stavu základní nebo rychlou interval kontroly můžete výrazně snížilo RTO během převzetí služeb při selhání a zkrátit dobu prostojů při pro uživatele.

Kromě toho můžete optimalizovat dobu DNS Live (TTL) hodnotu pro profil služby Traffic Manager. Hodnota TTL je hodnota, pro kterou položku DNS by být mezipaměti klienta. Pro záznam nebude položit dotaz DNS dvakrát v rámci rozpětí TTL. Každý záznam DNS má hodnotu TTL s ním spojená. Nastavení této hodnoty výsledkem další dotazy DNS na Traffic Manager, ale může snížit RTO zjišťováním výpadků rychlejší.

TTL, které klient také nezvyšuje zvyšuje počet překladače služby DNS mezi klientem a autoritativní server DNS. Překladače služby DNS, počet ' Hodnota TTL a předejte pouze na hodnotu TTL, která odráží uplynulý čas, protože záznam se ukládá do mezipaměti. Zajistíte, že záznam DNS získá obnoven v klientovi po TTL, bez ohledu na počet překladače služby DNS v řetězu.

## <a name="next-steps"></a>Další postup
- Další informace o Traffic Manager [metody směrování](../traffic-manager/traffic-manager-routing-methods.md).
- Další informace o [vnořené profily Traffic Manageru](../traffic-manager/traffic-manager-nested-profiles.md).
- Další informace o [monitorování koncového bodu](../traffic-manager/traffic-manager-monitoring.md).
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
