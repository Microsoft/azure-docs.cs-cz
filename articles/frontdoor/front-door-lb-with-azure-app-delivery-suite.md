---
title: Služba Azure branou – služby Vyrovnávání zatížení se sadou Azure aplikace dodání | Dokumentace Microsoftu
description: Tento článek pomáhá získat informace o tom, jak Azure doporučuje Vyrovnávání zatížení pomocí jeho doručení sada aplikací
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 253ec8c0d1d6725e6ae5c47c79882284c633d6e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984462"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Vyrovnávání zatížení se sadou Azure application delivery

## <a name="introduction"></a>Úvod
Microsoft Azure poskytuje několik globální a místní služby pro správu, jak se distribuuje síťový provoz s vyrovnáváním zatížení: Traffic Manageru, branou služby, služba Application Gateway a nástroj pro vyrovnávání zatížení.  Spolu s mnoha oblastí Azure a zónové architektury, pomocí těchto služeb společně umožňují vytvářet robustní a škálovatelné vysoce výkonné aplikace.

![Sada doručování aplikací ][1]
 
Tyto služby jsou rozdělená do dvou kategorií:
1. **Globální služby Vyrovnávání zatížení** , jako je Traffic Manageru a branou distribuovat provoz od koncových uživatelů ve vaší místní back-EndY, napříč cloudy nebo dokonce i vaše hybridní místní služby. Vyrovnávání zatížení globálního směruje provoz na nejbližší back-endu služby a reaguje na změny v spolehlivosti služeb nebo výkonu zajistit nepřetržité a maximální výkon pro vaše uživatele. 
2. **Regionálních služeb pro vyrovnávání zatížení** , jako je Load balanceru úrovně Standard nebo služby Application Gateway poskytuje možnost distribuovat provoz v rámci virtuálních sítí (VNETs) ve virtuálních počítačích (VM) nebo koncové body služby oblastmi v rámci oblasti.

Kombinování globální a místní služby ve vaší aplikaci poskytuje začátku do konce spolehlivým, výkonným a zabezpečený způsob, jak směrovat provoz do a z vašich uživatelů k IaaS, PaaS a místních služeb. V další části jsme popisují každou z těchto služeb.

## <a name="global-load-balancing"></a>Globální Vyrovnávání zatížení
**Traffic Manager** poskytuje Vyrovnávání zatížení globálního DNS. Prohledá příchozí žádosti DNS a odpovídá zprávou v dobrém stavu back-end, v souladu se zásadami směrování vybraného zákazníka. Možnosti pro směrování metody jsou následující:
- Směrování k odeslání žadatel nejbližší back-endu z hlediska latence výkonu.
- Priorita směrování směrovat veškerý provoz do back-end, s jiných back-EndY jako back up.
- Vážené kruhové dotazování směrování, která distribuuje provoz podle váhy, který je přiřazen k každý back-endu.
- Geografické směrování k zajištění, že žadatele umístěné v konkrétní zeměpisné oblasti jsou směrované na back-EndY namapované na tyto oblasti (například všechny žádosti od Španělsko směrovat do oblasti Azure USA – východ)
- Podsíť směrování, která umožňuje mapování IP adresa rozsahu do back-EndY tak, aby se pošlou požadavky přicházející z těch zadané back-endu (například všichni uživatelé připojení z vaší podnikové Sídel rozsah IP adres by měl získat jiný webový obsah než obecné Uživatelé)

