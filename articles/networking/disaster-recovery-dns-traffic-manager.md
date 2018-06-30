---
title: Zotavení po havárii pomocí Azure DNS a Traffic Manageru | Microsoft Docs
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
ms.openlocfilehash: d608378f9b3ff3179f9e37ef13f88c65a645d018
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112982"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Zotavení po havárii s využitím Azure DNS a Traffic Manageru

Zotavení po havárii se zaměřuje na obnovu po závažné ztráty funkčnosti aplikací. Chcete-li vybrat řešení zotavení po havárii, třeba obchodním a technologickým vlastníky nejprve určit úroveň funkcí, které je potřeba při havárii, jako například – není k dispozici, částečně dostupné prostřednictvím sníženou funkčností nebo zpožděné dostupnosti, nebo plně k dispozici.
Většina podnikových zákazníků výběru několika oblast architektura pro odolnost proti aplikace nebo úrovně převzetí služeb při selhání infrastruktury. Zákazníci v požadavek na zajistit převzetí služeb při selhání a vysokou dostupnost prostřednictvím redundantní architektura můžete vybrat několik přístupů. Tady jsou některé z oblíbených přístupů:

- **Aktivní – pasivní s cold pohotovostní**: V tomto řešení převzetí služeb při selhání virtuálních počítačů a jiných zařízení spuštěný v oblasti pohotovostní nejsou aktivní dokud je třeba pro převzetí služeb při selhání. Produkčním prostředí se však replikují ve formě záloh, Image virtuálních počítačů nebo šablony Resource Manageru, v jiné oblasti. Tento mechanismus převzetí služeb při selhání je nákladově efektivní, ale trvá déle provádět dokončení převzetí služeb při selhání.
 
    ![Aktivní/pasivní s cold pohotovostní režim](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Obrázek – aktivní/pasivní s konfiguraci obnovení po havárii cold pohotovostní*

- **Aktivní/pasivní s pilotní nasazení light**: V tomto řešení převzetí služeb při selhání pohotovostní prostředí nastavené s minimální konfigurací. Instalace má pouze se potřebné služby spuštěné na podporu pouze minimální a kritické sadu aplikací. V nativním formátu tento scénář můžete provést minimální funkce, ale můžete škálovat a vytvořit další služby provést hromadné produkční zatížení, pokud dojde k selhání.
    
    ![Aktivní/pasivní s light pilotní nasazení](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Obrázek: Aktivní/pasivní s konfigurací pro zotavení po havárii light pilotní nasazení*

- **Aktivní/pasivní s záložním pohotovostním**: V tomto řešení převzetí služeb při selhání pohotovostní oblast je předem jestli a je připravena vytvořit základní zatížení, automatické škálování je zapnutý a všechny instance jsou spuštěny. Toto řešení není škálovat provést úplné produkční zatížení, ale je funkční, a všechny služby jsou spuštěny. Toto řešení je aktualizovaná verze tohoto přístupu light pilotního nasazení.
    
    ![Aktivní/pasivní s záložním pohotovostním](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Obrázek: Aktivní/pasivní s konfiguraci obnovení po havárii záložním pohotovostním režimu*
    
Další informace o převzetí služeb při selhání a vysokou dostupnost, najdete v části [zotavení po havárii pro aplikací Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Plánování vaší architektury obnovení po havárii

Existují dvě technické aspekty směrem nastavení vaší architektury obnovení po havárii:
-  Pomocí nasazení mechanismus k replikaci mezi prostředími primárního a záložního instancí, dat a konfigurací. Tento typ zotavení po havárii můžete provést nativně prostřednictvím Azure Site-Recovery prostřednictvím služby Microsoft Azure partnera zařízení nebo jako této společnosti nebo NetApp. 
- Vývoj řešení pro přesměrovat provoz sítě nebo webové z primární lokality do pohotovostního lokality. Tento typ zotavení po havárii se dá dosáhnout pomocí Azure DNS, Manager(DNS) provozu Azure nebo nástroje pro vyrovnávání zatížení globální třetích stran.

Tento článek je omezený na přístupy prostřednictvím sítě a webové přenosy přesměrování. Pokyny k nastavení Azure Site Recovery najdete v tématu [dokumentaci k Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
DNS je jedním z nejúčinnější mechanismy pro přesměrovat provoz sítě, protože DNS je často globální a externí do datového centra a izolované od jeho místní nebo dostupnost zóny (AZ) úrovni selhání. Jeden můžete použít mechanismus převzetí služeb při selhání na základě DNS a v Azure, můžete provést dvě služby DNS stejné nějak - Azure DNS (autoritativních DNS) a Azure Traffic Manager (směrování na základě DNS inteligentní provozu). 

Je důležité si uvědomit, několika koncepty ve službě DNS, který je hojně používaný diskutovat o řešení uvedené v tomto článku:
- **Záznam DNS A** – jsou A záznamy ukazatele, které domény přejděte na adresu IPv4. 
- **Název CNAME nebo Canonical** – tento typ záznamu je používat tak, aby odkazoval na jiný záznam DNS. IP adresu, ale spíš ukazatele nebude odpovídat CNAME na záznam, který obsahuje IP adresu. 
- **Vážené směrování** – jeden můžete přidružit váhu ke koncovým bodům služby a pak distribuovat provozu na základě přiřazené vah. Tuto metodu směrování je jedním z čtyři provoz směrování mechanismů k dispozici v rámci Traffic Manager. Další informace najdete v tématu [vážené metody směrování](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Směrování s prioritou** – směrování s prioritou vychází z kontroly stavu koncových bodů. Ve výchozím nastavení Azure Traffic manager odešle veškerý provoz do nejvyšší priorita koncového bodu a při selhání nebo havárie, Traffic Manager směruje provoz do sekundárního koncového bodu. Další informace najdete v tématu [metody směrování s prioritou](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Ruční převzetí služeb při selhání pomocí Azure DNS
Azure DNS ruční převzetí služeb při selhání řešení pro zotavení po havárii používá standardní mechanismus DNS pro převzetí služeb při selhání k zálohování lokality. Ruční prostřednictvím Azure DNS funguje nejlíp, když používá ve spojení s cold pohotovostní režim nebo přístup light pilotního nasazení. 

![Ruční převzetí služeb při selhání pomocí Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Obrázek – ruční převzetí služeb při selhání pomocí Azure DNS*

Předpoklady pro řešení jsou:
-   Primární i sekundární koncové body mají statické IP adresy, které často neměnit. IP adresa je 100.168.124.44 a IP adresu pro sekundární lokalitu je 100.168.124.43, že pro primární lokalitu.
-   Zóny DNS existuje pro primární a sekundární lokality. Například pro primární lokalitu koncový bod je prod.contoso.com a pro zálohování lokality je dr.contoso.com. Záznam DNS pro hlavní aplikaci označuje jako www.contoso.com také existuje.   
-   Hodnota TTL je na nebo nižší RTO SLA, nastavte v organizaci. Například pokud organizace nastaví RTO o odpověď po havárii aplikace na 60 minut, pak hodnota TTL musí být menší než 60 minut, pokud možno nižší, tím lépe. Můžete nastavit Azure DNS pro ruční převzetí služeb při selhání následujícím způsobem:
1. Vytvoření zóny DNS
2. Vytvoření záznamů DNS zóny
3. Aktualizovat záznam CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: Vytvoření DNS
Vytvoření zóny DNS (například www.contoso.com), jak je uvedeno níže:

![Vytvoření zóny DNS v Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Obrázek – vytvoření zóny DNS v Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: Vytvoření záznamů DNS zóny

V této zóně vytvořte tři záznamy (například - www.contoso.com, prod.contoso.com a dr.consoto.com) jako dole nezobrazují.

![Vytvoření záznamů DNS zóny](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Obrázek – vytvořit záznamy DNS zóny v Azure*

V tomto scénáři lokality, má hodnotu TTL 30 minut, který je dobře pod stanovené RTO a ukazovat na prod.contoso.com lokality produkční www.contoso.com. Tato konfigurace je během běžné obchodní operací. Hodnota TTL prod.contoso.com a dr.contoso.com byla nastavena na 300 sekund nebo 5 minut. Můžete použít Azure monitorování služby, například Azure monitorování nebo Statistika aplikace Azure nebo partnerských řešení například Dynatrace monitorování, můžete dokonce používat domácí vyvinuta řešení, která může monitorování nebo zjišťování aplikace nebo úrovně selhání virtuální infrastruktury.

### <a name="step-3-update-the-cname-record"></a>Krok 3: Aktualizace záznam CNAME

Jakmile bude zjištěno selhání, změňte hodnotu záznam tak, aby odkazoval na dr.contoso.com, jak je uvedeno níže:
       
![Aktualizovat záznam CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Obrázek – aktualizovat záznam CNAME ve službě Azure*

Do 30 minut, za které se většina překladače aktualizuje soubor v mezipaměti zóny, bude přesměrován na dr.contoso.com jakýkoli dotaz na www.contoso.com.
Můžete taky spustit následující příkaz rozhraní příkazového řádku Azure ke změně hodnoty CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Tento krok lze provést ručně nebo pomocí automatizace. Ho můžete provést ručně pomocí konzole nebo pomocí rozhraní příkazového řádku Azure. Azure SDK a rozhraní API můžete použít k automatizaci aktualizace CNAME, aby není nutný žádný ruční zásah. Automatizaci jde integrovat přímo prostřednictvím Azure functions nebo v rámci monitorování aplikace třetích stran nebo dokonce z místní.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak ruční převzetí služeb při selhání funguje pomocí Azure DNS
Vzhledem k tomu, že je DNS server z pásma převzetí služeb při selhání nebo havárie, je izolovat proti výpadkům. To umožňuje uživateli architektury scénář jednoduchého převzetí služeb při selhání, který je nákladově efektivní a bude fungovat všechny na čas za předpokladu, že operátor připojený k síti během po havárii a provádět překlopit. Když je vytvořena řešení, pak jeden musí zajistit server nebo služba spuštěna skript by měl být izolovány proti problém ovlivňující produkčního prostředí. Také berte v úvahu nízkou hodnotu TTL, který byl nastaven na zónu, aby resolver po celém světě uchová v mezipaměti pro dlouho koncový bod se zákazníci mohou přistupovat k lokalitě v rámci RTO. Pro studenou pohotovostní a světlý pilotního nasazení vzhledem k tomu, že některé prewarming a další aktivita správy může být nutný – jeden také získat dostatek času před provedením překlopit.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatické převzetí služeb při selhání pomocí Azure Traffic Manager
Pokud máte komplexní architektury a více sad prostředků schopná provádět stejnou funkci, můžete nakonfigurovat Azure Traffic Manageru (podle DNS) a zkontrolujte stav svých prostředků směrovat přenosy z prostředku není v pořádku pořádku prostředek. V následujícím příkladu oblasti primární i sekundární oblast mají úplné nasazení. Toto nasazení obsahuje cloudových služeb a databázi synchronizované. 

![Automatické převzetí služeb při selhání pomocí Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Obrázek - automatické převzetí služeb při selhání pomocí Azure Traffic Manager*

Pouze primární oblasti se však aktivně zpracovávat požadavky sítě od uživatele. Sekundární oblast stane aktivní jenom v případě, že primární oblasti dojde přerušení služby. V takovém případě směrovat všechny nové požadavky na síť sekundární oblast. Od zálohování databáze je téměř okamžitou, Vyrovnávání zatížení mají IP adresy, které můžou být zaškrtnuto, stavu a instance jsou vždy nahoru a spuštěna, tato topologie nabízí možnost přejdete nízkou RTO a převzetí služeb při selhání bez jakékoli ruční zásah. Oblasti sekundární převzetí služeb při selhání musí být připravení přejít na za provozu okamžitě po selhání primární oblasti.
Tento scénář je ideální pro použití nástroje Azure Traffic Manager, který má integrované sondy pro různé typy kontroly stavu včetně http nebo https a TCP. Azure Traffic manager má také stroj pravidel, která může být nakonfigurován k převzetí služeb při selhání, když dojde k selhání, jak je popsáno níže. Zvažte následující řešení pomocí Traffic Manager:
- Zákazník má koncový bod oblast č. 1 – známé jako prod.contoso.com se statickou IP adresu jako 100.168.124.44 a koncový bod oblast č. 2 – známé jako dr.contoso.com se statickou IP adresu jako 100.168.124.43. 
-   Každý z těchto prostředí je přední stěnou přes veřejný přístupných vlastnost jako nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení můžete nakonfigurovat tak, aby měl koncový bod na základě DNS nebo platný plně kvalifikovaný název domény (FQDN), jak je uvedeno výše.
-   Všechny instance v oblasti 2 jsou téměř v reálném čase replikace s 1 oblast. Kromě toho bitové kopie počítačů jsou aktuální, a všechna softwaru nebo konfigurační data je nainstalována oprava a odpovídají 1 oblast.  
-   Automatické škálování je předem předem nakonfigurován. 

Kroky prováděné převzetí služeb při selhání nakonfigurovat Azure Traffic Manager jsou následující:
1. Vytvoření nového profilu Azure Traffic Manager
2. Vytvoření koncových bodů v rámci profilu Správce provozu
3. Nastavení konfigurace stavu kontroly a převzetí služeb při selhání

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: Vytvoření nového profilu Azure Traffic Manager
Vytvoření nového profilu Azure Traffic manager s contoso123 název a vyberte metodu směrování jako s prioritou. Pokud máte existující skupinu prostředků, který chcete přidružit, můžete vybrat existující skupinu prostředků, jinak, vytvořte novou skupinu prostředků.

![Vytvořit profil služby Traffic Manager](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
*obrázek – vytvoření profilu Traffic Manageru*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: Vytvoření koncových bodů v rámci profilu Správce provozu

V tomto kroku vytvoříte koncové body, které odkazují na produkční a lokality obnovení po havárii. Zde, vyberte **typ** jako externí koncový bod, ale pokud prostředek je hostován v Azure, můžete zvolit **koncového bodu Azure** také. Pokud se rozhodnete **koncového bodu Azure**, zvolte položku **cíle prostředků** to znamená buď **služby App Service** nebo **veřejnou IP adresu** která je přidělena Azure. Priorita je nastavena jako **1** vzhledem k tomu, že je primární služba pro oblast 1.
Podobně vytvořte po havárii obnovení koncový bod v rámci Traffic Manager také.

![Vytvoření koncové body obnovení po havárii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Obrázek – vytvoření koncové body obnovení po havárii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: Nastavení konfigurace stavu kontroly a převzetí služeb při selhání

V tomto kroku nastavíte DNS TTL na 10 sekund, což je berou v úvahu většina internetových rekurzivní překladače. Tato konfigurace znamená, že žádné překladač služby DNS bude mezipaměti informace o více než 10 sekund. Pro nastavení monitorování koncového bodu je cesta aktuální sady na nebo nebo kořenové, ale můžete upravit nastavení koncového bodu vyhodnotit cestu, například prod.contoso.com/index. V příkladu níže ukazuje **https** jako protokol pro testování. Můžete však zvolit **http** nebo **tcp** také. Volba protokol závisí na ukončit aplikaci. Testování interval je nastaven na hodnotu 10 sekund, což umožňuje rychlé zjišťování a opakovaném je nastavena na hodnotu 3. V důsledku toho Traffic Manager bude převzetí služeb při selhání do druhého koncového bodu, když tři po sobě jdoucích intervaly zaregistrovat k chybě. Následující vzorec definuje celkovou dobu pro automatické převzetí služeb při selhání: čas pro převzetí služeb při selhání = TTL + opakujte * Probing interval a v takovém případě hodnota je 10 + 3 * 10 = 40 sekund (maximum).
Pokud opakovaném je nastavena na 1 a hodnota TTL nastavená na 10 sekund, potom čas pro převzetí služeb při selhání 10 + 1 * 10 = 20 sekund. Nastavte opakovaném na hodnotu větší než **1** eliminovat pravděpodobnost převzetí služeb při selhání z důvodu falešně pozitivních nebo blips žádné vedlejší sítě. 


![Nastavení kontroly stavu](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Obrázek – nastavení konfigurace stavu kontroly a převzetí služeb při selhání*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Funkce automatické převzetí služeb při selhání funguje pomocí Traffic Manager

Při havárii, získá zjištěný primární koncový bod a stav změní na **degradovaný** a lokalitě pro obnovení po havárii zůstane **Online**. Ve výchozím nastavení odešle Traffic Manager veškerý provoz na primární koncový bod (nejvyšší priorita). Primární koncový bod se zobrazí sníženou, směrování Traffic Manager provoz na druhý koncový bod zůstává v pořádku. Jeden má možnost konfigurovat další koncové body v rámci Traffic Manager které mohou slouží jako koncové body další převzetí služeb při selhání nebo jako nástroje pro vyrovnávání zatížení mezi koncovými body sdílení zatížení.

## <a name="next-steps"></a>Další postup
- Další informace o [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Další informace o [Azure DNS](../dns/dns-overview.md).









