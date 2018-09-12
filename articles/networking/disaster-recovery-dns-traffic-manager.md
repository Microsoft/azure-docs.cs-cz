---
title: Zotavení po havárii pomocí Azure DNS a Traffic Manager | Dokumentace Microsoftu
description: Přehled řešení zotavení po havárii pomocí Azure DNS a Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
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
ms.openlocfilehash: ce3e8f31c7fee6afdeabf931485a49934e98f81b
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391347"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Zotavení po havárii s využitím Azure DNS a Traffic Manageru

Zotavení po havárii se zaměřuje na obnovení závažné ztráty funkčnost aplikace. Pokud chcete zvolit řešení zotavení po havárii, třeba obchodním a technologickým vlastníky nejprve určit úroveň funkcí, které se vyžaduje při havárii, jako například – není k dispozici, částečně dostupného přes omezenou funkčnost, nebo zpožděného dostupnosti, nebo plně k dispozici.
Většina podnikoví zákazníci volí architektura pro více oblastí pro odolnost proti aplikace nebo úrovni převzetí služeb při selhání infrastrukturou. Zákazníci si mohou vybrat několik přístupů v Vyžá zajistit převzetí služeb při selhání a vysokou dostupností redundantní architektury. Tady jsou některé z oblíbených přístupů:

- **Aktivní-pasivní vysoká dostupnost s pasivním pohotovostním režimem:**: V tomto řešení převzetí služeb při selhání virtuálních počítačů a jiných zařízení, které jsou spuštěny v pohotovostním režimu oblasti nejsou aktivní dokud je potřeba pro převzetí služeb při selhání. Produkčním prostředí se však replikují ve formě záloh, Image virtuálních počítačů nebo šablon Resource Manageru do jiné oblasti. Tento mechanismus převzetí služeb při selhání je nákladově efektivní, ale trvá delší dobu provádět dokončení převzetí služeb při selhání.
 
    ![Aktivní/pasivní vysoká dostupnost s pasivním pohotovostním režimem:](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Obrázek – aktivní/pasivní vysoká dostupnost s konfigurace zotavení po havárii studenou pohotovostním režimu*

- **Aktivní/pasivní vysoká dostupnost s kontrolka**: V tomto řešení převzetí služeb při selhání je nastavit pohotovostního prostředí s minimální konfigurací. Instalační program obsahuje pouze nezbytné služby spuštěné na podporu pouze náročné a minimální sadu aplikací. V nativním formátu tento scénář můžete provést minimální funkce, ale můžete vertikálně navýšit kapacitu a spustit další služby provést hromadné provozního zatížení, pokud dojde k selhání.
    
    ![Aktivní/pasivní vysoká dostupnost s kontrolka](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Obrázek: Aktivní/pasivní vysoká dostupnost s kontrolka konfigurace zotavení po havárii*

- **Aktivní/pasivní vysoká dostupnost s záložním pohotovostním režimu**: V tomto řešení převzetí služeb při selhání pohotovostní oblasti předem zahřívají a je připravena vytvořit základní zatížení, automatické škálování je zapnuté a všechny instance jsou spuštěné. Toto řešení není škálovat, aby trvat plného zatížení, ale je funkční, a všechny služby jsou spuštěny. Toto řešení je rozšířená verze kontrolka přístup.
    
    ![Aktivní/pasivní s záložním pohotovostním režimem:](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Obrázek: Aktivní/pasivní vysoká dostupnost s konfigurace zotavení po havárii záložním pohotovostním režimu*
    
Další informace o vysoké dostupnosti a převzetí služeb při selhání najdete v tématu [zotavení po havárii pro aplikace Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Plánování architektury pro zotavení po havárii

Existují dva technické aspekty směrem k nastavení architektury pro zotavení po havárii:
-  Pomocí mechanismu nasazení k replikaci mezi prostředími primárního a záložního instancí, dat a konfigurací. Tento typ zotavení po havárii můžete to udělat nativně přes Azure Site-Recovery pomocí služby Microsoft Azure partnerské zařízení nebo jako Veritas nebo NetApp. 
- Vývoj řešení k rozdělení sítě a webového provozu z primární lokality do pohotovostního lokality. Tento typ zotavení po havárii můžete dosáhnout prostřednictvím Azure DNS, Manager(DNS) provozu Azure nebo nástroje pro vyrovnávání zatížení globálního třetích stran.

Tento článek je omezená na přístupy prostřednictvím přesměrování provozu sítě a Web. Pokyny k nastavení Azure Site Recovery najdete v tématu [dokumentace ke službě Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
DNS je jednou z nejvíce využití efektivních mechanismů k rozdělení síťový provoz, protože DNS je často globální a mimo datové centrum a izolované od jakékoli místní nebo dostupnost zóny (AZ) úrovně chyby. Můžete s použitím mechanismus převzetí služeb při selhání na základě DNS a v Azure, můžete provést dvě služby DNS stejné nějak - Azure DNS (autoritativních DNS) a Azure Traffic Manageru (inteligentního přenosu na základě DNS směrování). 

Je důležité porozumět několika konceptům ve službě DNS, který se často používají k řešení uvedené v tomto článku:
- **Záznam DNS** – záznamy jsou ukazatele, které domény přejděte na adresu IPv4. 
- **Název CNAME nebo Canonical** – tento typ záznamu se používá tak, aby odkazoval na jiný záznam DNS. Záznam CNAME nereaguje IP adresu, ale místo toho je ukazatel na záznam, který obsahuje IP adresu. 
- **Váha směrování** – lze přidružit váhu do koncových bodů služby a pak distribuovat provoz podle váhy přiřazené. Tuto metodu směrování je jedním z čtyři provoz směrování mechanismy k dispozici v Traffic Manageru. Další informace najdete v tématu [váha směrování metoda](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Priorita směrování** – směrování Priority je založené na kontroly stavu koncových bodů. Ve výchozím nastavení Azure Traffic Manageru odesílá veškerý provoz na nejvyšší prioritního koncového bodu a při selhání nebo havárie, Traffic Manager směruje provoz do sekundárního koncového bodu. Další informace najdete v tématu [metody prioritního směrování](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Ruční převzetí služeb při selhání pomocí Azure DNS
Řešení Azure DNS ruční převzetí služeb při selhání pro zotavení po havárii využívá standardní mechanismus DNS pro převzetí služeb při selhání k zálohování lokality. Možnost ručně přes Azure DNS je nejvhodnější při použití ve spojení s pasivním pohotovostním režimem nebo kontrolka přístup. 

![Ruční převzetí služeb při selhání pomocí Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Obrázek – ruční převzetí služeb při selhání pomocí Azure DNS*

Předpoklady pro řešení jsou:
-   Primární a sekundární koncové body mají statické IP adresy, která se často nemění. Dejme tomu, že primární lokalita je IP adresa 100.168.124.44 a 100.168.124.43 se IP adresa pro sekundární lokalitu.
-   Zóny DNS existuje pro primární a sekundární lokality. Dejme tomu, že pro danou primární lokalitu na koncový bod je prod.contoso.com a pro zálohování lokality dr.contoso.com. Existuje také záznam DNS pro hlavní aplikaci označuje jako www.contoso.com.   
-   Hodnota TTL je v nebo nižší než SLA RTO, nastavte v organizaci. Pokud třeba organizace nastaví RTO s odpověď po havárii aplikace bude 60 minut, a hodnota TTL musí být menší než 60 minut, pokud možno nižší lepší. Můžete nastavit Azure DNS pro ruční převzetí služeb při selhání následujícím způsobem:
1. Vytvoření zóny DNS
2. Vytvoření záznamů DNS zóny
3. Aktualizace záznamu CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: Vytvoření serveru DNS
Vytvoření zóny DNS (například www.contoso.com), jak je znázorněno níže:

![Vytvoření zóny DNS v Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Obrázek – vytvoření zóny DNS v Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: Vytvoření záznamů DNS zóny

V rámci této zóny vytvořte tři záznamy (například - www.contoso.com, prod.contoso.com a dr.consoto.com) jako dole nezobrazují.

![Vytvoření záznamů DNS zóny](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Obrázek – vytvoření záznamů zóny DNS v Azure*

V tomto scénáři webu, www.contoso.com má hodnotu TTL 30 minut, je také níže uvedená RTO, který odkazuje na webu prod.contoso.com produkčního prostředí. Tato konfigurace je během běžné obchodní operace. Hodnota TTL prod.contoso.com a dr.contoso.com je nastavená na 300 sekund nebo 5 minut. Můžete použít monitorování služby, jako je Azure Monitor nebo Azure App Insights Azure nebo partnerského řešení, jako je například Dynatrace monitorování se dá dokonce využít domácí pěstuje řešení, které můžete monitorování nebo zjišťování aplikace nebo selhání virtuální infrastruktury na úrovni.

### <a name="step-3-update-the-cname-record"></a>Krok 3: Aktualizace záznamu CNAME

Po selhání detekuje, změňte hodnotu záznamu tak, aby odkazoval na dr.contoso.com, jak je znázorněno níže:
       
![Aktualizace záznamu CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Obrázek – aktualizace záznam CNAME v Azure*

Do 30 minut, během nichž většina překladače aktualizuje soubor v mezipaměti zóny, budete přesměrováni na dr.contoso.com jakýkoli dotaz na www.contoso.com.
Můžete také spustit následující příkaz Azure CLI ke změně hodnoty CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Tento krok lze provést ručně nebo pomocí automatizace. To lze provést ručně přes konzolu nebo pomocí rozhraní příkazového řádku Azure. Azure SDK a rozhraní API můžete použít k automatizaci aktualizace CNAME tak, aby není nutný žádný ruční zásah. Automatizaci jde integrovat prostřednictvím Azure functions nebo v rámci monitorování aplikací třetích stran nebo dokonce z místního.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak ruční převzetí služeb při selhání funguje používání Azure DNS
Protože se DNS server mimo pásmo převzetí služeb při selhání nebo havárie, izolována žádné výpadky. To umožňuje uživatelům upravovat architekturu, když tento scénář jednoduchého převzetí služeb při selhání je nákladově efektivní a bude fungovat, všechny čas za předpokladu, že operátor má připojení k síti během po havárii a můžete provést druhou. Pokud je vytvořena řešení, pak jeden musíte zajistit, server nebo služba spuštění skriptu by měl být izolované proti problému by to mělo dopad na produkční prostředí. Také mějte na paměti s nízkou hodnotu TTL, která byla nastavena na zóně tak, aby resolver po celém světě zajišťuje koncového bodu do mezipaměti pro dlouho a zákazníci mají přístup k lokalitě v rámci RTO. Pasivním pohotovostním režimem a světla pilotního projektu protože některé prewarming a další aktivitu správy může být třeba jeden by měl také poskytují dostatek času před provedením druhou.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatické převzetí služeb při selhání pomocí Azure Traffic Manageru
Pokud máte komplexní architektury a více sad prostředků schopný provádět má stejnou funkci, můžete nakonfigurovat Azure Traffic Manageru (podle DNS) zkontrolovat stav svých prostředků a směrovat provoz z prostředku není v pořádku v pořádku prostředek. V následujícím příkladu primární oblasti a sekundární oblasti máte úplné nasazení. Toto nasazení zahrnuje cloudové služby a databáze synchronizované. 

![Automatické převzetí služeb při selhání pomocí Azure Traffic Manageru](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Obrázek – automatické převzetí služeb při selhání pomocí Azure Traffic Manageru*

Jenom primární oblasti se však aktivně zpracování síťových požadavků od uživatelů. Sekundární oblasti stane aktivní, pouze v případě, že primární oblast dojde k přerušení služeb. V takovém případě všechny nové požadavky na sítě směrovat do sekundární oblasti. Od zálohování databáze je téměř okamžité, oba nástroje pro vyrovnávání zatížení mají IP adresy, které mohou být stav zaškrtnutí a instance jsou vždy nahoru a spuštěn, tato topologie poskytuje možnost do toho pustit v nízké RTO a převzetí služeb při selhání bez ručního zásahu. Převzetí služeb při selhání sekundární oblastí musí být připraveny pro uvedení do provozu okamžitě po selhání z primární oblasti.
Tento scénář je ideální pro použití nástroje Azure Traffic Manageru, který má integrované testy pro různé typy kontrol stavu, včetně protokolu http / https a protokolu TCP. Azure Traffic manager má také stroj pravidel, která lze nakonfigurovat pro převzetí služeb při selhání, když dojde k selhání, jak je popsáno níže. Uvažujme následující řešení pomocí služby Traffic Manager:
- Zákazník má koncový bod oblasti č. 1 označuje jako prod.contoso.com se statickou IP adresou jako 100.168.124.44 a říká dr.contoso.com se statickou IP adresou jako 100.168.124.43 koncový bod oblasti č. 2. 
-   Každá z těchto prostředí je přední stěnou přes veřejnou vlastnost protilehlé jako nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení můžete nakonfigurovat mít koncový bod na základě DNS nebo použitím plně kvalifikovaného názvu domény (FQDN), jak je znázorněno výše.
-   Všechny instance ve verzi 2 oblasti jsou v téměř v reálném čase replikace s 1 oblast. Kromě toho Image počítačů jsou aktuální a všechna data nebo konfigurace softwaru je opravují se a jsou tato oblast 1.  
-   Automatické škálování je předkonfigurované předem. 

Kroky, chcete-li nakonfigurovat převzetí služeb při selhání pomocí Azure Traffic Manageru jsou následující:
1. Vytvoření nového profilu Azure Traffic Manageru
2. Vytváření koncových bodů v rámci profilu Traffic Manageru
3. Nastavení konfigurace stavu zaškrtnutí a převzetí služeb při selhání

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: Vytvoření nového profilu Azure Traffic Manageru
Vytvoření nového profilu Azure Traffic Manageru s contoso123 název a vyberte metodu směrování jako prioritní. Pokud máte existující skupinu prostředků, kterou chcete přidružit, pak můžete vybrat existující skupinu prostředků, v opačném případě vytvořte novou skupinu prostředků.

![Vytvořit profil služby Traffic Manager](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
*obrázek – vytvoření profilu Traffic Manageru*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: Vytvoření koncových bodů v rámci profilu Traffic Manageru

V tomto kroku vytvoříte koncové body, které přejděte na produkčního prostředí a servery pro zotavení po havárii. Tady, zvolte **typ** jako externí koncový bod, ale pokud prostředek je hostované v Azure, pak se můžete rozhodnout **koncový bod Azure** také. Pokud se rozhodnete **koncový bod Azure**a pak **cílový prostředek** , který je buď **služby App Service** nebo **veřejnou IP adresu** , která je přidělena Azure. Priorita je nastavena jako **1** protože jde o primární služeb pro oblast 1.
Podobně vytvořte po havárii obnovení koncový bod v Traffic Manageru také.

![Vytvořit koncové body obnovení po havárii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Obrázek – vytvoření koncových bodů pro zotavení po havárii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: Nastavení konfigurace stavu zaškrtnutí a převzetí služeb při selhání

V tomto kroku nastavíte hodnotu TTL pro DNS na 10 sekund, což je respektovat většina rekurzivní překladače přístupem k Internetu. Tato konfigurace znamená, že žádné překladače DNS bude ukládat do mezipaměti informace o více než 10 sekund. Nastavení monitorování koncového bodu, cesta je v aktuální sadě / nebo kořenový, ale můžete přizpůsobit nastavení koncového bodu se vyhodnotit cestu, například prod.contoso.com/index. Na příkladu níže **https** jako protokol pro zjišťování. Ale můžete zvolit **http** nebo **tcp** také. Výběr protokolu závisí na ukončení aplikace. Zjišťování interval je nastavený na 10 sekund, což umožňuje rychlé zjišťování a opakování je nastavena na hodnotu 3. V důsledku toho Traffic Manageru se převzetí služeb při selhání do druhého koncového bodu případě tří po sobě jdoucích intervalech zaregistrovat k chybě. Na následující vzorec definuje celkový čas automatické převzetí služeb při selhání: čas pro převzetí služeb při selhání = TTL + opakujte * interval zjišťování a v takovém případě hodnota je 10 + 3 * 10 = 40 sekund (Max.).
Pokud se nový pokus je nastavena na 1 a hodnota TTL je nastavena na 10 sekund, potom čas pro převzetí služeb při selhání 10 + 1 * 10 = 20 sekund. Nastavení opakování na hodnotu větší než **1** eliminovat riziko převzetí služeb při selhání z důvodu počet falešně pozitivních výsledků nebo jakékoli blips menší sítě. 


![Nastavení kontroly stavu](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Obrázek – nastavení konfigurace stavu zaškrtnutí a převzetí služeb při selhání*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Funkce automatické převzetí služeb při selhání funguje pomocí Traffic Manageru

Při havárii, získá otestovaná primárního koncového bodu a stav se změní na **degradovaný** a zůstává lokalita zotavení po havárii **Online**. Ve výchozím nastavení Traffic Manageru odesílá veškerý provoz na primární koncový bod (nejvyšší priorita). Pokud primární koncový bod se zobrazí v degradovaném stavu, Traffic Manageru směruje provoz druhý za předpokladu, zůstane v dobrém stavu. Jeden má možnost konfigurovat více koncových bodů v Traffic Manageru, které může sloužit jako koncové body další převzetí služeb při selhání nebo jako nástroje pro vyrovnávání zatížení mezi koncovými body sdílení zatížení.

## <a name="next-steps"></a>Další postup
- Další informace o [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Další informace o [Azure DNS](../dns/dns-overview.md).