Klient se připojí přímo do tohoto back-endu. Azure Traffic Manager zjistí, že back-end není v pořádku a pak přesměruje klienty k jiné instanci v pořádku. Odkazovat na [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentaci se dozvíte informace o službě.

**Služba Azure branou** poskytuje akcelerace dynamického webu (DSA), včetně globální Vyrovnávání zatížení protokolu HTTP.  Dohlíží na příchozím požadavkům HTTP trasy na nejbližší služby back-end / oblast pro zadaný název hostitele, cestu adresy URL a nakonfigurovaná pravidla.  
Přední dveře ukončí požadavky HTTP na hranici sítě od Microsoftu a aktivně sondy pro detekci změn stavu nebo latence aplikací nebo infrastrukturou.  Přední dveře pak vždy směruje provoz nejrychlejší a k dispozici (v pořádku) back-endu. Odkazovat na přední dveře [směrování architektura](front-door-routing-architecture.md) podrobnosti a [metody směrování provozu](front-door-routing-methods.md) Další informace o službě.

## <a name="regional-load-balancing"></a>Regionální Vyrovnávání zatížení
Služba Application Gateway poskytuje kontroler doručování aplikací (ADC) jako službu, nabízí různé vrstvy 7 možnostmi Vyrovnávání zatížení pro vaši aplikaci. Umožňuje zákazníkům optimalizovat produktivitu webové farmy tím, že se ukončování protokolu SSL náročnou na procesor, ke službě application gateway. Další možnosti přesměrování vrstvy 7 obsahovat kruhové dotazování na distribuci příchozích přenosů, spřažení relace na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedné službě application gateway. Application Gateway lze nakonfigurovat jako brány přístupem k Internetu, pouze interní brány nebo kombinaci obojího. Služba Application Gateway je Azure plně spravované, škálovatelné a vysoce dostupné. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu.
Nástroj pro vyrovnávání zatížení je nedílnou součástí Azure SDN stack, poskytuje vysoce výkonné, s nízkou latencí vrstva 4 služby Vyrovnávání zatížení služby pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Můžete nakonfigurovat veřejné a vnitřní s vyrovnáváním zatížení koncových bodů a definovat pravidla pro namapování příchozí připojení do back endového fondu cílů s použitím TCP nebo HTTP zjišťování stavu možností pro správu dostupnost služeb.


## <a name="choosing-a-global-load-balancer"></a>Nástroj pro vyrovnávání zatížení globálního výběru
Při výběru nástroj pro vyrovnávání zatížení globálního mezi Traffic Manageru a Azure branou pro globální směrování, měli byste zvážit, co je podobné a čím se liší tyto dvě služby.   Obě služby poskytují
- **Redundance geografickým oblastem:** Pokud jedné oblasti ocitne mimo provoz, provoz bez problémů směruje do nejbližší oblasti bez jakéhokoli zásahu od vlastníka aplikace.
- **Nejbližší oblast směrování:** provozu se automaticky směrují na nejbližší oblasti

</br>Následující tabulka popisuje rozdíly mezi Traffic Manageru a branou služby Azure:</br>

| Traffic Manager | Azure Front Door Service |
| --------------- | ------------------------ |
|**Libovolný protokol:** protože Traffic Manager funguje ve vrstvě DNS, můžete směrovat jakýkoli typ síťového provozu; HTTP, TCP, UDP, atd. | **Akcelerace HTTP:** přenosy s branou jsou směrovány přes proxy server v hraniční z Microsoftu pro sítě.  Z tohoto důvodu žádosti protokolu HTTP (S), najdete v článku latenci a propustnost vylepšení snížení latence pro vyjednávání protokolu SSL a používání aktivní připojení z AFD do vaší aplikace.|
|**Směrování na místní:** se směrováním ve vrstvě DNS, provoz směrován vždy bodu z bodu.  Směrování od firemní pobočka do vašeho datového centra v místním prostředí může trvat přímé cestu. dokonce i ve vaší síti pomocí Traffic Manageru. | **Nezávislého škálování:** protože branou funguje s žádostí HTTP, je možné směrovat požadavky do jiné cesty adresy URL na různé back-end / regionálních služeb fondů (mikroslužeb) na základě pravidel a stavu jednotlivých aplikací mikroslužeb.|
|**Formát fakturace:** škáluje s uživateli a služby s více uživateli, plateaus ke snížení nákladů většího využití fakturaci na základě DNS. |**Vložené zabezpečení:** branou povoluje pravidla, jako je například omezení rychlosti a seznamu ACL IP-ing vám neumožňuje chránit váš back-end před přenosy přicházejí vaší aplikace. 

</br>Z důvodu výkonu, požadavky na provoz a výhody zabezpečení pro úlohy HTTP s branou doporučujeme, abyste zákazníci využívat branou pro své úlohy HTTP.    Traffic Manager a branou umožňuje paralelní sloužit veškerý provoz pro vaši aplikaci. 

## <a name="building-with-azures-application-delivery-suite"></a>Sestavování s využitím Azure application delivery suite 
Doporučujeme vám, že všechny weby, rozhraní API, služby se geograficky redundantní a zajištění provozu svým uživatelům z nejbližší (nejnižší latenci) umístění k nim kdykoli je to možné.  Kombinace služeb z Traffic Manageru, branou Service, služba Application Gateway a nástroje pro vyrovnávání zatížení umožňují vytvářet zonally a geograficky redundantní pro maximalizaci spolehlivost, škálování a výkonu.

V následujícím diagramu popisujeme příklad služby, který používá kombinaci těchto služeb k vytvoření globální webové služby.   V tomto případě má architekt používá Traffic Manageru směrovat do globální back-EndY pro soubor nebo objekt, při použití branou globálně směrovat cestami URL, které odpovídají vzoru/store / * ke službě se migrovali do služby App Service při směrování všech ostatních požadavky na místní brány Application Gateway.

V oblasti, ke svým službám IaaS vývojář aplikace se rozhodla, že žádné adresy URL, které odpovídají vzoru/imagí / * se obsluhují z vyhrazený fond virtuálních počítačů, které se liší od zbývající části webové farmy.

Výchozí fond virtuální počítač obsluhující dynamického obsahu se navíc musí komunikovat s back-end databáze, která je hostovaná v clusteru s vysokou dostupností. Celé nasazení je nastavený prostřednictvím Azure Resource Manageru.

Následující diagram znázorňuje architekturu tento scénář:

![Sada doručování aplikací podrobnosti architektury][2] 

> [!NOTE]
> V tomto příkladu je pouze jeden z mnoha možné konfigurace služby Vyrovnávání zatížení, které nabízí Azure. Traffic Manager, branou, služba Application Gateway a služby Load balancer úrovně lze kombinovat a spárují s nejlepší vyhovovala vašim potřebám Vyrovnávání zatížení. Například pokud přesměrování zpracování SSL nebo vrstvy 7 zpracování není nezbytné, nástroj pro vyrovnávání zatížení může být zastoupen Application Gateway.


## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvořit přední dveře](quickstart-create-front-door.md).
- Přečtěte si [fungování branou](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png