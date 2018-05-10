---
title: Přehled služby Vyrovnávání zatížení Azure | Microsoft Docs
description: Přehled funkce Vyrovnávání zatížení Azure, architekturu a implementace. Zjistěte, jak funguje nástroj pro vyrovnávání zatížení a využít v cloudu.
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
ms.date: 05/03/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8a3eedb5a3d96eedd1a64d85afdb58f8961df272
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-load-balancer"></a>Co je nástroj pro vyrovnávání zatížení Azure?

Pomocí nástroje pro vyrovnávání zatížení Azure můžete škálování aplikací a vytvoření vysoké dostupnosti pro vaše služby. Nástroj pro vyrovnávání zatížení podporuje scénáře příchozí a odchozí, zajišťuje nízkou latencí a vysokou propustnost a škáluje až miliony toků pro všechny aplikace, TCP a UDP.  

Nástroj pro vyrovnávání zatížení distribuuje nové příchozí toky, které přicházejí na Vyrovnávání zatížení front-endu na fond back-end instancí podle pravidel a sondy stavu. 

Kromě toho může Vyrovnávání zatížení veřejnou poskytnout odchozí připojení pro virtuální počítače (VM) uvnitř virtuální sítě jejich soukromé IP adresy na veřejné IP adresy.

Azure nástroj pro vyrovnávání zatížení je k dispozici ve dvou SKU: Basic a Standard. Existují rozdíly v škálování, funkce a ceny. Všechny scénáře, který je možné s základní pro vyrovnávání zatížení můžete také vytvořit s standardní pro vyrovnávání zatížení, i když přístupy se může mírně lišit. Jak se dozvíte o vyrovnávání zatížení, je důležité se seznámit s rozdíly specifický pro SKU a základní informace.

## <a name="why-use-load-balancer"></a>Proč používat nástroj pro vyrovnávání zatížení? 

Můžete použít nástroj pro vyrovnávání zatížení Azure na:

