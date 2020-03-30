---
title: Více frontendů – Azure Load Balancer
description: S tímto studijním programem můžete začít s přehledem více frontendů v Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0a54416a70a8561edfad5915944100e0ce686bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771253"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Více frontendů pro Azure Load Balancer

Azure Load Balancer umožňuje služby vyrovnávání zatížení na více portech, více IP adres nebo obojí. Můžete použít veřejné a interní definice vyrovnávání zatížení pro toky vyrovnávání zatížení v rámci sady virtuálních aplikací.

Tento článek popisuje základy této schopnosti, důležité koncepty a omezení. Pokud máte v úmyslu zpřístupnit služby pouze na jedné adrese IP, můžete najít zjednodušené pokyny pro [veřejné](load-balancer-get-started-internet-portal.md) nebo [interní](load-balancer-get-started-ilb-arm-portal.md) konfigurace nástroje pro vyrovnávání zatížení. Přidání více front-endů je přírůstkové do konfigurace jednoho front-endu. Pomocí konceptů v tomto článku můžete kdykoli rozšířit zjednodušenou konfiguraci.

Když definujete nástroj Azure Load Balancer, front-end a konfigurace back-endového fondu jsou propojeny s pravidly. Sonda stavu odkazuje pravidlo se používá k určení, jak jsou odesílány nové toky do uzlu v back-endovém fondu. Front-end (aka VIP) je definován 3 řazené kolekce členů skládající se z IP adresy (veřejné nebo interní), přenosového protokolu (UDP nebo TCP) a čísla portu z pravidla vyrovnávání zatížení. Back-endový fond je kolekce konfigurací IP virtuálních počítačů (součást prostředku nic), které odkazují na back-endový fond nástroje pro vyrovnávání zatížení.

Následující tabulka obsahuje některé příklady front-end konfigurace:

| Front-end | IP adresa | Protokol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

V tabulce jsou uvedeny čtyři různé front-endy. Frontendy #1, #2 a #3 jsou jeden front-end s více pravidly. Používá se stejná adresa IP, ale port nebo protokol se liší pro každý front-end. Frontendy #1 a #4 jsou příkladem více frontendů, kde se stejný front-endový protokol a port znovu používají ve více frontendech.

Azure Load Balancer poskytuje flexibilitu při definování pravidel vyrovnávání zatížení. Pravidlo deklaruje, jak je adresa a port v front-endu mapována na cílovou adresu a port v back-endu. To, zda jsou back-endové porty znovu použity napříč pravidly, závisí na typu pravidla. Každý typ pravidla má specifické požadavky, které mohou ovlivnit konfiguraci hostitele a návrh sondy. Existují dva typy pravidel:

1. Výchozí pravidlo bez opakovaného použití back-endového portu
2. Pravidlo plovoucí IP adresy, kde jsou znovu použity back-endové porty

Nástroj Azure Load Balancer umožňuje kombinovat oba typy pravidel ve stejné konfiguraci nástroje pro vyrovnávání zatížení. Vyrovnávání zatížení je můžete použít současně pro daný virtuální virtuální pokladny nebo libovolnou kombinaci, pokud budete dodržovat omezení pravidla. Typ pravidla, který zvolíte, závisí na požadavcích aplikace a složitosti podpory této konfigurace. Měli byste vyhodnotit, které typy pravidel jsou nejlepší pro váš scénář.

Tyto scénáře dále zkoumáme tak, že začneme s výchozím chováním.

## <a name="rule-type-1-no-backend-port-reuse"></a>#1 typ pravidla: Žádné opětovné použití back-endového portu

