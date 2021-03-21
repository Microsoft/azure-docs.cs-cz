---
title: Zotavení po havárii pomocí Azure DNS a Traffic Manager | Microsoft Docs
description: Přehled řešení pro zotavení po havárii pomocí Azure DNS a Traffic Manager
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234150"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Zotavení po havárii s využitím Azure DNS a Traffic Manageru

Zotavení po havárii se zaměřuje na obnovení z vážné ztráty funkčnosti aplikace. Aby bylo možné zvolit řešení pro zotavení po havárii, musí vlastníci obchodních a technologických funkcí nejprve určit úroveň funkčnosti, která je vyžadována během havárie, jako je například nedostupné, částečně dostupná prostřednictvím omezené funkčnosti nebo plně dostupná.
Většina podnikových zákazníků vybírá architekturu s více oblastmi pro odolnost proti selhání aplikace nebo úrovně infrastruktury. Zákazníci si můžou v poli Quest vybrat několik přístupů, aby dosáhli převzetí služeb při selhání a vysokou dostupnost prostřednictvím redundantní architektury. Tady jsou některé z oblíbených přístupů:

- **Aktivní – pasivní s studeným pohotovostním režimem**: v tomto řešení pro převzetí služeb při selhání nejsou virtuální počítače a další zařízení běžící v pohotovostním režimu aktivní, dokud nebudete potřebovat převzetí služeb při selhání. Provozní prostředí se ale replikuje ve formě záloh, imagí virtuálních počítačů nebo šablon Správce prostředků do jiné oblasti. Tento mechanismus převzetí služeb při selhání je cenově výhodnější, ale trvá déle, než se dokončí úplné převzetí služeb při selhání.
 
    ![Aktivní/pasivní s studeným pohotovostním režimem](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Obrázek – aktivní/pasivní s konfigurací zotavení po havárii v pohotovostním režimu*

- **Aktivní/pasivní s pilotním světlem**: v tomto řešení pro převzetí služeb při selhání je pohotovostní prostředí nastavené s minimální konfigurací. Instalační program má pouze nezbytné služby, které jsou spuštěny pro podporu pouze minimální a kritické sady aplikací. V jeho nativním formátu může tento scénář provádět jenom minimální funkce, ale může škálovat a vytvářet další služby, aby se při převzetí služeb při selhání provádělo hromadné zatížení v produkčním prostředí.
    
    ![Aktivní/pasivní s pilotním světlem](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Obrázek: aktivní/pasivní s pilotní konfigurací – konfigurace zotavení po havárii*

- **Aktivní/pasivní s teplým pohotovostním režimem**: v tomto řešení převzetí služeb při selhání je pohotovostní oblast předem zahřívání a je připravená na základní zatížení, automatické škálování je zapnuté a všechny instance jsou v provozu. Toto řešení se neškáluje, aby bylo možné provádět úplné provozní zatížení, ale je funkční a všechny služby jsou spuštěné. Toto řešení je rozšířenou verzí přístupového světla pilotního přístupu.
    
    ![Aktivní/pasivní s teplým pohotovostním režimem](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Obrázek: aktivní/pasivní s konfigurací záložního pohotovostního režimu zotavení po havárii*
    
Další informace o převzetí služeb při selhání a vysoké dostupnosti najdete v tématu [zotavení po havárii pro aplikace Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Plánování architektury zotavení po havárii

Existují dvě technické aspekty při nastavení architektury zotavení po havárii:
-  Použití mechanismu nasazení k replikaci instancí, dat a konfigurací mezi primárními a pohotovostními prostředími. Tento typ zotavení po havárii se dá nativně provést prostřednictvím Azure Site-Recovery přes Microsoft Azure partnerská zařízení/služby, jako je Veritas nebo NetApp. 
- Vývoj řešení pro přesměrování síťového nebo webového provozu z primární lokality do pohotovostní lokality. Tento typ zotavení po havárii můžete dosáhnout prostřednictvím Azure DNS, Azure Traffic Manager (DNS) nebo globálních nástrojů pro vyrovnávání zatížení třetích stran.

Tento článek se týká pouze přístupů přes přesměrování sítě a webového provozu. Pokyny k nastavení Azure Site Recovery najdete v dokumentaci k [Azure Site Recovery](../site-recovery/index.yml).
DNS je jedním z nejúčinnějších mechanismů pro směrování síťového provozu, protože služba DNS je často globální a externě připojená k datovému centru a je izolovaná od selhání na úrovni regionu nebo oblasti dostupnosti (AZ). Jedním z nich může být mechanismus převzetí služeb při selhání založený na DNS a v Azure, takže dvě služby DNS můžou být stejné v některém Azure DNS (autoritativní DNS) a Azure Traffic Manager (směrování inteligentních přenosů na základě DNS). 

Je důležité pochopit několik konceptů služby DNS, které jsou široce používány pro diskuzi o řešeních, která jsou uvedená v tomto článku:
- **Záznam DNS a** – záznamy jsou ukazatele, které ukazují doménu na adresu IPv4. 
- **CNAME nebo kanonický název** – tento typ záznamu slouží k odkazování na jiný záznam DNS. CNAME nereaguje s IP adresou, ale místo ukazatele na záznam, který obsahuje IP adresu. 
- **Vážené směrování** – jedna se může rozhodnout, že se má k koncovým bodům služby přidružit váha, a pak distribuovat provoz na základě přiřazených vah. Tato metoda směrování je jedním ze čtyř mechanismů směrování provozu dostupných v rámci Traffic Manager. Další informace najdete v tématu [vážený způsob směrování](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Priorita směrování** – priorita směrování závisí na kontrolách stavu koncových bodů. Služba Azure Traffic Manager ve výchozím nastavení odesílá veškerý provoz do koncového bodu s nejvyšší prioritou a při selhání nebo havárie Traffic Manager směruje provoz do sekundárního koncového bodu. Další informace najdete v tématu [Priorita metody směrování](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Ruční převzetí služeb při selhání pomocí Azure DNS
Řešení Azure DNS ručního převzetí služeb při selhání pro zotavení po havárii používá standardní mechanismus DNS pro převzetí služeb při selhání záložní lokality. Ruční možnost prostřednictvím Azure DNS funguje nejlépe při použití ve spojení s pohotovostním nebo zkušebním přístupem. 

![Ruční převzetí služeb při selhání pomocí Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Obrázek – ruční převzetí služeb při selhání pomocí Azure DNS*

Předpoklady pro řešení jsou:
- Primární i sekundární koncové body mají statické IP adresy, které se nemění často. Řekněme, že je primární lokalita, že je IP adresa 100.168.124.44 a IP adresa pro sekundární lokalitu je 100.168.124.43.
- Zóna Azure DNS existuje jak pro primární, tak pro sekundární lokalitu. Řekněme, že je primární lokalita, že je koncový bod prod.contoso.com a lokalita zálohování je dr.contoso.com. Záznam DNS pro hlavní aplikaci, který se označuje jako \. contoso.com, existuje taky.   
- Hodnota TTL je pod nebo pod RTO smlouvou SLA nastavenou v organizaci. Pokud například podnik nastaví RTO odpovědi na havárie aplikace na 60 minut, hodnota TTL by měla být menší než 60 minut, přednostně čím méně lepší. 
  Azure DNS můžete nastavit pro manuální převzetí služeb při selhání následujícím způsobem:
- Vytvoření zóny DNS
- Vytvoření záznamů zóny DNS
- Aktualizovat záznam CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: vytvoření DNS
Vytvořte zónu DNS (například www \. contoso.com), jak je znázorněno níže:

![Vytvoření zóny DNS v Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Obrázek – vytvoření zóny DNS v Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: vytvoření záznamů zóny DNS

V rámci této zóny vytvořte tři záznamy (například www \. contoso.com, Prod.contoso.com a Dr.consoto.com), jak je uvedeno níže.

![Vytvoření záznamů zóny DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Obrázek – vytvoření záznamů zóny DNS v Azure*

V tomto scénáři má web na webu \. contoso.com hodnotu TTL 30 minut, což je dobře pod uvedeným RTO a odkazuje na produkční web prod.contoso.com. Tato konfigurace je během normálních obchodních operací. Hodnota TTL pro prod.contoso.com a dr.contoso.com byla nastavena na 300 sekund nebo 5 minut. Můžete použít službu monitorování Azure, jako je Azure Monitor nebo Azure App Insights, nebo jakákoli řešení monitorování partnerů, jako je dynaTrace, můžete dokonce použít domácí vypěstovaná řešení, která můžou monitorovat nebo detekovat selhání na úrovni aplikace nebo virtuální infrastruktury.

### <a name="step-3-update-the-cname-record"></a>Krok 3: aktualizace záznamu CNAME

Po zjištění selhání změňte hodnotu záznamu tak, aby odkazovala na dr.contoso.com, jak je znázorněno níže:
       
![Aktualizovat záznam CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Obrázek – aktualizace záznamu CNAME v Azure*

Do 30 minut, během kterých většina překladačů aktualizuje soubor zóny v mezipaměti, bude každý dotaz na webovou \. contoso.com přesměrován na Dr.contoso.com.
Pokud chcete změnit hodnotu CNAME, můžete taky spustit následující příkaz Azure CLI:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Tento krok lze provést ručně nebo prostřednictvím automatizace. Můžete to provést ručně prostřednictvím konzoly nebo pomocí Azure CLI. Sadu Azure SDK a rozhraní API je možné použít k automatizaci aktualizace CNAME, takže se nevyžaduje žádný ruční zásah. Automatizaci je možné vytvořit prostřednictvím služby Azure Functions nebo v rámci aplikace pro monitorování třetích stran nebo i z místního prostředí.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak funguje ruční převzetí služeb při selhání pomocí Azure DNS
Vzhledem k tomu, že se server DNS nachází mimo zónu převzetí služeb při selhání nebo po havárii, je izolovaný proti výpadkům. Díky tomu může uživatel architektovat jednoduchý scénář převzetí služeb při selhání, který je cenově efektivní a bude fungovat i v případě, že má operátor připojení k síti během havárie a může provést překlopení. Pokud je toto řešení skriptované, musí být jeden z nich zajišťovat izolaci serveru nebo služby, která spouští skript, na problém, který by ovlivnil provozní prostředí. Pamatujte také na nízkou hodnotu TTL, která byla nastavena na zónu, aby žádné překladače po celém světě neudržovaly koncový bod v mezipaměti pro dlouhou dobu a zákazníci budou mít přístup k webu v rámci RTO. Pro studený pohotovostní a pilotní světlo, protože může být vyžadována určitá předvolba a další administrativní činnost – jeden by měl také poskytnout dostatek času před provedením překlopení.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatické převzetí služeb při selhání s využitím Azure Traffic Manager
Pokud máte složité architektury a několik sad prostředků schopných provádět stejnou funkci, můžete nakonfigurovat Azure Traffic Manager (na základě DNS), abyste zkontrolovali stav svých prostředků a směrovali provoz z prostředku, který není v pořádku, do prostředku v pořádku. V následujícím příkladu má primární oblast i sekundární oblast úplné nasazení. Toto nasazení zahrnuje cloudové služby a synchronizovanou databázi. 

![Automatické převzetí služeb při selhání s využitím Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Obrázek – automatické převzetí služeb při selhání s využitím Azure Traffic Manager*

Nicméně pouze primární oblast aktivně zpracovává síťové požadavky uživatelů. Sekundární oblast bude aktivní pouze v případě, že dojde k přerušení služby v primární oblasti. V takovém případě všechny nové požadavky na síť směrují do sekundární oblasti. Vzhledem k tomu, že je záloha databáze skoro okamžitá, mají obě služby Vyrovnávání zatížení IP adresy, které se můžou kontrolovat stav a instance jsou vždycky v provozu, tato topologie nabízí možnost přechodu na RTO a převzetí služeb při selhání bez nutnosti ručního zásahu. Sekundární oblast převzetí služeb při selhání musí být připravená přejít hned po selhání primární oblasti.
Tento scénář je ideální pro použití Traffic Manager Azure, která obsahuje předem připravené sondy pro různé typy kontrol stavu, včetně http/https a TCP. Azure Traffic Manager má také modul pravidel, který se dá nakonfigurovat na převzetí služeb při selhání, pokud dojde k selhání, jak je popsáno níže. Pojďme zvážit následující řešení pomocí Traffic Manager:
- Zákazník má oblast #1 koncový bod známý jako prod.contoso.com se statickou IP adresou 100.168.124.44 a oblastí #2 koncový bod známý jako dr.contoso.com se statickou IP adresou jako 100.168.124.43. 
-   Každé z těchto prostředí je frontou prostřednictvím veřejné vlastnosti, jako je třeba nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení je možné nakonfigurovat tak, aby měl koncový bod založený na DNS nebo plně kvalifikovaný název domény (FQDN), jak je uvedeno výše.
-   Všechny instance v oblasti 2 jsou při replikaci téměř v reálném čase s oblastí 1. Kromě toho jsou image počítačů aktuální a všechna data softwaru a konfigurace jsou opravená a jsou v souladu s oblastí 1.  
-   Automatické škálování je předem nakonfigurované předem. 

Postup při konfiguraci převzetí služeb při selhání pomocí Azure Traffic Manager je následující:
1. Vytvořit nový profil Traffic Manager Azure
2. Vytvoření koncových bodů v profilu Traffic Manager
3. Nastavení kontroly stavu a konfigurace převzetí služeb při selhání

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: vytvoření nového profilu Azure Traffic Manager
Vytvořte nový profil Azure Traffic Manageru s názvem contoso123 a vyberte metodu směrování jako prioritu. Pokud máte již existující skupinu prostředků, kterou chcete přidružit, můžete vybrat existující skupinu prostředků, jinak vytvořit novou skupinu prostředků.

![Vytvořit profil Traffic Manager](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Obrázek – vytvoření profilu Traffic Manager*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: vytvoření koncových bodů v profilu Traffic Manager

V tomto kroku vytvoříte koncové body, které odkazují na lokality pro obnovení z provozu a zotavení po havárii. Tady zvolte **typ** jako externí koncový bod, ale pokud je prostředek hostovaný v Azure, můžete zvolit taky **koncový bod Azure** . Pokud zvolíte **koncový bod Azure**, vyberte **cílový prostředek** , který je buď **App Service** , nebo **veřejnou IP adresou** , která je přidělená Azure. Priorita je nastavena na hodnotu **1** , protože se jedná o primární službu pro oblast 1.
Podobně Vytvořte i koncový bod obnovení po havárii v rámci Traffic Manager.

![Vytvoření koncových bodů zotavení po havárii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Obrázek – vytvoření koncových bodů zotavení po havárii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: nastavení kontroly stavu a konfigurace převzetí služeb při selhání

V tomto kroku nastavíte hodnotu TTL pro DNS na 10 sekund, která je zavedená z většiny rekurzivních překladačů internetového přenosu. Tato konfigurace znamená, že žádné překladače DNS nebudou tyto informace ukládat do mezipaměti déle než 10 sekund. Pro nastavení monitorování koncového bodu je cesta aktuální nastavená na/nebo root, ale můžete upravit nastavení koncového bodu a vyhodnotit cestu, například prod.contoso.com/index. Níže uvedený příklad ukazuje protokol **https** jako protokol zjišťování. Můžete ale také zvolit **protokol HTTP** nebo **TCP** . Volba protokolu závisí na koncové aplikaci. Interval zjišťování je nastavený na 10 sekund, který umožňuje rychlé zjišťování a opakování je nastavené na 3. V důsledku toho Traffic Manager převzetí služeb při selhání druhého koncového bodu, pokud dojde k selhání tří po sobě jdoucích intervalů. Následující vzorec definuje celkovou dobu automatizovaného převzetí služeb při selhání: čas pro převzetí služeb při selhání = TTL + opakování * interval zjišťování a v tomto případě je tato hodnota 10 + 3 × 10 = 40 sekund (max).
Je-li opakování nastaveno na hodnotu 1 a hodnota TTL je nastavena na hodnotu 10 sekund, pak je doba pro převzetí služeb při selhání 10 + 1 × 10 = 20 sekund. Nastavte opakování na hodnotu větší než **1** , aby nedošlo k převzetí služeb při selhání kvůli falešně pozitivnímu nebo libovolnému výkyvůi sítě. 


![Nastavení kontroly stavu](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Obrázek – nastavení kontroly stavu a konfigurace převzetí služeb při selhání*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Způsob fungování automatického převzetí služeb při selhání pomocí Traffic Manager

Během havárie se primární koncový bod vyhledá a stav se změní na **Degradováno** a lokalita pro zotavení po havárii zůstane **online**. Ve výchozím nastavení Traffic Manager odesílá veškerý provoz na primární koncový bod (s nejvyšší prioritou). Pokud se primární koncový bod jeví jako snížený, Traffic Manager směruje provoz do druhého koncového bodu, pokud zůstane v dobrém stavu. Jedna má možnost konfigurovat další koncové body v rámci Traffic Manager, které mohou sloužit jako další koncové body převzetí služeb při selhání, nebo jako nástroj pro vyrovnávání zatížení, který sdílí zatížení mezi koncovými body.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Přečtěte si další informace o [Azure DNS](../dns/dns-overview.md).