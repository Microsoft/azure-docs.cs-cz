---
title: Azure Load Balancer – přehled | Dokumentace Microsoftu
description: Přehled funkcí Azure Load Balancer, architektury a implementaci. Seznámení s fungováním nástroje pro vyrovnávání zatížení a využít ho v cloudu.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 4714a2792b30bfe5cb8a25f0b8912969858f5c34
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187083"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

S nástrojem Azure Load Balancer můžete škálovat svoje aplikace a poskytovat vysokou dostupnost pro vaše služby. Nástroj pro vyrovnávání zatížení podporuje další scénáře využití příchozí i odchozí, poskytuje nízkou latenci a vysokou propustnost a škálování až na úrovni milionů toků pro všechny aplikace TCP a UDP.  

Nástroj pro vyrovnávání zatížení distribuuje nové příchozí toky, které přicházejí na front-endu nástroje pro vyrovnávání zatížení do back-endový fond instancí, podle pravidel a sondy stavu. 

Kromě toho může nástroj pro vyrovnávání zatížení veřejnou poskytnout odchozí připojení pro virtuální počítače (VM) uvnitř virtuální sítě podle jejich privátní IP adresy na veřejné IP adresy překladu.

Nástroj Azure Load Balancer je k dispozici ve dvou skladových položkách: Basic a Standard. Existují rozdíly v škálování, funkce a ceny. Jakýkoli scénář, který je možné s Load balancer úrovně Basic můžete také vytvořit pomocí Load balanceru úrovně Standard, i když přístupů se mohou mírně lišit. Za použití nástroje pro vyrovnávání zatížení, je důležité se seznámit s principy a rozdíly specifické pro SKU.

## <a name="why-use-load-balancer"></a>Proč používat nástroj pro vyrovnávání zatížení? 

Můžete použít nástroje pro vyrovnávání zatížení Azure:

* Vyrovnávat zatížení příchozího internetového provozu do virtuálních počítačů. Tato konfigurace se označuje jako [nástroje pro vyrovnávání zatížení veřejnou](#publicloadbalancer).
* Provoz – nástroj pro vyrovnávání zatížení napříč virtuálními počítači ve virtuální síti. Front-endu nástroje pro vyrovnávání zatížení z místní sítě v hybridním scénáři můžete také kontaktovat. Oba scénáře použití konfigurace, která se označuje jako [interního nástroje load balancer](#internalloadbalancer).
* Port předávají provoz na konkrétní port na konkrétní virtuální počítače s příchozí síťové pravidel překladu adres (NAT).
* Zadejte [odchozí připojení](load-balancer-outbound-connections.md) pro virtuální počítače ve vaší virtuální síti s použitím nástroj pro vyrovnávání zatížení veřejnou.


>[!NOTE]
> Azure poskytuje sadu plně spravované řešení vyrovnávání zatížení pro vaše scénáře. Pokud chcete pro ukončení protokol zabezpečení TLS (Transport Layer) ("přesměrování zpracování SSL") nebo požadavek na HTTP/HTTPS, aplikačního zpracování, zkontrolujte [Application Gateway](../application-gateway/application-gateway-introduction.md). Pokud chcete pro službu DNS globální Vyrovnávání zatížení, přečtěte si [Traffic Manageru](../traffic-manager/traffic-manager-overview.md). Scénáře začátku do konce může mít užitek z kombinace těchto řešení podle potřeby.

## <a name="what-are-load-balancer-resources"></a>Co jsou prostředků nástroje pro vyrovnávání zatížení?

Prostředek nástroje pro vyrovnávání zatížení může existovat jako nástroj pro vyrovnávání zatížení veřejnou nebo interní nástroj. Funkce prostředek nástroje pro vyrovnávání zatížení jsou vyjádřeny jako front-endu, pravidla, sondu stavu a definice fondu back-endu. Virtuální počítače umístíte do back-endového fondu tak, že zadáte back-endový fond z virtuálního počítače.

Prostředků nástroje pro vyrovnávání zatížení jsou objekty, ve kterém můžete vyjádřit způsobu Azure by měla aplikace dosáhnout scénář, který chcete vytvořit svoji infrastrukturu více tenantů. Není k dispozici žádný přímý vztah mezi prostředků nástroje pro vyrovnávání zatížení a infrastruktury skutečný. Vytvoření nástroje pro vyrovnávání zatížení nevytváří instance a kapacita je vždy k dispozici. 

## <a name="fundamental-load-balancer-features"></a>Základní funkce nástroje pro vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení poskytuje následující základní funkce, pro aplikace TCP a UDP:

* **Vyrovnávání zatížení**

    S Azure Load Balancer můžete vytvořit pravidlo Vyrovnávání zatížení pro distribuci provozu, která dorazí na front-endu do back-endový fond instancí. Nástroj pro vyrovnávání zatížení používá algoritmus hash na základě pro distribuci příchozích toků a přepíše záhlaví toků do back-endový fond instancí odpovídajícím způsobem. Server je k dispozici pro příjem nových toků při sondu stavu Určuje koncový bod v dobrém stavu back-endu.
    
    Ve výchozím nastavení používá nástroj pro vyrovnávání zatížení pro mapování toků na dostupných serverů hodnotu hash 5 řazené kolekce členů skládající se z Zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol IP. Můžete vytvářet spřažení pro konkrétní zdrojové IP adresy ve výběru řazené kolekce členů 2 nebo 3 hash pro dané pravidlo. Všechny pakety ze stejného toku paketů doručení na stejnou instanci za vyrovnáváním zatížení front-endu. Když klient zahájí nový tok z stejnou zdrojovou IP adresu, změny zdrojového portu. 5-řazené kolekce členů v důsledku toho může dojít k provozu do přejděte ke koncovému bodu jiný back-end.

    Další informace najdete v tématu [distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md). Následující obrázek zobrazuje rozdělení na základě hodnoty hash:

    ![Na základě algoritmu hash distribuce](./media/load-balancer-overview/load-balancer-distribution.png)

    *Obrázek: Distribuce Hash-based*

* **Přesměrování portu**

    Pomocí nástroje pro vyrovnávání zatížení můžete vytvořit příchozí pravidlo NAT pro přesměrování provozu na portu z specifického portu konkrétní front-endové IP adresy na konkrétní port konkrétní back-end instance ve virtuální síti. Také to provádí stejnou hodnotu hash na základě distribuční jako Vyrovnávání zatížení. Běžné scénáře pro tuto funkci se relace protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) k jednotlivým instancím virtuálních počítačů ve virtuální síti Azure. Můžete namapovat více koncovým bodům s interním na různé porty na stejné IP adresy front-endu. Můžete je Vzdálená správa vašich virtuálních počítačů prostřednictvím Internetu bez nutnosti další jump box.

* **Aplikace bez a transparentnost**

    Nástroj pro vyrovnávání zatížení nekomunikuje přímo s TCP nebo UDP nebo aplikační vrstvy a jakékoli TCP nebo UDP aplikace scénář může být podporovaný.  Nástroj pro vyrovnávání zatížení neobsahuje ukončit nebo pocházejí z toků, interakci s datovou část toku, poskytuje žádná funkce brány vrstvy aplikace, a počet metod Handshake protocol vždy odehrávat přímo mezi klientem a instance back-endového fondu.  Odpověď na příchozí tok, který je vždy odpovědi z virtuálního počítače.  Pokud tok dorazí na virtuálním počítači, původní Zdrojová IP adresa je také zachována.  Několik příkladů, pro další ilustraci transparentnosti:
    - Každý koncový bod je pouze zodpoví virtuálního počítače.  Například TCP handshake dochází vždy mezi klientem a vybrané back-endového virtuálního počítače.  Odpověď na požadavek na front-endu je odpověď generovaných back-endového virtuálního počítače. Po úspěšném ověření připojení k front-end, jsou ověřování koncového připojení k aspoň jeden back-endového virtuálního počítače.
    - Datové části aplikace jsou transparentní pro nástroj pro vyrovnávání zatížení a jakékoli UDP nebo TCP aplikace může být podporovaný. Pro úlohy, které vyžadují za zpracování žádostí protokolu HTTP nebo manipulaci s datovými částmi vrstvy aplikace (například parsování adres URL protokolu HTTP), měli byste použít nástroj pro vyrovnávání zatížení vrstvy 7 stejně jako [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Protože je nezávislá na datovou část protokolu TCP nástroje pro vyrovnávání zatížení a snižování zátěže protokolu TLS ("šifrování SSL") není k dispozici, můžete vytvářet komplexní šifrované scénáře použití nástroje pro vyrovnávání zatížení a získat velké horizontální navýšení kapacity pro TLS aplikace tím, že se ukončuje připojení TLS na samotných virtuálních počítačů.  Například své TLS relace klíčovací kapacity je omezen pouze typ a počet virtuálních počítačů, které přidáte do fondu back-endu.  Pokud požadujete "Snižování zátěže protokolu SSL", zacházení vrstvy aplikace nebo chcete delegovat správu certifikátu do Azure, měli byste použít nástroj pro vyrovnávání zatížení vrstvy 7 pro Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway) místo.
        

* **Automatickou změnou konfigurace**

    Nástroj pro vyrovnávání zatížení okamžitě změní konfiguraci samotného při změně velikosti instancí směrem nahoru nebo dolů. Přidávání nebo odebírání virtuální počítače v back-endového fondu změní konfiguraci nástroje pro vyrovnávání zatížení bez další operace na prostředek nástroje pro vyrovnávání zatížení.

* **Sondy stavu**

     Pokud chcete zjistit stav instancí ve fondu back-endu, nástroje pro vyrovnávání zatížení používá sondy stavu služby, které definujete. Když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání nových připojení k instance v nedobrém stavu. Stávající připojení to neovlivní a pokračují až do ukončení aplikace flow, nastane časový limit nečinnosti, nebo virtuální počítač je vypnutý.

    Podporuje tři typy testů:

    - **Vlastní sondu protokolu HTTP**: Tento test můžete použít k vytvoření vlastní logiky určit stav instance back-endový fond. Nástroje pro vyrovnávání zatížení pravidelně sondy váš koncový bod (každých 15 sekund, ve výchozím nastavení). Instance se považuje za se stavem v pořádku, pokud odpoví HTTP 200 v časovém limitu (výchozí hodnoty 31 sekund). Jakýkoli stav než HTTP 200 způsobí, že tento test selže. Tento test je také užitečné pro implementaci vlastní logiky k odebrání instance otočení nástroje pro vyrovnávání zatížení. Můžete například nakonfigurovat instance, kterou chcete vrátit stav než 200, pokud instance je větší než 90 % využitím procesoru.  Tento test přepisuje výchozí kontroly agenta hosta.

    - **Vlastní sondu protokolu TCP**: Tento test závisí na vytvoření úspěšné relace TCP na portu definované sondy. Za předpokladu, existuje zadaný naslouchací proces ve virtuálním počítači, tento test uspěje. Pokud připojení bylo odmítnuto, test se nezdaří. Tento test přepisuje výchozí kontroly agenta hosta.

    - **Test agenta hosta**: nástroje pro vyrovnávání zatížení můžete také využít agent hosta ve virtuálním počítači. Agent hosta, který přijímá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. Pokud agent přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat a zastaví odesílání provozu do této instance. Nástroje pro vyrovnávání zatížení i nadále se pokoušejí připojit k instanci. Pokud agent hosta odpoví HTTP 200, nástroje pro vyrovnávání zatížení odesílá provoz do této instance znovu. Jsou testy agenta hosta _poslední možnost a nedoporučuje se_ když jsou možné konfigurace vlastní sondu protokolu HTTP nebo TCP. 
    
* **Odchozí připojení (SNAT)**

    Všechny odchozí toky z privátních IP adres ve virtuální síti na veřejné IP adresy v síti internet lze přeložit na IP adresu front-endu nástroje pro vyrovnávání zatížení. Když veřejnou front-endu se váže na back-endu virtuálních počítačů mimo jiné pravidlo Vyrovnávání zatížení, Azure programy odchozí připojení automaticky převést na veřejnou front-endovou IP adresu.

    * Povolte snadný upgrade a zotavení po havárii služeb, protože front-endu můžete dynamicky namapována na jinou instanci služby.
    * Jednodušší správu řízení přístupu (ACL) seznamu k. Seznamy ACL vyjadřují front-endové IP adresy neměňte jako škálování služby směrem nahoru nebo dolů nebo získat znovu nasadil.  Překlad odchozí připojení k menšímu počtu IP adres než počítačů můžete snížit zatížení na seznam povolených.

    Další informace najdete v tématu [odchozí připojení](load-balancer-outbound-connections.md).

Load balancer úrovně Standard má další funkce specifické pro skladovou Položku mimo tyto základy. Projděte si zbývající část tohoto článku najdete podrobnosti.

## <a name="skus"></a> Porovnání SKU nástroje pro vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení podporuje základní a standardní SKU, každý lišící se škálování scénář, funkce a ceny. Jakýkoli scénář, který je možné s Load balancer úrovně Basic lze také vytvořit pomocí Load balanceru úrovně Standard. Ve skutečnosti rozhraní API pro obě položky jsou podobné a vyvolané prostřednictvím specifikace SKU. Rozhraní API pro podporu SKU pro nástroj pro vyrovnávání zatížení a veřejná IP adresa je k dispozici od verze rozhraní API 2017-08-01. Obě položky mají stejné obecné rozhraní API a struktura.

Ale v závislosti na SKU, které zvolíte, kompletní scénáře konfigurace může mírně lišit. Dokumentace nástroje pro vyrovnávání zatížení zdůrazňuje při článek platí jenom pro určité skladové položky. Můžete porovnat a znát rozdíly, najdete v následující tabulce. Další informace najdete v tématu [Load balanceru úrovně Standard přehled](load-balancer-standard-overview.md).

>[!NOTE]
> Load balancer úrovně Standard by měl přijmout nové návrhy. 

Samostatných virtuálních počítačů, dostupnosti a škálovací sady virtuálních počítačů může být připojena k pouze jedna skladová položka, nikdy obojí. Pokud je použijete s veřejnými IP adresami, nástroje pro vyrovnávání zatížení a veřejnou IP adresu SKU musí odpovídat. Nástroj pro vyrovnávání zatížení a SKU veřejné IP nejsou mutable.

_Je osvědčeným postupem určete SKU explicitně, i když ještě není povinné._  V současné době jsou synchronizovány požadované změny na minimum. Pokud není zadán skladovou Položku, je interpretován jako záměr použijte verzi rozhraní API 2017-08-01 základní SKU.

>[!IMPORTANT]
>Load balancer úrovně Standard je nový produkt nástroje pro vyrovnávání zatížení a z velké části nadmnožinou Load balancer úrovně Basic. Existují důležité a rozhodnout vědomě a záměrně rozdíly mezi těmito dvěma produkty. Jakýkoli scénář začátku do konce, který je možné s Load balancer úrovně Basic lze vytvořit také pomocí Load balanceru úrovně Standard. Pokud už jste zvyklí Load balancer úrovně Basic, měli byste seznámit s Load Balanceru úrovně Standard pro pochopení nejnovější změny v chování mezi úrovněmi Standard a Basic a jejich dopad. V této části najdete pečlivě.

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-endu | až 1 000 instancí | až 100 instancí |
| Koncové body back-Endového fondu | jakýkoli virtuální počítač v jedné virtuální sítě, včetně kombinace virtuální počítače, skupiny dostupnosti, škálovacích sad virtuálních počítačů. | virtuální počítače ve škálovací sady nebo na virtuálním počítači jeden dostupnosti |
| Zóny dostupnosti | zónové a zónově redundantních front-endů pro příchozí a odchozí, odchozí toky mapování překonat selhání zóny Vyrovnávání zatížení mezi zónami | / |
| Diagnostika | Azure Monitor, vícedimenzionálních metrik, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (SNAT úspěšné i neúspěšné toky), měření roviny aktivních dat. | Azure Log Analytics pro veřejného Load Balanceru úrovně pouze SNAT vyčerpání výstraha, počet stavu back-endového fondu |
| HA porty | interní nástroj pro vyrovnávání zatížení | / |
| Ve výchozím nastavení zabezpečení | Výchozí uzavřel za veřejné IP adresy a nástroje pro vyrovnávání zatížení koncové body a skupiny zabezpečení sítě musí použít na explicitní seznam povolených pro provoz tok | Výchozí otevřený, skupiny zabezpečení sítě volitelné |
| [Odchozí připojení](load-balancer-outbound-connections.md) | Několik front-endů s za odhlásit pravidlo Vyrovnávání zatížení. Odchozí scénář _musí_ explicitně vytvořit pro virtuální počítač, abyste mohli použít odchozí připojení.  [Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) dostupná bez odchozí připojení a ne započítávat zpracovaná data.  Všechny veřejné IP adresy, včetně služeb Azure PaaS, která není k dispozici jako koncové body služby virtuální sítě, musíte kontaktovat prostřednictvím odchozího připojení a počet směrem k zpracovaná data. Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, nejsou k dispozici odchozí připojení přes výchozí SNAT. Přenosový protokol konkrétní založené na protokolu Příchozí pravidlo Vyrovnávání zatížení je výstupní programování SNAT. | Jeden front-endu, když jsou k dispozici několik front-endů vybraného náhodně.  Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, použije se výchozí SNAT. |
| [Několik front-endů](load-balancer-multivip-overview.md) | Příchozí a [odchozí](load-balancer-outbound-connections.md) | Pouze příchozí |
| [Sonda stavu dolů chování](load-balancer-custom-probe-overview.md) | Připojení TCP zůstat naživu na instanci test dolů __a__ na všechny testy dolů | Připojení TCP zůstat naživu na instanci test mimo provoz. Ukončete všechna připojení TCP na všechny testy dolů |
| Operace správy | Většina operací < 30 sekund | 60 – 90 sekund typické |
| SLA | 99,99 % pro cestu k datům s dva virtuální počítače v dobrém stavu | Implicitní v SLA k virtuálním počítačům | 
| Ceny | Bude účtovat podle počtu pravidel, zpracování dat příchozí nebo odchozí přidruženého prostředku  | bez poplatků |

Další informace najdete v tématu [omezení služby nástroje pro vyrovnávání zatížení](https://aka.ms/lblimits). Podrobnosti Load balanceru úrovně Standard najdete v tématu [přehled](load-balancer-standard-overview.md), [ceny](https://aka.ms/lbpricing), a [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Koncepty

### <a name = "publicloadbalancer"></a>Veřejný load balancer

Nástroj pro vyrovnávání zatížení veřejnou mapuje privátní IP adresu a číslo portu virtuálního počítače a naopak pro přenosy odpovědí veřejnou IP adresu a číslo portu příchozího provozu z virtuálního počítače. Při použití pravidla Vyrovnávání zatížení můžete distribuovat určité typy přenosů mezi několika virtuálními počítači nebo službami. Například můžete rozložit zatížení webového požadavku provozu napříč několika webovými servery.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který je sdílen mezi tři virtuální počítače pro veřejné a privátní port TCP 80. Tyto tři virtuální počítače v sadě s vyrovnáváním zatížení.

![Příklad nástroje pro vyrovnávání zatížení veřejnou](./media/load-balancer-overview/IC727496.png)

*Obrázek: Nahrajte vyrovnávání webového provozu s využitím nástroj pro vyrovnávání zatížení veřejnou*

Pokud internetoví klienti odesílat požadavky na webovou stránku na veřejnou IP adresu webové aplikace na portu TCP 80, Azure Load Balancer distribuuje požadavky na třech virtuálních počítačích v sadě s vyrovnáváním zatížení. Další informace o algoritmech Vyrovnávání zatížení najdete v tématu [funkce nástroje pro vyrovnávání zatížení](load-balancer-overview.md##fundamental-load-balancer-features) části tohoto článku.

Ve výchozím nastavení Azure Load Balancer distribuuje síťový provoz rovnoměrně mezi několik instancí virtuálních počítačů. Můžete také nakonfigurovat spřažení relace. Další informace najdete v tématu [distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Interního nástroje load balancer

Interní nástroj směruje provoz jenom k prostředkům, které jsou uvnitř virtuální sítě nebo sítě VPN, který používáte pro přístup k infrastruktury Azure. V tomto ohledu interního nástroje se liší od nástroj pro vyrovnávání zatížení veřejnou. Infrastruktura Azure omezuje přístup na IP adresy s vyrovnáváním zatížení front-endový virtuální sítě. front-endové IP adresy a virtuální sítě jsou přístupné nesmí nikdy přímo do koncového bodu sítě internet. Interní-obchodní aplikace v Azure a jsou přístupné z v rámci Azure nebo z místních prostředků.

Službě interní služby load balancer umožňuje následující typy Vyrovnávání zatížení:

* **V rámci virtuální sítě**: Vyrovnávání zatížení z virtuálních počítačů v virtuální sítě k sadě virtuálních počítačů, které se nacházejí ve stejné virtuální síti.
* **Pro virtuální síť mezi různými místy**: načtení vyrovnávání z místních počítačů k sadě virtuálních počítačů, které se nacházejí ve stejné virtuální síti. 
* **Při vytváření víceúrovňových aplikací**: Vyrovnávání zatížení pro internetové vícevrstvé aplikace, kde úrovní back-end nejsou přístupem k Internetu. Vrstvami back-end vyžadují Vyrovnávání zatížení provozu z internetové vrstvy (viz následující obrázek).
* **Obchodních aplikací**: Vyrovnávání zatížení pro-obchodní aplikace, které jsou hostované v Azure bez dalších zatížení vyrovnávání hardwaru nebo softwaru. Tento scénář obsahuje místní servery, které jsou v sadě počítačů, jejichž provoz je s vyrovnáváním zatížení.

![Příklad interní služby load balancer](./media/load-balancer-overview/IC744147.png)

*Obrázek: Vyrovnávání vícevrstvé aplikace s použitím obou nástroje pro vyrovnávání zatížení veřejné a vnitřní zatížení*

## <a name="pricing"></a>Ceny
Standardní služby Load balancer úrovně využití se účtuje podle počtu nakonfigurovaných pravidel Vyrovnávání zatížení a množství zpracovaných příchozích a odchozích dat. Load balancer úrovně Standard informace, o cenách najdete [ceny nástroje pro vyrovnávání zatížení](https://azure.microsoft.com/pricing/details/load-balancer/) stránky.

Load balancer úrovně Basic se nabízí zdarma.

## <a name="sla"></a>SLA

Informace o standardních SLA nástroje pro vyrovnávání zatížení, přejděte [SLA nástroje pro vyrovnávání zatížení](https://aka.ms/lbsla) stránky. 

## <a name="limitations"></a>Omezení

- Nástroj pro vyrovnávání zatížení je produkt TCP nebo UDP pro vyrovnávání zatížení a přesměrování portu pro tyto konkrétní protokoly IP.  Pravidla Vyrovnávání zatížení a pravidla příchozího překladu adres jsou podporované pro TCP a UDP a není podporována pro ostatní IP protokoly včetně protokolu ICMP. Nástroj pro vyrovnávání zatížení neukončí, reagovat nebo jinak interakci s datovou částí toku UDP nebo TCP. Nejedná se o proxy serveru. Úspěšné ověření připojení k front-end přijme místo integrované s stejný protokol použitý v zatížení vyrovnávání nebo příchozí pravidlo NAT (TCP nebo UDP) _a_ alespoň jeden z vašich virtuálních počítačů musí generovat pro klienta pro odpověď Podívejte se na odpověď front-endu.  Nepřijímá odpověď integrovaných z front-endu nástroje pro vyrovnávání zatížení Určuje, že žádné virtuální počítače nebyly schopné reagovat.  Není možné pracovat s front-endu nástroje pro vyrovnávání zatížení, aniž by virtuální počítač, který je schopný reagovat.  To platí i pro odchozí připojení kde [port krycí SNAT](load-balancer-outbound-connections.md#snat) se podporuje jenom pro TCP a UDP; žádné další IP protokoly včetně protokolu ICMP se také nezdaří.  Přiřazení veřejné IP adresy na úrovni instance zmírnit.
- Na rozdíl od veřejné Vyrovnávání zatížení, který bude poskytovat [odchozí připojení](load-balancer-outbound-connections.md) při přesunu z privátních IP adres ve virtuální síti na veřejné IP adresy, interní nástroje pro vyrovnávání zatížení nepřekládat odchozí pochází připojení k front-endu interního nástroje Load Balancer obě jsou v privátní adresní prostor IP adres.  Tím se vyhnete potenciál pro SNAT vyčerpání uvnitř jedinečný interní adresní prostor IP adres ve kterém se nevyžaduje překlad.  Vedlejším účinkem je, že pokud odchozí tok z virtuálního počítače v back-endového fondu pokouší toku pro front-endu interního nástroje pro vyrovnávání zatížení ve fondu, který se nachází _a_ je namapována na sebe sama, obě úsecích tok neodpovídají a tok se nezdaří.  Pokud tok nemapují zpět na stejný virtuální počítač v back-endový fond, který vytvoří tok front-endu, tok proběhne úspěšně.   Když tok mapuje sám na sebe odchozího toku se zobrazí na pocházejí z virtuálního počítače do front-endu a odpovídající příchozího toku se zobrazí pocházejí z virtuálního počítače na sebe sama. Z hostovaného operačního systému se příchozí a odchozí části stejný tok neshodují ve virtuálním počítači. Zásobník protokolu TCP nerozpozná tyto polovinami stejný tok jako součást stejný tok jako zdroje a cíle se neshodují.  Když tok mapuje na jakýkoli jiný virtuální počítač v back-endový fond, polovinami tok budou odpovídat a virtuálního počítače můžou úspěšně reagovat na tok.  Příznak pro tento scénář je přerušované připojení, vypršení časového limitu. Existuje několik běžných řešení pro spolehlivě dosažení tohoto scénáře (pocházející toky z back-endového fondu do back-endové fondy příslušné interní nástroj pro vyrovnávání zatížení front-endu), mezi které patří buď vložení proxy třetích stran za interní zatížení Nástroje pro vyrovnávání nebo [pomocí pravidel stylu DSR](load-balancer-multivip-overview.md).  Zatímco veřejný Load Balancer můžete použít ke zmírnění, je výsledný scénář náchylná k [SNAT vyčerpání](load-balancer-outbound-connections.md#snat) a mělo by se vyhnout, pokud pečlivě spravované.

## <a name="next-steps"></a>Další postup

Nyní máte přehled o Azure Load Balancer. Abyste mohli začít s používáním nástroje pro vyrovnávání zatížení, vytvořte si ho, vytvořit virtuální počítače s vlastní nainstalované rozšíření služby IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači. Další informace o postupu [vytvoření Load Balanceru úrovně Basic](quickstart-create-basic-load-balancer-portal.md) rychlý start.