![Více front-end ilustrace se zelenou a fialovou front-end](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

V tomto scénáři front-endy jsou nakonfigurovány takto:

| Front-end | IP adresa | Protokol | port |
| --- | --- | --- | --- |
| ![zelený front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![fialový front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP je cíl příchozího toku. V back-endovém fondu každý virtuální počítač zveřejňuje požadovanou službu na jedinečném portu na DIP. Tato služba je přidružena k front-endu prostřednictvím definice pravidla.

Definujeme dvě pravidla:

| Pravidlo | Front-end mapy | Chcete-li back-end fondu |
| --- | --- | --- |
| 1 |![zelený front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![Virtuální IP adresa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, 2. ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Úplné mapování v Azure Load Balancer je teď následující:

| Pravidlo | IP adresa front-endu | Protokol | port | Cíl | port |
| --- | --- | --- | --- | --- | --- |
| ![zelené pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |IP adresa DIP |80 |
| ![fialové pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |IP adresa DIP |81 |

Každé pravidlo musí vytvořit tok s jedinečnou kombinací cílové adresy IP a cílového portu. Změnou cílového portu toku může více pravidel dodávat toky do stejného dipu na různých portech.

Sondy stavu jsou vždy přesměrovány na DIP virtuálního zařízení. Musíte zajistit, že vaše sonda odráží stav virtuálního počítače.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Typ pravidla #2: znovu použít back-endový port pomocí plovoucí IP adresy

Nástroj Azure Load Balancer poskytuje flexibilitu pro opakované použití portu front-endu napříč více front-endy bez ohledu na použitý typ pravidla. Kromě toho některé scénáře aplikace upřednostňují nebo vyžadují stejný port, který má být používán více instancemi aplikace na jednom virtuálním počítači v back-endovém fondu. Mezi běžné příklady opakovaného použití portů patří clustering pro vysokou dostupnost, síťová virtuální zařízení a vystavení více koncových bodů TLS bez opětovného šifrování.

Pokud chcete znovu použít back-endový port pro více pravidel, musíte v definici pravidla povolit plovoucí IP adresu.

"Plovoucí IP" je terminologie Azure pro část, co je známé jako direct server return (DSR). DSR se skládá ze dvou částí: topologie toku a schéma mapování IP adres. Na úrovni platformy Azure Balancer vždy pracuje v topologii toku DSR bez ohledu na to, zda je povolena plovoucí IP je povolena nebo ne. To znamená, že odchozí část toku je vždy správně přepsána tak, aby tok přímo zpět do počátku.

S výchozím typem pravidla Azure zveřejňuje tradiční schéma mapování IP adres vyrovnávání zatížení pro snadné použití. Povolením plovoucí IP změní schéma mapování IP adres, aby byla umožněna další flexibilita, jak je vysvětleno níže.

Následující diagram znázorňuje tuto konfiguraci:

![Více front-end ilustrace se zelenou a fialovou frontend s DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

V tomto scénáři má každý virtuální modul ve fondu back-end tři síťová rozhraní:

* DIP: virtuální nekarta přidružená k virtuálnímu počítači (konfigurace IP prostředků nic Azure)
* Frontend 1: rozhraní zpětné smyčky v rámci hostovaného operačního systému, které je nakonfigurováno s IP adresou front-endu 1
* Frontend 2: rozhraní zpětné smyčky v rámci hostovaného operačního systému, které je nakonfigurováno s IP adresou frontendu 2

Pro každý virtuální server v back-endovém fondu spusťte na příkazovém řádku systému Windows následující příkazy.

Chcete-li získat seznam názvů rozhraní, které máte na virtuálním počítači, zadejte tento příkaz:

    netsh interface show interface 

Pro nic virtuálního počítače (spravovaný Azure) zadejte tento příkaz:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
   (nahraďte název rozhraní názvem tohoto rozhraní)

Pro každé rozhraní zpětné smyčky, které jste přidali, opakujte tyto příkazy:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
   (nahraďte název rozhraní názvem tohoto rozhraní zpětné smyčky)
     
    netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
   (nahraďte název rozhraní názvem tohoto rozhraní zpětné smyčky)

> [!IMPORTANT]
> Konfigurace rozhraní zpětné smyčky se provádí v rámci hostovaného operačního systému. Tato konfigurace není prováděna ani spravována Azure. Bez této konfigurace nebudou pravidla fungovat. Definice sondy stavu použít DIP virtuálního virtuálního soudu spíše než rozhraní zpětné smyčky představující front-end DSR. Proto musí vaše služba poskytnout odpovědi na zrcadlový port DIP, které odrážejí stav služby nabízené na rozhraní zpětné smyčky představující frontovou výrobu DSR.


Předpokládejme stejnou front-endovou konfiguraci jako v předchozím scénáři:

| Front-end | IP adresa | Protokol | port |
| --- | --- | --- | --- |
| ![zelený front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![fialový front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definujeme dvě pravidla:

| Pravidlo | Front-end | Mapování na back-endový fond |
| --- | --- | --- |
| 1 |![Pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (ve VM1 a VM2) |
| 2 |![Pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (ve VM1 a VM2) |

V následující tabulce je uvedeno úplné mapování v záložně zatížení:

| Pravidlo | IP adresa front-endu | Protokol | port | Cíl | port |
| --- | --- | --- | --- | --- | --- |
| ![zelené pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |stejné jako frontend (65.52.0.1) |stejné jako frontend (80) |
| ![fialové pravidlo](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |stejné jako frontend (65.52.0.2) |stejné jako frontend (80) |

Cílem příchozího toku je front-endová IP adresa v rozhraní zpětné smyčky ve virtuálním počítači. Každé pravidlo musí vytvořit tok s jedinečnou kombinací cílové adresy IP a cílového portu. Změnou cílové IP adresy toku je možné znovu použít port na stejném virtuálním počítači. Vaše služba je vystavena balancer vazbou na ip adresu front-endu a port příslušné rozhraní zpětné smyčky.

Všimněte si, že tento příklad nezmění cílový port. I když se jedná o scénář plovoucí IP, Azure Balancer také podporuje definování pravidla přepsání cílového portu back-endu a aby se lišilod cílového portu front-endu.

Typ pravidla plovoucí IP je základem několika vzorů konfigurace nástroje pro vyrovnávání zatížení. Jeden příklad, který je aktuálně k dispozici, je [SQL AlwaysOn s konfigurací více naslouchacích procesy.](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) V průběhu času budeme dokumentovat více z těchto scénářů.

## <a name="limitations"></a>Omezení

* Více front-end konfigurace jsou podporovány pouze s virtuálními počítači IaaS.
* S plovoucí IP pravidlo, vaše aplikace musí použít primární konfiguraci IP pro odchozí Toky SNAT. Pokud se vaše aplikace váže na ip adresu front-endu nakonfigurovanou v rozhraní zpětné smyčky v hostovaném osu, není odchozí SNAT Azure k dispozici k přepsání odchozího toku a tok se nezdaří.  Zkontrolujte [odchozí scénáře](load-balancer-outbound-connections.md).
* Veřejné IP adresy mají vliv na fakturaci. Další informace naleznete v tématu [Ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Platí limity předplatného. Další informace naleznete v tématu [Omezení služeb](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) pro podrobnosti.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [odchozí připojení,](load-balancer-outbound-connections.md) abyste pochopili dopad více front-endů na chování odchozího připojení.
