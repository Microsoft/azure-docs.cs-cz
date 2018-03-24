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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 4f46e796ff1ab85c0061c70ff9a725a6945a4f5d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-load-balancer-overview"></a>Azure Load Balancer – přehled

Azure nástroj pro vyrovnávání zatížení umožňuje škálování aplikací a vytvořte vysokou dostupnost pro vaše služby. Nástroj pro vyrovnávání zatížení podporuje scénáře příchozí i odchozí a poskytuje nízkou latencí a vysokou propustnost a škáluje až miliony toků pro všechny aplikace, TCP a UDP.   

Nástroj pro vyrovnávání zatížení provede distribuci nové příchozí toky přicházejících na front-endové služby Vyrovnávání zatížení na back-end fondu instancí podle pravidel a sondy stavu.  

Kromě toho veřejné nástroje pro vyrovnávání zatížení můžete zadat taky odchozí připojení pro virtuální počítače ve virtuální síti jejich soukromé IP adresy na veřejné IP adresy.

Azure nástroj pro vyrovnávání zatížení je k dispozici ve dvou různých SKU: Basic a Standard.  Existují rozdíly v škálování, funkce a ceny.  Každý scénář možné s základní pro vyrovnávání zatížení můžete také vytvořit s standardní pro vyrovnávání zatížení, i když přístupu se může mírně lišit.  Jak se dozvíte o vyrovnávání zatížení, je důležité se seznámit s rozdíly specifický pro SKU a základní informace.

## <a name="why-use-load-balancer"></a>Proč používat nástroj pro vyrovnávání zatížení? 

Azure nástroj pro vyrovnávání zatížení lze provádět:

* Načtěte vyrovnávání příchozí přenosy z Internetu k virtuálním počítačům. Tato konfigurace se označuje jako [veřejné nástroje pro vyrovnávání zatížení](#publicloadbalancer).
* Vyrovnávání zatížení přenosů mezi virtuálními počítači ve virtuální síti. Mohou být využity také front-end pro vyrovnávání zatížení z místní sítě v hybridním scénáři. Oba scénáře použití konfigurace, která se označuje jako [interní nástroj pro vyrovnávání zatížení](#internalloadbalancer).
* Port přesměrovat provoz na konkrétní port u konkrétních virtuálních počítačů s příchozích pravidel NAT.
* Zadejte [odchozí připojení](load-balancer-outbound-connections.md) pro virtuální počítače uvnitř virtuální sítě pomocí veřejné nástroje pro vyrovnávání zatížení.


>[!NOTE]
> Azure poskytuje sada plně spravovaná řešení pro vaše scénáře Vyrovnávání zatížení.  Pokud hledáte ukončení protokolu TLS ("přesměrování zpracování SSL") nebo zpracování vrstvy aplikace HTTP/HTTPS, přečtěte si [Application Gateway](../application-gateway/application-gateway-introduction.md).  Pokud hledáte pro globální DNS Vyrovnávání zatížení, přečtěte si [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Vaše scénáře začátku do konce může těžit z kombinace těchto řešení podle potřeby.

## <a name="what-is-load-balancer"></a>Co je nástroj pro vyrovnávání zatížení?

Nástroj pro vyrovnávání zatížení prostředků může existovat jako veřejné nástroje pro vyrovnávání zatížení nebo interní pro vyrovnávání zatížení. Funkce Vyrovnávání zatížení prostředků jsou vyjádřené jako front-end, pravidlo, test stavu a definici fondu back-end.  Virtuální počítače se umístí do fondu back-end zadáním fondu back-end z virtuálního počítače.

Prostředky nástroje pro vyrovnávání zatížení jsou objekty v rámci kterých můžete express, jak by měla Azure programu její infrastruktuře víceklientské k dosažení scénáře, který chcete vytvořit.  Není žádný přímý vztah mezi prostředky pro vyrovnávání zatížení a skutečný infrastruktury; vytvoření služby Vyrovnávání zatížení nepodporuje vytvoření instance a kapacita je vždy k dispozici. 

## <a name="fundamental-load-balancer-features"></a>Základní funkce služby Vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení poskytuje následující základní možnosti pro TCP a UDP aplikace:

* **Vyrovnávání zatížení**

    Azure Vyrovnávání zatížení můžete vytvořit pravidlo pro distribuci přenosů přicházejících u front-end k instancím fond back-end pro vyrovnávání zatížení.  Ji používá algoritmus hash na základě pro distribuci příchozí tok dat a přepíše hlavičky toků na back-end fondu instance odpovídajícím způsobem. Server je k dispozici pro příjem nové toky při test stavu označuje koncový bod v pořádku back-end.
    
    Ve výchozím nastavení použije hodnotu hash 5 řazené kolekce členů tvořený zdrojové IP adresy, zdrojového portu, cílové IP adresy, cílový port a protokol IP k mapování toků na dostupné servery.  Můžete vytvářet spřažení na IP adresu konkrétního zdroje vyjádření výslovného do řazené kolekce členů 2 nebo 3 hash pro dané pravidlo.  Všechny pakety stejného toku paketů dorazí na stejnou instanci za front-endu Vyrovnávání zatížení sítě.  Když klient inicializuje nový tok ze stejné zdrojové IP, portu změny zdrojů. Výsledný 5-n-tice může způsobit provoz v důsledku přejít na koncový bod různých back-end.

    Další informace najdete v tématu [režim distribuce nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md). Následující obrázek znázorňuje rozdělení na základě hodnoty hash:

    ![Na základě hodnoty hash distribuce](./media/load-balancer-overview/load-balancer-distribution.png)

    *Obrázek - Hash-based distribuce*

* **Přesměrování portu**

    Azure nástroj pro vyrovnávání zatížení vám umožní vytvořit příchozí pravidlo NAT port přesměrovat provoz z konkrétní port konkrétní front-end IP adresy na konkrétní port instance konkrétní back-end připojení k virtuální síti. To lze provést také stejnou hodnotu hash na základě distribuce jako Vyrovnávání zatížení.  Běžné scénáře pro tato schopnost se relace protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) pro instance jednotlivých virtuálních počítačů ve virtuální síti.  Můžete namapovat více vnitřních koncových bodů na jiné porty na stejnou IP adresu front-endu. Ty můžete použít ke vzdálené správě virtuálních počítačů prostřednictvím Internetu bez nutnosti pole na další přechod.

* **Aplikace lhostejné a transparentní**

    Nástroj pro vyrovnávání zatížení nekomunikuje přímo s TCP nebo UDP nebo aplikační vrstvu a všechny TCP nebo může být podporovaný scénář aplikace založené na protokolu UDP.  Například při vyrovnávání zatížení nezavře TLS sám sebe, můžete sestavit a škálovat TLS aplikací pomocí nástroje pro vyrovnávání zatížení a ukončit připojení protokolu TLS na virtuálním počítači sám sebe. Nástroj pro vyrovnávání zatížení nezavře tokem a protokol ověřování metodou handshake jsou vždy přímo mezi klientem a instance vybrané hodnoty hash back-end fondu. Například metoda handshake TCP je vždy mezi klientem a back-end vybraný virtuální počítač.  A je odpověď na žádost o front-end odpovědí vygenerovat z virtuálního počítače back-end.  Odchozí síťový výkon služby Vyrovnávání zatížení je pouze omezený tak, že virtuální počítač zvolíte SKU a toky zůstane aktivní po delší dobu Pokud časový limit nečinnosti není přístupný.

* **Automatické překonfigurování**

    Azure nástroj pro vyrovnávání zatížení okamžitě automatických při změně velikosti instancí nahoru nebo dolů. Přidání nebo odebrání virtuálních počítačů z fondu back-end změní konfiguraci nástroje pro vyrovnávání zatížení bez další operace u prostředku Vyrovnávání zatížení.

* **Sondy stavu služby**

    Azure Vyrovnávání zatížení používá sondy stavu služby můžete definovat, chcete-li zjistit stav instancí ve fondu back-end. Když sondu přestane reagovat, nástroje pro vyrovnávání zatížení zastaví odesílání nových připojení k instancím není v pořádku. Existující připojení nebude mít dopad a bude pokračovat, dokud aplikace ukončí tok, dojde k časový limit nečinnosti, nebo virtuální počítač je vypnutý.

    Jsou podporovány tři typy sondy:

    - **Vlastní test paměti HTTP:** slouží k vytvoření vlastní logiky k určení stavu instance fond back-end. Nástroje pro vyrovnávání zatížení bude pravidelně testů váš koncový bod (každých 15 sekund, ve výchozím nastavení). Instance se považuje za pořádku, pokud ji odpoví HTTP 200 během časového limitu (výchozí hodnoty 31 sekund). Tento test selhání způsobí, že všechny stav než HTTP 200.  To je užitečné pro implementaci vlastní logiky k odebrání instance otočení Vyrovnávání zatížení. Můžete například nakonfigurovat instanci systému na vrátit stav bez 200, pokud je instance vyšší než 90 % využití procesoru.   Tento test přepíše výchozí kontroly agenta hosta.

    - **Vlastní test paměti TCP:** tento test spoléhá na úspěšné vytvoření relace TCP port definované testu.  Také zadaný naslouchací proces ve virtuálním počítači existuje, bude tento test úspěšný. Pokud připojení bylo odmítnuto, že test se nezdaří. Tento test přepíše výchozí kontroly agenta hosta.

    - **Test agenta hosta (na platformě jako služby pouze ve virtuálních počítačích):** nástroje pro vyrovnávání zatížení můžete také používat agent hosta ve virtuálním počítači. Agent hosta, který přijímá a odpoví odpověď HTTP 200 OK jenom v případě, že instance je ve stavu Připraveno. Když agent přestane reagovat s 200 OK protokolu HTTP, nástroje pro vyrovnávání zatížení označí instance jako reagovat a zastaví odesílání provozu do této instance. Nástroje pro vyrovnávání zatížení bude nadále se pokoušejí připojit k instanci. Pokud agenta hosta odpoví HTTP 200, bude nástroj pro vyrovnávání zatížení znovu odeslat provoz tuto instanci.  Sondy agenta hosta jsou poslední možnost a by se neměla používat, když je možné, HTTP nebo TCP konfigurace vlastní test paměti. 
    
* **Odchozí připojení (zdroj NAT)**

    Všechny odchozí toky ze soukromých IP adres ve virtuální síti na veřejné IP adresy v síti Internet lze přeložit na IP adresu front-endové služby Vyrovnávání zatížení. Když veřejnou front-endovou je vázaný na virtuální počítač back-end mimo jiné pravidlo Vyrovnávání zatížení, programy Azure odchozí připojení, které mají být automaticky převedeny na veřejnou front-endovou IP adresu. To je také označován zdroj NAT (SNAT). Překládat pomocí SNAT poskytuje důležité výhody:

    + Umožňuje snadný upgrade a zotavení po havárii služeb, protože front-endu lze dynamicky mapovat k jiné instanci služby.
    + Ho usnadňuje správu seznamu ACL řízení přístupu. Seznamy ACL, vyjádřené jako front-endovou IP adresy se nemění jako škálování služby, dolů nebo získat znovu nasazena.

    Odkazovat na [odchozí připojení](load-balancer-outbound-connections.md) článku podrobnou diskuzi o tuto možnost.

Nástroj pro vyrovnávání zatížení má další specifická pro SKU dalo nad rámec těchto základy.  Zkontrolujte zbývající část tohoto článku podrobnosti.

## <a name="skus"></a> Porovnání SKU služby Vyrovnávání zatížení

Podporuje dvě různé identifikátory SKU Azure nástroj pro vyrovnávání zatížení: Basic a Standard.  Existují rozdíly ve scénáři škálování, funkce a ceny.  Každý scénář možné s základní pro vyrovnávání zatížení lze vytvořit také pomocí nástroj pro vyrovnávání zatížení.  Ve skutečnosti rozhraní API pro obě položky jsou podobné a vyvolaných prostřednictvím specifikace SKU.  Rozhraní API pro podporu SKU pro nástroj pro vyrovnávání zatížení a veřejné IP adresy je k dispozici od verze rozhraní API 2017-08-01.  Obě položky mají stejné obecné rozhraní API a struktura.

V závislosti na tom, který je zvoleno SKU, může být mírně odlišné podrobností konfigurace úplného scénáře. Dokumentace pro vyrovnávání zatížení volá, pokud se článek vztahuje na konkrétní SKU jenom. Naleznete v následující tabulce níže porovnat a pochopit rozdíly.  Zkontrolujte [standardní přehled nástroje pro vyrovnávání zatížení](load-balancer-standard-overview.md) další podrobnosti.

>[!NOTE]
> Nové návrhy měli použít nástroj pro vyrovnávání zatížení. 

Do jednoho identifikátoru SKU, nikdy obě lze připojit pouze samostatné virtuální počítače, skupiny dostupnosti a sady škálování virtuálního počítače. Při použití s veřejné IP adresy, nástroj pro vyrovnávání zatížení a veřejnou IP adresu SKU se musí shodovat. Nástroj pro vyrovnávání zatížení a veřejné IP SKU nejsou měnitelný.

_Je osvědčeným postupem zadejte SKU explicitně, i když ještě není povinné._  V tomto okamžiku jsou synchronizovány na minimum požadované změny. Pokud není zadán SKU, je interpretován jako v úmyslu použít základní SKU ve verzi API 2017-08-01.

>[!IMPORTANT]
>Nástroj pro vyrovnávání zatížení je nový produkt nástroj pro vyrovnávání zatížení a z velké části nadmnožinou základní nástroj pro vyrovnávání zatížení.  Jsou důležité a záměrné rozdíly mezi oba produkty.  Každý scénář začátku do konce možné s základní pro vyrovnávání zatížení lze vytvořit pomocí nástroj pro vyrovnávání zatížení.  Pokud se už používá základní Vyrovnávání zatížení, byste si měli přečíst s standardní pro vyrovnávání zatížení pochopit nejnovější změny v chování mezi Standard a Basic a jejich dopad. V této části najdete pečlivě.

| | [Standardní SKU](load-balancer-standard-overview.md) | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-end | až 1 000 instancí | až 100 instancí|
| Koncové body fond back-end | jakýkoli virtuální počítač v jedné virtuální sítě, včetně blend virtuálních počítačů, skupiny dostupnosti, sadách škálování virtuálních počítačů. | nastavení virtuálních počítačů v jedné dostupnost sady nebo virtuální počítač škálování |
| Zóny dostupnosti | zónově redundantní a oblastmi frontends pro příchozí a odchozí, odchozí toky mapování zůstanou platné i po selhání zóny cross zóny Vyrovnávání zatížení | / |
| Diagnostika | Azure monitorování vícerozměrných metriky, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (úspěšné i neúspěšné překládat pomocí SNAT toky), aktivní data roviny měření | Azure analýzy protokolů pro pouze veřejné Vyrovnávání zatížení, překládat pomocí SNAT vyčerpání výstraha, počet stavu fondu back-end |
| HA porty | Interní zátěže. | / |
| Ve výchozím nastavení zabezpečení | veřejné koncové body IP adresy a nástroj pro vyrovnávání zatížení se vždy výchozí uzavřen, skupinu zabezpečení sítě umožňuje explicitně seznamu povolených IP adres | Výchozí hodnoty otevřete, volitelné skupinu zabezpečení sítě |
| Odchozí připojení | Více frontends za pravidlo výslovný nesouhlas s. Přidružení virtuálního počítače s adresou odchozí _musí_ explicitně vytvořit.  Jedná se o připojení k jiným službám Azure PaaS nebo [koncové body služby virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) se musí použít. Odchozí připojení přes výchozí překládat pomocí SNAT nejsou k dispozici, když slouží pouze k interním pro vyrovnávání zatížení virtuálního počítače. | Jeden front-endu. Když obsluhuje pouze interní nástroj pro vyrovnávání zatížení virtuálního počítače, použije se výchozí překládat pomocí SNAT |
| Více frontends | Příchozí a odchozí | Pouze příchozí |
| Operace | Většinu operací < 30 sekund | 60-90 sekund typické |
| SLA | 99,99 % pro cestu k datům s dva virtuální počítače v pořádku | Implicitní v SLA k Virtuálním počítačům | 
| Ceny | Účtovat na základě počtu pravidel, zpracování dat příchozí nebo odchozí přidružené prostředků  | Bez poplatků |

Zkontrolujte [omezení služby pro vyrovnávání zatížení](https://aka.ms/lblimits).  Pro nástroj pro vyrovnávání zatížení také zkontrolovat další podrobné [přehled](load-balancer-standard-overview.md), [ceny](https://aka.ms/lbpricing), a [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Koncepty

### <a name = "publicloadbalancer"></a>Nástroj pro vyrovnávání zatížení veřejnou

Veřejné Vyrovnávání zatížení mapuje veřejnou IP adresu a číslo portu příchozí přenosy na privátní IP adresu a číslo portu virtuálního počítače a naopak pro odpověď provoz z virtuálního počítače. Pravidla Vyrovnávání zatížení umožňují distribuovat specifické typy přenosů mezi několik virtuálních počítačů nebo služeb. Můžete například šíří zatížení provozu webové žádosti napříč několika webovými servery.

Následující obrázek znázorňuje koncový bod Vyrovnávání zatížení sítě pro webový provoz, která je sdílena mezi tři virtuální počítače pro veřejné a privátní port TCP 80. Tyto tři virtuální počítače jsou v sadě s vyrovnáváním zatížení.

![Příklad nástroje pro vyrovnávání zatížení veřejnou](./media/load-balancer-overview/IC727496.png)

**Obrázek 1: Zátěže webový provoz použití veřejné služby Vyrovnávání zatížení**

Pokud internetoví klienti odesílat žádosti o webovou stránku na veřejnou IP adresu webové aplikace na portu TCP 80, Vyrovnávání zatížení Azure distribuuje požadavky mezi tři virtuální počítače v sadě s vyrovnáváním zatížení. Další informace o algoritmy Vyrovnávání zatížení, najdete v článku [stránku přehled nástroje pro vyrovnávání zatížení](load-balancer-overview.md#load-balancer-features).

Ve výchozím nastavení nástroj pro vyrovnávání zatížení Azure distribuuje síťový provoz mezi více instancí virtuálního počítače. Můžete také nakonfigurovat spřažení relace. Další informace najdete v tématu [režim distribuce nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Interní zátěže.

Interní nástroj pro vyrovnávání zatížení pouze přesměruje přenosy na prostředky, které jsou ve virtuální síti nebo který používat síť VPN pro přístup k infrastruktury Azure. V tomto ohledu interní nástroj pro vyrovnávání zatížení se liší od veřejné nástroje pro vyrovnávání zatížení. Infrastrukturu Azure omezuje přístup na Vyrovnávání zatížení sítě front-end IP adresy virtuální sítě. Front-endovou IP adresy a virtuální sítě jsou zveřejněné nikdy přímo k Internetu koncový bod. Interní-obchodní aplikace spustit v Azure a ke kterým se přistupuje z Azure nebo z místních prostředků.

Interní nástroj pro vyrovnávání zatížení umožňuje následující typy Vyrovnávání zatížení:

* V rámci virtuální sítě: Vyrovnávání zatížení z virtuálních počítačů v virtuální sítě na sadu virtuálních počítačů, které se nacházejí v rámci stejné virtuální síti.
* Pro virtuální síť mezi různými místy: vyrovnávání z místního počítače na sadu virtuálních počítačů, které se nacházejí v rámci stejné virtuální síti zatížení. 
* U vícevrstvých aplikací: pro internetový vícevrstvé aplikace, kde vrstvy back-end nejsou internetového Vyrovnávání zatížení. Back-end vrstvy vyžadují provoz rozložení zátěže z internetové vrstvy (viz obrázek 2).
* Pro-obchodní aplikace: pro-obchodní aplikace, které jsou hostované v Azure bez další zátěže vyrovnávání hardwaru nebo softwaru Vyrovnávání zatížení. Tento scénář zahrnuje na místní servery, které jsou v sadě počítačů, jejichž provoz s vyrovnáváním zatížení.

![Příklad nástroje pro vyrovnávání zatížení interní](./media/load-balancer-overview/IC744147.png)

**Obrázek 2 – vícevrstvé aplikace pomocí obou nástroje pro vyrovnávání zatížení veřejné a interní Vyrovnávání zatížení**

## <a name="pricing"></a>Ceny
Standardní Vyrovnávání zatížení je odečtena produkt na základě počtu Vyrovnávání zatížení nakonfigurovaná pravidla a všechny příchozí a odchozí data zpracování. Informace o cenách standardní Vyrovnávání zatížení, najdete v článku [ceny služby Vyrovnávání zatížení](https://azure.microsoft.com/pricing/details/load-balancer/) stránky.

Základní nástroj pro vyrovnávání zatížení je k dispozici zdarma.

## <a name="sla"></a>SLA

Informace o standardní smlouvě SLA pro vyrovnávání zatížení, najdete v článku [SLA nástroje pro vyrovnávání zatížení](https://aka.ms/lbsla) stránky. 

## <a name="next-steps"></a>Další postup

- Zkontrolujte [podrobněji standardní pro vyrovnávání zatížení](load-balancer-standard-overview.md)
- Další informace o použití [dostupnost zóny a nástroj pro vyrovnávání zatížení](load-balancer-standard-availability-zones.md)
- Další informace o použití [Vyrovnávání zatížení pro odchozí připojení](load-balancer-outbound-connections.md)
- Další informace o [HA porty nástroje pro vyrovnávání zatížení](load-balancer-ha-ports-overview.md)
- Další informace o použití [Vyrovnávání zatížení s více Frontends](load-balancer-multivip-overview.md)
- Další informace o [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md)
- Naučte se vytvářet [základní veřejné zátěže.](load-balancer-get-started-internet-portal.md)