* Vyrovnávání zatížení příchozí internetové přenosy na virtuální počítače. Tato konfigurace se označuje jako [nástroj pro vyrovnávání zatížení veřejnou](#publicloadbalancer).
* Provoz Vyrovnávání zatížení napříč virtuálními počítači ve virtuální síti. Mohou být využity také nástroj pro vyrovnávání zatížení klientská část z místní sítě v hybridním scénáři. Oba scénáře použití konfigurace, která se označuje jako [nástroj pro vyrovnávání zatížení pro vnitřní](#internalloadbalancer).
* Port přesměrovat provoz konkrétní port, na konkrétní virtuálních počítačů s pravidla příchozích síťových adres překlad (NAT).
* Zadejte [odchozí připojení](load-balancer-outbound-connections.md) pro virtuální počítače uvnitř virtuální sítě pomocí služby Vyrovnávání zatížení veřejné.


>[!NOTE]
> Azure poskytuje sada plně spravovaná řešení vyrovnávání zatížení pro vaše scénáře. Pokud hledáte ukončení protokol zabezpečení TLS (Transport Layer) ("přesměrování zpracování SSL") nebo požadavku na-protokolu HTTP nebo HTTPS, zpracování aplikační vrstvě, přečtěte si [Application Gateway](../application-gateway/application-gateway-introduction.md). Pokud hledáte pro globální DNS Vyrovnávání zatížení, přečtěte si [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vaše scénáře začátku do konce může mít užitek z kombinace těchto řešení podle potřeby.

## <a name="what-are-load-balancer-resources"></a>Jaké jsou prostředky služby Vyrovnávání zatížení?

Prostředek pro vyrovnávání zatížení může existovat jako nástroj pro vyrovnávání zatížení veřejnou nebo k interní vyrovnávání zátěže. Funkce prostředek pro vyrovnávání zatížení jsou vyjádřené jako front-end, pravidlo, test stavu a definici fondu back-end. Virtuální počítače umístíte do fondu back-end zadáním fond back-end z virtuálního počítače.

Prostředky nástroje pro vyrovnávání zatížení jsou objekty v rámci kterých můžete express, jak by měla Azure programu její infrastruktuře víceklientské k dosažení scénář, který chcete vytvořit. Není k dispozici žádný přímý vztah mezi prostředky nástroje pro vyrovnávání zatížení a skutečný infrastruktury. Vytvoření služby Vyrovnávání zatížení nepodporuje vytvoření instance a kapacita je vždy k dispozici. 

## <a name="fundamental-load-balancer-features"></a>Základní funkce služby Vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení poskytuje následující základní možnosti pro TCP a UDP aplikace:

* **Vyrovnávání zatížení**

    S Azure Vyrovnávání zatížení můžete vytvořit pravidlo Vyrovnávání zatížení pro provoz, který dorazí na front-endu na fond back-end instance rozdělení. Nástroj pro vyrovnávání zatížení používá algoritmus hash na základě pro distribuci příchozí tok dat a přepíše hlavičky tok tak, aby fond back-end instancí odpovídajícím způsobem. Server je k dispozici pro příjem nové toky při test stavu označuje funkční koncový bod back-end.
    
    Ve výchozím nastavení používá nástroj pro vyrovnávání zatížení hodnota hash 5 řazené kolekce členů tvořený zdrojové IP adresy, zdrojového portu, cílové IP adresy, cílový port a protokol IP k mapování toků na dostupné servery. Můžete vytvářet spřažení na IP adresu konkrétního zdroje vyjádření výslovného do řazené kolekce členů 2 nebo 3 hash pro dané pravidlo. Všechny pakety stejného toku paketů dorazí na stejnou instanci za vyrovnáváním zatížení front-endu. Když klient inicializuje nový tok ze stejné zdrojové IP, portu změny zdrojů. 5-řazené kolekce členů v důsledku toho může dojít k provozu přejít k jinému koncovému bodu back-end.

    Další informace najdete v tématu [režim distribuce nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md). Následující obrázek zobrazuje distribuci založenou na hash:

    ![Na základě hodnoty hash distribuce](./media/load-balancer-overview/load-balancer-distribution.png)

    *Obrázek: Distribuce Hash-based*

* **Přesměrování portu**

    S nástrojem pro vyrovnávání zatížení můžete vytvořit příchozí pravidlo NAT port přesměrovat provoz z konkrétní port konkrétní front-end IP adresy na konkrétní port konkrétní instance back-end připojení k virtuální síti. To lze provést také stejnou hodnotu hash na základě distribuce jako Vyrovnávání zatížení. Běžné scénáře pro tuto funkci se relace protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) k jednotlivým instancím virtuálních počítačů ve virtuální síti Azure. Můžete namapovat více vnitřních koncových bodů s různé porty ve stejné front-end IP adresy. Můžete je používat pro vzdálenou správu virtuálních počítačů prostřednictvím Internetu bez nutnosti pole na další přechod.

* **Aplikace lhostejné a transparentní**

    Nástroj pro vyrovnávání zatížení nekomunikuje přímo s TCP nebo UDP nebo aplikační vrstvu a všechny TCP nebo UDP aplikační scénář může být podporovaný.  Nástroj pro vyrovnávání zatížení neobsahuje ukončit nebo pocházejí toky, pracovat s datovou část toku, poskytuje žádná funkce brány vrstvy aplikace a metodou handshake protocol vždy odehrávat přímo mezi klientem a instance fond back-end.  Odpověď na příchozí tok je vždy odpověď z virtuálního počítače.  Při toku dorazí na virtuálním počítači, je také zachována původní zdrojovou IP adresu.  Pár příkladů dále upravit průhlednost:
    - Každý koncový bod je pouze odpovědi Virtuálním počítačem.  Například metoda handshake TCP dochází vždy mezi klientem a vybraný virtuální počítač back-end.  Odpověď na žádost o front-end je odpověď generované virtuálních počítačů v back-end. Pokud jste úspěšně ověřit připojení k front-endu, ověřujete koncová připojení k aspoň jednomu virtuálnímu počítači back-end.
    - Datové části aplikace jsou transparentní pro nástroj pro vyrovnávání zatížení a všechny UDP nebo TCP aplikace může být podporovaný. Pro úlohy, které vyžadují pro zpracování požadavku HTTP nebo zpracování datových částí vrstvy aplikace (například analýzy adres URL protokolu HTTP), měli byste použít nástroj pro vyrovnávání zatížení vrstvy 7 jako [Application Gateway](https://azure.microsoft.com/en-us/services/application-gateway).
    - Protože nástroj pro vyrovnávání zatížení nerozlišuje datové části TCP a přesměrování zpracování protokolu TLS (dále jen "šifrování SSL") není k dispozici, můžete sestavit koncová šifrované scénáře použití služby Vyrovnávání zatížení a získání velké škálování pro TLS aplikace se ukončuje připojení TLS na virtuální počítač.  Například relace TLS vytvoření kapacity omezen pouze podle typu a počtu virtuálních počítačů, které přidáte do fondu back-end.  Pokud budete potřebovat "Snižování zátěže protokolu SSL", ošetření vrstvy aplikace nebo chcete delegovat správu certifikátů do Azure, měli byste použít nástroj pro vyrovnávání zatížení vrstvy 7 Azure [Application Gateway](https://azure.microsoft.com/en-us/services/application-gateway) místo.
        

* **Automatické překonfigurování**

    Nástroj pro vyrovnávání zatížení okamžitě automatických při změně velikosti instancí nahoru nebo dolů. Přidání nebo odebrání virtuálních počítačů z fondu back-end změní konfiguraci nástroje pro vyrovnávání zatížení bez další operace na prostředek pro vyrovnávání zatížení.

* **Sondy stavu služby**

     Pokud chcete zjistit stav instancí ve fondu back-end, Vyrovnávání zatížení používá sondy stavu, které definujete. Když sondu přestane reagovat, nástroje pro vyrovnávání zatížení zastaví odesílání nových připojení k instancím není v pořádku. Existující připojení nejsou ovlivněna a budou pokračovat, dokud aplikace ukončí tok, dojde k časový limit nečinnosti, nebo virtuální počítač je vypnutý.

    Jsou podporovány tři typy sondy:

    - **Vlastní test paměti HTTP**: Tento test můžete použít k vytvoření vlastní logiky k určení stavu instance fond back-end. Nástroje pro vyrovnávání zatížení pravidelně sondy váš koncový bod (každých 15 sekund, ve výchozím nastavení). Instance se považuje za pořádku, pokud ji odpoví HTTP 200 během časového limitu (výchozí hodnoty 31 sekund). Tento test selhání způsobí, že všechny stav než HTTP 200. Tento test je také užitečné pro implementaci vlastní logiky k odebrání instance otočení Vyrovnávání zatížení. Můžete například nakonfigurovat instanci systému na vrátit stav bez 200, pokud instance je větší než 90 procent procesoru.  Tento test přepíše výchozí kontroly agenta hosta.

    - **Vlastní test paměti TCP**: Tento test závisí na vytvoření úspěšné relace TCP port definované testu. Tento test je úspěšné, dokud zadaný naslouchací proces do virtuálního počítače existuje. Pokud připojení bylo odmítnuto, že test se nezdaří. Tento test přepíše výchozí kontroly agenta hosta.

    - **Test agenta hosta (na platformě jako virtuální služby [PaaS] počítače pouze)**: nástroje pro vyrovnávání zatížení můžete také používat agent hosta ve virtuálním počítači. Agent hosta, který přijímá a odpoví odpověď HTTP 200 OK jenom v případě, že instance je ve stavu Připraveno. Když agent přestane reagovat s 200 OK protokolu HTTP, nástroje pro vyrovnávání zatížení označí instance jako reagovat a zastaví odesílání provozu do této instance. Nástroje pro vyrovnávání zatížení bude nadále se pokoušejí připojit k instanci. Pokud agenta hosta odpoví HTTP 200, nástroje pro vyrovnávání zatížení odešle provoz do této instance znovu. Sondy agenta hosta jsou poslední možnost a nedoporučuje se, když je možné, HTTP nebo TCP konfigurace vlastní test paměti. 
    
* **Odchozí připojení (překládat pomocí SNAT)**

    Všechny odchozí toky ze soukromých IP adres ve virtuální síti na veřejné IP adresy v síti internet lze přeložit na adresu IP front-endové služby Vyrovnávání zatížení. Když veřejných front-endu je vázaný na virtuální počítač back-end mimo jiné pravidlo Vyrovnávání zatížení, programy Azure odchozí připojení, které mají být automaticky převedeny na veřejnou IP adresu front-endu.

    * Povolte snadný upgrade a zotavení po havárii služeb, protože front-endu lze dynamicky mapovat k jiné instanci služby.
    * Jednodušší správu řízení přístupu (ACL) seznamu k. Seznamy ACL, vyjádřené jako front-end IP adresy nemění jako služby škálování nahoru nebo dolů nebo získat znovu nasazena.  Překladu odchozí připojení k menší počet IP adres než počítačů můžete snížit zatížení vytvoření seznamu povolených.

    Další informace najdete v tématu [odchozí připojení](load-balancer-outbound-connections.md).

Nástroj pro vyrovnávání zatížení má další funkce specifická pro SKU nad rámec těchto základy. Zkontrolujte zbývající část tohoto článku podrobnosti.

## <a name="skus"></a> Porovnání SKU služby Vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení podporuje základní a standardní SKU, každý liší ve scénáři měřítku, funkce a ceny. Všechny scénáře, který je možné s základní pro vyrovnávání zatížení lze vytvořit také pomocí nástroj pro vyrovnávání zatížení. Ve skutečnosti rozhraní API pro obě položky jsou podobné a vyvolaných prostřednictvím specifikace SKU. Rozhraní API pro podporu SKU pro nástroj pro vyrovnávání zatížení a veřejné IP adresy je k dispozici od verze rozhraní API 2017-08-01. Obě položky mají stejné obecné rozhraní API a struktura.

Ale v závislosti na SKU, které zvolíte, úplného scénáře konfigurace může se mírně liší. Dokumentace nástroje pro vyrovnávání zatížení volá při článku se vztahuje pouze na konkrétní SKU. Porovnat a pochopit rozdíly, najdete v následující tabulce. Další informace najdete v tématu [nástroj pro vyrovnávání zatížení přehled](load-balancer-standard-overview.md).

>[!NOTE]
> Pokud používáte novější scénář návrhu, zvažte použití nástroj pro vyrovnávání zatížení. 

Samostatné virtuální počítače, skupiny dostupnosti a sady škálování virtuálního počítače může být připojena k pouze jednoho identifikátoru SKU, nikdy obě. Při jejich používání s veřejné IP adresy, nástroj pro vyrovnávání zatížení a veřejnou IP adresu SKU musí shodovat. Nástroj pro vyrovnávání zatížení a veřejné IP SKU nejsou měnitelný.

_Je osvědčeným postupem zadejte SKU explicitně, i když ještě není povinné._  V tomto okamžiku jsou synchronizovány na minimum požadované změny. Pokud není zadán SKU, interpretuje se jako záměr použít verzi rozhraní API 2017-08-01 základní SKU.

>[!IMPORTANT]
>Nástroj pro vyrovnávání zatížení je nový produkt nástroj pro vyrovnávání zatížení a z velké části nadmnožinou základní nástroj pro vyrovnávání zatížení. Jsou důležité a záměrné rozdíly mezi tyto dva produkty. Všechny scénáře klient server, který je možné s základní pro vyrovnávání zatížení můžete také vytvořit s nástroj pro vyrovnávání zatížení. Pokud jste už používá základní Vyrovnávání zatížení, byste si měli přečíst s standardní pro vyrovnávání zatížení pochopit nejnovější změny v chování mezi Standard a Basic a jejich dopad. V této části najdete pečlivě.

| | [Standardní SKU](load-balancer-standard-overview.md) | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-end | Až 1 000 instance. | Až 100 instance. |
| Koncové body fond back-end | Žádné virtuální počítače v jedné virtuální sítě, včetně blend virtuálních počítačů, skupiny dostupnosti a sady škálování virtuálního počítače. | Nastavení virtuálních počítačů v jedné dostupnosti nebo škálovací sady virtuálních počítačů. |
| Zóny dostupnosti Azure | Zónově redundantní a oblastmi front-end pro příchozí a odchozí, odchozí tok mapování zůstanou platné i po selhání zóny cross zóny Vyrovnávání zatížení. | / |
| Diagnostika | Azure monitorování vícerozměrných metriky, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (úspěšné i neúspěšné překládat pomocí SNAT toky), aktivní data roviny měření. | Azure analýzy protokolů pro veřejné načíst pouze vyrovnávání, překládat pomocí SNAT vyčerpání výstraha, počet stavu fondu back-end. |
| HA porty | Interní vyrovnávání zátěže. | / |
| Ve výchozím nastavení zabezpečení | Ve výchozím nastavení zavřít pro veřejné IP adresy a zatížení vyrovnávání koncové body. Pro tok provozu musí být skupinu zabezpečení sítě umožňuje explicitně povolených entity. | Výchozí otevřete skupinu zabezpečení sítě, které jsou volitelné. |
| Odchozí připojení | Více přední končí za pravidlo výslovný nesouhlas s. Odchozí scénář _musí_ explicitně vytvořit pro virtuální počítač, abyste mohli použít odchozí připojení. [Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) dostupný bez odchozí připojení a nepočítá zpracování dat. Všechny veřejné IP adresy, včetně služby Azure PaaS, které jsou k dispozici jako koncové body služby virtuální sítě, musí být dosaženo přes odchozí připojení a zpracování dat, se počítá. Když pouze k pro vyrovnávání zatížení pro vnitřní obsluhuje virtuálních počítačů, nejsou k dispozici odchozí připojení přes výchozí překládat pomocí SNAT. Odchozí překládat pomocí SNAT programování je protokol pro přenos konkrétní, založené na protokolu Příchozí pravidlo Vyrovnávání zatížení. | Jeden front-endu, náhodně vybrané, pokud existuje více front-end. Když pouze k pro vyrovnávání zatížení pro vnitřní obsluhuje virtuální počítač, použije se výchozí překládat pomocí SNAT. |
| Více front-end | Příchozí a odchozí. | Pouze příchozí. |
| Operace správy | Většinu operací < 30 sekund. | 60-90 sekund typické. |
| SLA | 99,99 procento pro cestu k datům s dva virtuální počítače v pořádku. | Implicitní ve smlouvě SLA virtuálních počítačů. | 
| Ceny | Poplatky jsou založené na počet pravidel a zpracování dat příchozí nebo odchozí které jsou přidruženy k prostředku.  | Bez poplatků. |

Další informace najdete v tématu [omezení služby pro vyrovnávání zatížení](https://aka.ms/lblimits). Nástroj pro vyrovnávání zatížení podrobnosti najdete v tématu [přehled](load-balancer-standard-overview.md), [ceny](https://aka.ms/lbpricing), a [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Koncepty

### <a name = "publicloadbalancer"></a>Nástroj pro vyrovnávání zatížení veřejnou

Nástroj pro vyrovnávání zatížení veřejnou mapuje privátní IP adresu a číslo portu virtuálního počítače a naopak přenosům odpovědi veřejnou IP adresu a port číslo příchozí provoz z virtuálního počítače. Použitím pravidla Vyrovnávání zatížení můžete distribuovat specifické typy přenosů mezi více virtuálních počítačů nebo služeb. Můžete například šíří zatížení provozu webové žádosti napříč několika webovými servery.

Následující obrázek znázorňuje koncový bod Vyrovnávání zatížení sítě pro webový provoz, která je sdílena mezi tři virtuální počítače pro veřejné a privátní port TCP 80. Tyto tři virtuální počítače jsou v sadě s vyrovnáváním zatížení.

![Příklad nástroje pro vyrovnávání zatížení veřejnou](./media/load-balancer-overview/IC727496.png)

*Obrázek: Načtení vyrovnávání webového provozu pomocí služby Vyrovnávání zatížení veřejnou*

Pokud internetoví klienti odesílat požadavky na webovou stránku na veřejnou IP adresu webové aplikace na portu TCP 80, Vyrovnávání zatížení Azure rozděluje požadavky na tři virtuální počítače v sadě s vyrovnáváním zatížení. Další informace o algoritmy Vyrovnávání zatížení, najdete v článku [stránku přehled nástroje pro vyrovnávání zatížení](load-balancer-overview.md#load-balancer-features).

Ve výchozím nastavení nástroj pro vyrovnávání zatížení Azure distribuuje síťový provoz mezi více instancí virtuálního počítače. Můžete také nakonfigurovat spřažení relace. Další informace najdete v tématu [režim distribuce nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Interní zátěže.

Interní nástroj přesměruje přenosy pouze na prostředky, které jsou ve virtuální síti nebo který používat síť VPN pro přístup k infrastruktury Azure. V tomto ohledu interní nástroj se liší od služby Vyrovnávání zatížení veřejné. Infrastrukturu Azure omezuje přístup na Vyrovnávání zatížení sítě front-end IP adresy virtuální sítě. Front-end IP adresy a virtuální sítě jsou zveřejněné nikdy přímo k Internetu koncový bod. Interní-obchodní aplikace spustit v Azure a ke kterým se přistupuje z Azure nebo z místních prostředků.

Interní nástroj umožňuje následující typy Vyrovnávání zatížení:

* **V rámci virtuální sítě**: Vyrovnávání zatížení z virtuálních počítačů v virtuální sítě na sadu virtuálních počítačů, které se nacházejí v rámci stejné virtuální síti.
* **Pro virtuální síť mezi různými místy**: vyrovnávání z místního počítače na sadu virtuálních počítačů, které se nacházejí v rámci stejné virtuální síti zatížení. 
* **Pro vícevrstvé aplikace**: pro internetový vícevrstvé aplikace, kde vrstvy back-end nejsou internetového Vyrovnávání zatížení. Back-end úrovně vyžadují, Vyrovnávání zatížení provozu z internetové vrstvy (viz následující obrázek).
* **Pro-obchodní aplikace**: pro-obchodní aplikace, které jsou hostované v Azure bez další zátěže vyrovnávání hardwaru nebo softwaru Vyrovnávání zatížení. Tento scénář zahrnuje na místní servery, které jsou v sadě počítačů, jejichž provoz s vyrovnáváním zatížení.

![Příklad nástroje pro vyrovnávání zatížení interní](./media/load-balancer-overview/IC744147.png)

*Obrázek: Vyrovnávání vícevrstvé aplikace s použitím obou nástroje pro vyrovnávání zatížení veřejné a interní zatížení*

## <a name="pricing"></a>Ceny
Standardní využití Vyrovnávání zatížení je účtován na základě počtu nakonfigurovaných pravidel Vyrovnávání zatížení a množství zpracovaná příchozí a odchozí data. Pro nástroj pro vyrovnávání zatížení ceny informace, přejděte na [nástroj pro vyrovnávání zatížení ceny](https://azure.microsoft.com/pricing/details/load-balancer/) stránky.

Základní nástroj pro vyrovnávání zatížení je k dispozici zdarma.

## <a name="sla"></a>SLA

Další informace o standardní smlouvě SLA pro vyrovnávání zatížení, [SLA nástroje pro vyrovnávání zatížení](https://aka.ms/lbsla) stránky. 

## <a name="limitations"></a>Omezení

- Nástroj pro vyrovnávání zatížení je produkt TCP nebo UDP pro vyrovnávání zatížení a předávání port pro tyto konkrétní protokoly IP.  Pravidla Vyrovnávání zatížení a příchozího pravidla NAT jsou podporované pro TCP a UDP a není podporován pro jiné protokoly IP včetně protokolu ICMP. Nástroj pro vyrovnávání zatížení nezavře, reagovat nebo jinak interakci s datovou část toku UDP nebo TCP. Není proxy server. Úspěšné ověření připojení k front-endu vyžaduje místní integrované s stejný protokol použitý v vyrovnávání nebo příchozí pravidlo NAT zatížení (TCP nebo UDP) _a_ alespoň jeden z vašich virtuálních počítačů musíte vygenerovat odpověď pro klienta Chcete-li zobrazit na odpověď od front-endu.  Nepřijaté odpovědi integrované z front-endu nástroje pro vyrovnávání zatížení označuje, že žádné virtuální počítače nebyly schopné reagovat.  Není možné pracovat s front-endu bez schopné reagovat virtuálního počítače pro vyrovnávání zatížení.  To platí také pro odchozí připojení kde [port maskovaným překládat pomocí SNAT](load-balancer-outbound-connections.md#snat) je podporována pouze pro TCP a UDP; žádné jiné protokoly IP, včetně ICMP se také nezdaří.  Přiřaďte veřejná IP adresa úrovni instance zmírnit.
- Na rozdíl od veřejných Vyrovnávání zatížení, které poskytují [odchozí připojení](load-balancer-outbound-connections.md) při přechodu ze soukromých IP adres ve virtuální síti na veřejné IP adresy, interní nástroje pro vyrovnávání zatížení nepřevádět odchozí pochází připojení k front-endu interní pro vyrovnávání zatížení jako obě jsou prostor privátní IP adresy.  Tím je zabráněno případným překládat pomocí SNAT vyčerpání uvnitř jedinečné interní adresní prostor IP adres kde překlad se nevyžaduje.  Vedlejším účinkem je, že pokud odchozího toku z virtuálního počítače ve fondu back-end pokusů o toku do front-endu interní služby Vyrovnávání zatížení ve fondu, který se nachází _a_ je namapovaný na sebe, oba úsecích toku neshodují a tok se nezdaří. .  Pokud toku nemapují zpět do stejného virtuálního počítače ve fondu back-end, který vytvoří toku front-endu, bude úspěšné toku.   Když toku mapuje sám na sebe odchozího toku pravděpodobně pocházet z virtuálního počítače do front-endu a odpovídající příchozí tok, zobrazí se jenom z virtuálního počítače na sebe sama. Z hostovaného operačního systému se příchozí a odchozí součástí stejného toku neshodují ve virtuálním počítači. Zásobník protokolu TCP nerozpozná tyto polovina stejného toku jako součást stejného toku jako zdroj a cíl se neshodují.  Při toku mapuje k žádné virtuální počítače ve fondu back-end, bude odpovídat polovina toku a virtuální počítač může úspěšně reagovat na toku.  Tento příznak pro tento scénář je časové limity nepřerušované připojení. Existuje několik běžných dočasných řešení pro tento scénář spolehlivě dosažení (pocházející toky z back-end fondu k back-end fondu příslušné interní nástroj pro vyrovnávání zatížení front-endu) mezi které patří buď vložení proxy třetích stran za interní zatížení Nástroje pro vyrovnávání nebo [pomocí pravidel stylu DSR](load-balancer-multivip-overview.md).  Zatímco veřejný Vyrovnávání zatížení můžete použít ke zmírnění, výsledná scénář je náchylné k [vyčerpání překládat pomocí SNAT](load-balancer-outbound-connections.md#snat) a je nutno Pokud pečlivě spravované.

## <a name="next-steps"></a>Další postup

Teď máte Přehled služby Vyrovnávání zatížení Azure. Pokud chcete začít používat nástroj pro vyrovnávání zatížení, vytvořit, vytvořit virtuální počítače s vlastní rozšíření služby IIS nainstalovaná a vyrovnávání zatížení webové aplikace mezi virtuálními počítači. Další informace, jak zjistit, [vytvořit základní nástroj pro vyrovnávání zatížení](quickstart-create-basic-load-balancer-portal.md) rychlý start.
