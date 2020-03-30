---
title: Zotavení po havárii pomocí Azure DNS a Traffic Manager | Dokumenty společnosti Microsoft
description: Přehled řešení pro zotavení po havárii pomocí Azure DNS a Traffic Manageru.
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
ms.openlocfilehash: 6eab1803bf5adab42be87b5f8567682c6d75947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483530"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Zotavení po havárii s využitím Azure DNS a Traffic Manageru

Zotavení po havárii se zaměřuje na obnovení z vážné ztráty funkčnosti aplikace. Aby bylo možné zvolit řešení zotavení po havárii, musí vlastníci podniků a technologií nejprve určit úroveň funkčnosti, která je vyžadována během havárie, jako je například - nedostupná, částečně dostupná prostřednictvím snížené funkčnosti nebo zpožděná dostupnost, nebo plně k dispozici.
Většina podnikových zákazníků volí architekturu s více oblastmi pro odolnost proti převzetí služeb při selhání na úrovni aplikace nebo infrastruktury. Zákazníci si mohou vybrat několik přístupů ve snaze dosáhnout převzetí služeb při selhání a vysokou dostupnost prostřednictvím redundantní architektury. Zde jsou některé z populárních přístupů:

- **Aktivní pasivní s režimem studeného režimu**: V tomto řešení převzetí služeb při selhání virtuální ch odpovky a další zařízení, která běží v oblasti pohotovostního režimu nejsou aktivní, dokud není potřeba převzetí služeb při selhání. Produkční prostředí je však replikováno ve formě záloh, bitových kopií virtuálních počítače nebo šablon Správce prostředků do jiné oblasti. Tento mechanismus převzetí služeb při selhání je nákladově efektivní, ale trvá delší dobu provést úplné převzetí služeb při selhání.
 
    ![Aktivní/Pasivní s pohotovostním režimem za studena](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Obrázek – aktivní/pasivní s konfigurací zotavení po havárii v pohotovostním režimu za studena*

- **Aktivní/Pasivní s pilotním světlem**: V tomto řešení převzetí služeb při selhání je pohotovostní prostředí nastaveno s minimální konfigurací. Instalace má pouze potřebné služby spuštěné pro podporu pouze minimální a kritické sady aplikací. V nativní podobě tento scénář může spustit pouze minimální funkce, ale můžete vertikálně navýšit kapacitu a složit další služby, aby se převážná část zatížení výroby, pokud dojde k převzetí služeb při selhání.
    
    ![Aktivní/Pasivní s pilotním světlem](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Obrázek: Aktivní/pasivní s konfigurací zotavení po havárii pilotního světla*

- **Aktivní/Pasivní s teplým pohotovostním režimem**: V tomto řešení převzetí služeb při selhání je pohotovostní oblast předehřátá a je připravena k převzetí základního zatížení, je zapnuto automatické škálování a všechny instance jsou v provozu. Toto řešení není škálováno tak, aby převzalo plnou výrobní zátěž, ale je funkční a všechny služby jsou v provozu. Toto řešení je rozšířená verze přístupu k pilotnímu světlu.
    
    ![Aktivní/pasivní s teplým pohotovostním režimem](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Obrázek: Aktivní/Pasivní s konfigurací teplého pohotovostního zotavení po havárii*
    
Další informace o převzetí služeb při selhání a vysoké dostupnosti najdete [v tématu Zotavení po havárii pro aplikace Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Plánování architektury zotavení po havárii

Nastavení architektury zotavení po havárii má dva technické aspekty:
-  Použití mechanismu nasazení k replikaci instancí, dat a konfigurací mezi primárním a pohotovostním prostředím. Tento typ zotavení po havárii lze provádět nativně prostřednictvím Azure Site-Recovery prostřednictvím partnerských zařízení nebo služeb Microsoft Azure, jako je Veritas nebo NetApp. 
- Vývoj řešení pro přesměrování síťového/webového provozu z primární lokality do pohotovostní lokality. Tohoto typu zotavení po havárii lze dosáhnout prostřednictvím Azure DNS, Azure Traffic Manager (DNS) nebo globálních vyrovnávání zatížení třetích stran.

Tento článek je omezen na přístupy prostřednictvím přesměrování síťového a webového provozu. Pokyny k nastavení Azure Site Recovery najdete v [tématu Dokumentace k obnovení webu Azure](https://docs.microsoft.com/azure/site-recovery/).
Služba DNS je jedním z nejúčinnějších mechanismů pro přesměrování síťového provozu, protože služba DNS je často globální a externí k datovému centru a je izolována od všech selhání na úrovni místní zóny nebo zóny dostupnosti (AZ). Jeden můžete použít mechanismus převzetí služeb při selhání založené na DNS a v Azure, dvě služby DNS můžete provést totéž nějakým způsobem - Azure DNS (autoritativní DNS) a Azure Traffic Manager (DNS založené inteligentní provoz směrování). 

Je důležité porozumět několika konceptům v dns, které se ve velké míře používají k diskusi o řešeních uvedených v tomto článku:
- **Záznam DNS** – Záznamy jsou ukazatele, které odkazují na doménu na adresu IPv4. 
- **CNAME nebo Canonical name** - Tento typ záznamu se používá k odkazem na jiný záznam DNS. CNAME nereaguje s IP adresou, ale spíše ukazatelem na záznam, který obsahuje IP adresu. 
- **Vážené směrování** – lze se rozhodnout přidružit váhu ke koncovým bodům služby a poté distribuovat provoz na základě přiřazených vah. Tato metoda směrování je jedním ze čtyř mechanismů směrování provozu, které jsou k dispozici v rámci traffic manageru. Další informace naleznete v tématu [Vážená metoda směrování](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Směrování priorit** – směrování priorit je založeno na kontrolách stavu koncových bodů. Ve výchozím nastavení Azure Traffic Manager odešle veškerý provoz do koncového bodu s nejvyšší prioritou a po selhání nebo havárii traffic manager směruje provoz do sekundárního koncového bodu. Další informace naleznete [v tématu Priorita metody směrování](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Ruční převzetí služeb při selhání pomocí Azure DNS
Řešení převzetí služeb při selhání azure DNS pro zotavení po havárii používá standardní mechanismus DNS k převzetí služeb při selhání do zálohovací lokality. Ruční možnost prostřednictvím Azure DNS funguje nejlépe při použití ve spojení s režimem studeného režimu nebo přiblížení pilotního světla. 

![Ruční převzetí služeb při selhání pomocí Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Obrázek – ruční převzetí služeb při selhání pomocí Azure DNS*

Předpoklady pro řešení jsou:
- Primární i sekundární koncové body mají statické IP adresy, které se často nemění. Řekněme, že pro primární lokalitu je ip adresa 100.168.124.44 a IP pro sekundární lokalitu je 100.168.124.43.
- Zóna Azure DNS existuje pro primární i sekundární lokalitu. Řekněme, že pro primární lokalitu je koncový bod prod.contoso.com a pro záložní lokalitu je dr.contoso.com. Existuje také záznam DNS pro\.hlavní aplikaci označovanou jako www contoso.com.   
- TTL je na nebo pod RTO SLA stanovené v organizaci. Pokud například podnik nastaví RTO reakce aplikace na katastrofy na 60 minut, pak by ttl měla být menší než 60 minut, nejlépe tím nižší, tím lépe. 
  Azure DNS můžete nastavit pro ruční převzetí služeb při selhání následujícím způsobem:
- Vytvoření zóny DNS
- Vytvoření záznamů zóny DNS
- Aktualizovat záznam CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: Vytvoření DNS
Vytvořte zónu DNS (například www\.contoso.com), jak je znázorněno níže:

![Vytvoření zóny DNS v Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Obrázek – vytvoření zóny DNS v Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: Vytvoření záznamů zóny DNS

V rámci této zóny vytvořte\.tři záznamy (například www contoso.com, prod.contoso.com a dr.consoto.com), jak je uvedeno níže.

![Vytvoření záznamů zóny DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Obrázek – vytvoření záznamů zóny DNS v Azure*

V tomto scénáři\.site, www contoso.com má TTL 30 minut, což je hluboko pod uvedenou RTO, a ukazuje na výrobní místo prod.contoso.com. Tato konfigurace je během běžných obchodních operací. TTL prod.contoso.com a dr.contoso.com byla nastavena na 300 sekund nebo 5 minut. Můžete použít službu monitorování Azure, jako je Azure Monitor nebo Azure App Insights, nebo, jakékoli řešení monitorování partnerů, jako je Dynatrace, Můžete dokonce použít domácí řešení, která můžete sledovat nebo detekovat selhání na úrovni aplikace nebo virtuální infrastruktury.

### <a name="step-3-update-the-cname-record"></a>Krok 3: Aktualizace záznamu CNAME

Jakmile je zjištěna chyba, změňte hodnotu záznamu tak, aby ukazovala na dr.contoso.com, jak je znázorněno níže:
       
![Aktualizovat záznam CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Obrázek – aktualizace záznamu CNAME v Azure*

Do 30 minut, během kterých většina překládání obnoví\.soubor zóny uložené v mezipaměti, bude jakýkoli dotaz na www contoso.com přesměrován na dr.contoso.com.
Můžete také spustit následující příkaz Azure CLI a změnit hodnotu CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Tento krok lze provést ručně nebo prostřednictvím automatizace. Lze provést ručně prostřednictvím konzoly nebo pomocí příkazového příkazového příkazu Azure. Azure SDK a rozhraní API lze použít k automatizaci aktualizace CNAME tak, aby není vyžadován žádný ruční zásah. Automatizaci lze sestavit prostřednictvím funkcí Azure nebo v rámci aplikace pro monitorování třetích stran nebo dokonce z místního prostředí.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak funguje ruční převzetí služeb při selhání pomocí Azure DNS
Vzhledem k tomu, že server DNS je mimo zónu převzetí služeb při selhání nebo zónu havárie, je izolován proti jakýmkoli prostojům. To umožňuje uživateli navrhovat jednoduchý scénář převzetí služeb při selhání, který je nákladově efektivní a bude fungovat po celou dobu za předpokladu, že operátor má připojení k síti během havárie a může provést překlopení. Pokud je řešení skriptováno, je třeba zajistit, aby server nebo služba se skriptem byla izolována proti problému ovlivňujícímu produkční prostředí. Mějte také na paměti nízkou hodnotu TTL, která byla nastavena proti zóně tak, aby žádný překladač po celém světě udržuje koncový bod v mezipaměti po dlouhou dobu a zákazníci mají přístup k webu v rámci vzdáleného vzdáleného webu. Pro studený pohotovostní režim a pilotní světlo, protože některé předpěřování a další administrativní činnost může být požadováno - jeden by měl také dát dostatek času před provedením flip.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatické převzetí služeb při selhání pomocí Azure Traffic Manageru
Pokud máte složité architektury a více sad prostředků schopných provádět stejnou funkci, můžete nakonfigurovat Azure Traffic Manager (na základě DNS) ke kontrole stavu vašich prostředků a směrovat provoz z prostředku, který není v pořádku, do stavu v pořádku. Zdrojů. V následujícím příkladu primární oblasti a sekundární oblasti mají úplné nasazení. Toto nasazení zahrnuje cloudové služby a synchronizovanou databázi. 

![Automatické převzetí služeb při selhání pomocí Azure Traffic Manageru](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Obrázek – automatické převzetí služeb při selhání pomocí Azure Traffic Manageru*

Pouze primární oblast však aktivně zpracovává síťové požadavky od uživatelů. Sekundární oblast se aktivuje pouze v případě, že primární oblast dojde k přerušení služby. V takovém případě všechny nové požadavky sítě směrovat do sekundární oblasti. Vzhledem k tomu, že zálohování databáze je téměř okamžité, oba vykladače zatížení mají IP adresy, které lze zkontrolovat stav a instance jsou vždy v provozu, tato topologie poskytuje možnost pro jít do pro nízké RTO a převzetí služeb při selhání bez ručního zásahu. Sekundární oblast převzetí služeb při selhání musí být připravena k živému vysílání ihned po selhání primární oblasti.
Tento scénář je ideální pro použití Azure Traffic Manager, který má vestavěné sondy pro různé typy kontrol stavu, včetně http / https a TCP. Azure Traffic Manager má také modul pravidel, který lze nakonfigurovat na převzetí služeb při selhání, když dojde k selhání, jak je popsáno níže. Podívejme se na následující řešení pomocí Traffic Manager:
- Zákazník má koncový bod oblast #1 označovaný jako prod.contoso.com se statickou IP adresou jako 100.168.124.44 a koncový bod #2 oblast známý jako dr.contoso.com se statickou IP adresou jako 100.168.124.43. 
-   Každé z těchto prostředí je fronted přes veřejné čelí vlastnost jako vyrovnávání zatížení. Provyčitatele zatížení lze nakonfigurovat tak, aby měl koncový bod založený na DNS nebo plně kvalifikovaný název domény (Plně kvalifikovaný název domény), jak je uvedeno výše.
-   Všechny instance v oblasti 2 jsou téměř v reálném čase replikace s oblastí 1. Kromě toho jsou bitové kopie počítače aktuální a všechna data softwaru/konfigurace jsou opravena a jsou v souladu s oblastí 1.  
-   Automatické škálování je předem nakonfigurováno předem. 

Kroky ke konfiguraci převzetí služeb při selhání pomocí Azure Traffic Manageru jsou následující:
1. Vytvoření nového profilu Azure Traffic Manageru
2. Vytvoření koncových bodů v profilu Traffic Manageru
3. Nastavení konfigurace kontroly stavu a převzetí služeb při selhání

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: Vytvoření nového profilu Azure Traffic Manageru
Vytvořte nový profil Správce provozu Azure s názvem contoso123 a vyberte metodu Směrování jako prioritu. Pokud máte již existující skupinu prostředků, ke které chcete přidružit, můžete vybrat existující skupinu prostředků, jinak vytvořte novou skupinu prostředků.

![Vytvořit profil traffic manageru](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Obrázek – vytvoření profilu traffic managera*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: Vytvoření koncových bodů v profilu Traffic Manageru

V tomto kroku vytvoříte koncové body, které odkazují na lokality pro produkční prostředí a zotavení po havárii. Tady zvolte **Type** jako externí koncový bod, ale pokud je prostředek hostovaný v Azure, pak můžete taky zvolit **koncový bod Azure.** Pokud zvolíte **koncový bod Azure**, vyberte cílový **prostředek,** který je buď **službou App Service,** nebo **veřejnou IP adresou,** která je přidělená Azure. Priorita je nastavena jako **1,** protože se jedná o primární službu pro oblast 1.
Podobně vytvořte koncový bod zotavení po havárii v rámci Traffic Manager také.

![Vytvoření koncových bodů zotavení po havárii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Obrázek – vytvoření koncových bodů zotavení po havárii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: Nastavení konfigurace kontroly stavu a převzetí služeb při selhání

V tomto kroku nastavíte DNS TTL na 10 sekund, což je dodrženo většinou internetových rekurzivních překladačů. Tato konfigurace znamená, že žádný překladač DNS bude ukládat informace do mezipaměti po dobu delší než 10 sekund. Pro nastavení monitoru koncového bodu je cesta aktuální nastavena na / nebo root, ale můžete přizpůsobit nastavení koncového bodu k vyhodnocení cesty, například prod.contoso.com/index. Následující příklad ukazuje **protokol https** jako sondovací protokol. Můžete však také zvolit **http** nebo **tcp.** Volba protokolu závisí na koncové aplikaci. Interval sondování je nastaven na 10 sekund, což umožňuje rychlé sondování a opakování je nastaveno na 3. V důsledku toho Traffic Manager převezme služby při selhání do druhého koncového bodu, pokud tři po sobě jdoucí intervaly zaregistrovat selhání. Následující vzorec definuje celkový čas pro automatické převzetí služeb při selhání: Čas pro převzetí služeb při selhání = TTL + Opakování * Interval zjišťování A v tomto případě je hodnota 10 + 3 * 10 = 40 sekund (Max).
Pokud je opakování nastaveno na 1 a TTL je nastavena na 10 sekund, pak čas pro převzetí služeb při selhání 10 + 1 * 10 = 20 sekund. Nastavte Opakovat na hodnotu větší než **1,** aby se vyloučila pravděpodobnost převzetí služeb při selhání z důvodu falešně pozitivních hodnot nebo menší síťové chyby. 


![Nastavit kontrolu stavu](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Obrázek – nastavení konfigurace kontroly stavu a převzetí služeb při selhání*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Jak automatické převzetí služeb při selhání funguje pomocí Traffic Manageru

Během havárie primární koncový bod získá sondované a stav se změní na **degradován** a lokalita zotavení po havárii zůstane **online**. Ve výchozím nastavení Traffic Manager odesílá veškerý provoz na primární koncový bod (s nejvyšší prioritou). Pokud primární koncový bod se zobrazí degradován, Traffic Manager směruje provoz do druhého koncového bodu tak dlouho, dokud zůstane v pořádku. Jeden má možnost nakonfigurovat více koncových bodů v rámci Traffic Manager, který může sloužit jako další koncové body převzetí služeb při selhání, nebo jako nástroje pro vyrovnávání zatížení sdílení zatížení mezi koncovými body.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md).
- Další informace o [Azure DNS](../dns/dns-overview.md).









