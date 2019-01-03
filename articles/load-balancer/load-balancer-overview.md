---
title: Co je Azure Load Balancer?
titlesuffix: Azure Load Balancer
description: Přehled funkcí, architektury a implementace Azure Load Balanceru. Zjistěte, jak Load Balancer funguje, a využijte ho v cloudu.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2018
ms.author: kumud
ms.openlocfilehash: 55cb1eee1c0319f9b1037476da208de377e0c47b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971167"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

S Azure Load Balancerem můžete škálovat aplikace a zajistit vysokou dostupnost služeb. Load Balancer podporuje scénáře odchozího i příchozího přenosu, poskytuje nízkou latenci a vysokou propustnost a pro všechny aplikace TCP a UDP umožňuje škálování až na úrovni milionů toků.  

Load Balancer na základě pravidel a sond stavu distribuuje nové příchozí toky, které dorazí do jeho front-endu, do instancí back-endového fondu. 

Veřejný Load Balancer navíc může zajišťovat odchozí připojení pro virtuální počítače ve vaší virtuální síti prostřednictvím překladu jejich privátních IP adres na veřejné IP adresy.

Nástroj Azure Load Balancer je k dispozici ve dvou skladových položkách: Basic a Standard. Ty se liší škálováním, funkcemi a cenou. Jakýkoli scénář, který umožňuje Load Balancer úrovně Basic, je možné vytvořit také s použitím Load Balanceru úrovně Standard, ale postup se může mírně lišit. Při seznamování s Load Balancerem je důležité, abyste porozuměli základům a rozdílům mezi skladovými položkami.

## <a name="why-use-load-balancer"></a>Proč používat Load Balancer? 

Azure Load Balancer můžete použít k následujícím účelům:

* Vyrovnávání zatížení příchozího internetového provozu do virtuálních počítačů. Tato konfigurace se označuje jako [veřejný Load Balancer](#publicloadbalancer).
* Vyrovnávání zatížení provozu mezi virtuálními počítači ve virtuální síti. V hybridním scénáři můžete s front-endem Load Balanceru navázat připojení také z místní sítě. Oba scénáře využívají konfiguraci označovanou jako [interní Load Balancer](#internalloadbalancer).
* Přesměrování portů u přenosů na konkrétní port na konkrétních virtuálních počítačích pomocí příchozích pravidel překladu adres (NAT).
* Zajištění [možností odchozího připojení](load-balancer-outbound-connections.md) pro virtuální počítače ve vaší virtuální síti pomocí veřejného Load Balanceru.


>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud chcete zajistit ukončování protokolu TLS (tzv. přesměrování zpracování SSL) nebo zpracování jednotlivých požadavků HTTP nebo HTTPS na úrovni aplikace, přečtěte si o službě [Application Gateway](../application-gateway/application-gateway-introduction.md). Pokud chcete zajistit globální vyrovnávání zatížení DNS, přečtěte si o službě [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vašim kompletním scénářům by mohla prospět kombinace těchto řešení podle potřeby.

## <a name="what-are-load-balancer-resources"></a>Co jsou prostředky Load Balanceru?

Prostředek Load Balanceru může existovat buď jako veřejný Load Balancer, nebo jako interní Load Balancer. Funkce prostředku Load Balanceru jsou vyjádřené jako definice front-endu, pravidla, sondy stavu a back-endového fondu. Virtuální počítače umístíte do back-endového fondu tak, že back-endový fond zadáte na příslušném virtuálním počítači.

Prostředky Load Balanceru jsou objekty, v rámci kterých můžete vyjádřit, jakým způsobem má Azure naprogramovat svou infrastrukturu s více tenanty, aby se dosáhlo scénáře, který chcete vytvořit. Mezi prostředky Load Balanceru a skutečnou infrastrukturou neexistuje přímý vztah. Vytvořením Load Balanceru nedojde k vytvoření instance a kapacita je vždy dostupná. 

## <a name="fundamental-load-balancer-features"></a>Základní funkce Load Balanceru

Load Balancer poskytuje pro aplikace TCP a UDP následující základní funkce:

* **Vyrovnávání zatížení**

    S Azure Load Balancerem můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu, který přichází do front-endu, do instancí back-endového fondu. Load Balancer při distribuci příchozích toků využívá algoritmus na základě hodnoty hash a odpovídajícím způsobem přepisuje hlavičky toků do instancí back-endového fondu. Server je k dispozici pro příjem nových toků, jakmile sonda stavu určí funkční koncový bod back-endu.
    
    Ve výchozím nastavení používá Load Balancer k mapování toků na dostupné servery hodnotu hash řazené kolekce 5 členů: zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a číslo protokolu IP. Volitelně můžete pro pravidlo zvolit použití hodnoty hash řazené kolekce 2 nebo 3 členů a vytvořit tak spřažení s konkrétní zdrojovou IP adresou. Všechny pakety ze stejného toku paketů se doručí do stejné instance za front-endem s vyrovnáváním zatížení. Když klient zahájí nový tok ze stejné zdrojové IP adresy, změní se zdrojový port. Řazená kolekce 5 členů tak ve výsledku může způsobit přesměrování provozu do jiného koncového bodu back-endu.

    Další informace najdete v tématu [Distribuční režim Load Balanceru](load-balancer-distribution-mode.md). Následující obrázek ukazuje distribuci na základě hodnoty hash:

    ![Distribuce na základě hodnoty hash](./media/load-balancer-overview/load-balancer-distribution.png)

    *Obrázek: Na základě algoritmu hash distribuce*

* **Přesměrování portů**

    S Load Balancerem můžete vytvořit příchozí pravidlo překladu adres (NAT) pro přesměrování portů u přenosů z určitého portu konkrétní front-endové IP adresy na určitý port konkrétní back-endové instance v rámci virtuální sítě. To se také provádí s využitím stejné distribuce na základě hodnoty hash jako vyrovnávání zatížení. Mezi běžné scénáře pro tuto funkci patří relace protokolu RDP (Remote Desktop Protocol) nebo SSH (Secure Shell) k jednotlivým instancím virtuálních počítačů v rámci virtuální sítě Azure. Na různé porty stejné front-endové IP adresy můžete mapovat více interních koncových bodů. IP adresy front-endu můžete použít ke vzdálené správě virtuálních počítačů prostřednictvím Internetu bez nutnosti další jump box.

* **Nezávislost na aplikaci a transparentnost**

    Load Balancer neprovádí interakce přímo s protokolem TCP ani UDP ani s aplikační vrstvou a umožňuje tak podporu všech scénářů aplikací TCP nebo UDP.  Load Balancer neukončuje ani nezahajuje toky, neprovádí interakce s datovou částí toku, neposkytuje funkce brány aplikační vrstvy a přímo mezi klientem a instancí back-endového fondu vždy probíhá ověřování protokolu metodou handshake.  Odpověď na příchozí tok je vždy odpověď z virtuálního počítače.  Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.  Tady je několik příkladů, které blíže ilustrují transparentnost:
    - Odpovědi pro každý koncový bod zajišťuje pouze virtuální počítač.  Mezi klientem a vybraným back-endovým virtuálním počítačem například vždy probíhá ověřování protokolu TCP metodou handshake.  Odpověď na požadavek na front-end je vždy odpověď vygenerovaná back-endovým virtuálním počítačem. Když úspěšně ověříte připojení k front-endu, ověříte tím i koncové připojení alespoň k jednomu back-endovému virtuálnímu počítači.
    - Datové části aplikací jsou pro Load Balancer transparentní a díky tomu je možná podpora jakékoli aplikace UDP nebo TCP. Pro úlohy, které vyžadují zpracování jednotlivých požadavků HTTP nebo manipulaci s datovými částmi aplikační vrstvy (například parsování adres URL HTTP), byste měli použít nástroj pro vyrovnávání zatížení vrstvy 7, jako je služba [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Vzhledem k tomu, že je Load Balancer nezávislý na datové části protokolu TCP a přesměrování zpracování protokolu TLS (SSL) není k dispozici, můžete s využitím Load Balanceru vytvářet scénáře s koncovým šifrováním a získat tak rozsáhlé škálování na více instancí pro aplikace TLS díky ukončování připojení TLS na samotném virtuálním počítači.  Například kapacita udělování klíčů relace protokolu TLS je omezená pouze typem a počtem virtuálních počítačů, které přidáte do back-endového fondu.  Pokud vyžadujete přesměrování zpracování SSL, zpracování aplikační vrstvy nebo chcete delegovat správu certifikátů na Azure, měli byste místo toho použít nástroj pro vyrovnávání zatížení vrstvy 7 Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway).
        

* **Automatická rekonfigurace**

    Když vertikálně navýšíte nebo snížíte kapacitu instancí, Load Balancer se okamžitě automaticky rekonfiguruje. Přidáním virtuálních počítačů do back-endového fondu nebo jejich odebráním se Load Balancer rekonfiguruje bez dalších operací s prostředkem Load Balanceru.

* **Sondy stavu**

    Load Balancer k určení stavu instancí v back-endovém fondu používá sondy stavu, které definujete. Když sonda přestane reagovat, Load Balancer zastaví odesílání nových připojení k instancím, které nejsou v pořádku. Stávající připojení to neovlivní a budou pokračovat, dokud aplikace neukončí tok, nedojde k vypršení časového limitu nečinnosti nebo se virtuální počítač nevypne.
     
    Load Balancer poskytuje [různé typy sondy stavu](load-balancer-custom-probe-overview.md#types) pro koncové body TCP, HTTP a HTTPS.

    Kromě toho při využívání cloudových služeb Classic, další typ je povolený:  [Agent hosta](load-balancer-custom-probe-overview.md#guestagent).  Tento typ by se měl považovat za sondu stavu poslední instance a neměl by se používat, pokud jsou k dispozici jiné možnosti.
    
* **Odchozí připojení (SNAT)**

    Všechny odchozí toky z privátních IP adres v rámci vaší virtuální sítě na veřejné IP adresy na internetu je možné přeložit na front-endovou IP adresu Load Balanceru. Pokud je veřejný front-end svázaný s back-endovým virtuálním počítačem prostřednictvím pravidla vyrovnávání zatížení, Azure naprogramuje automatický překlad odchozích připojení na veřejnou front-endovou IP adresu.

    * Umožněte snadný upgrade služeb a jejich zotavení po havárii díky možnosti dynamického mapování front-endu na jinou instanci služby.
    * Usnadněte také správu seznamu řízení přístupu (ACL). Seznamy ACL vyjádřené s použitím front-endových IP adres se nemění při vertikálním navýšení nebo snížení kapacity služeb ani při jejich opětovném nasazení.  Překlad odchozích připojení na menší počet IP adres, než je počet počítačů, může snížit zátěž spojenou s přidáváním na seznam povolených.

    Další informace najdete v tématu věnovaném [odchozím připojením](load-balancer-outbound-connections.md).

Load Balancer úrovně Standard nabízí nad rámec těchto základních funkcí další funkce specifické pro skladovou položku. Podrobnosti najdete ve zbývající části tohoto článku.

## <a name="skus"></a>Porovnání skladových položek Load Balanceru

Load Balancer podporuje skladové položky Basic i Standard, které se liší v rozsahu scénářů, funkcích a cenách. Jakýkoli scénář, který umožňuje Load Balancer úrovně Basic, je možné vytvořit také s použitím Load Balanceru úrovně Standard. Ve skutečnosti jsou rozhraní API pro obě skladové položky podobná a vyvolávají se prostřednictvím specifikace skladové položky. Rozhraní API zajišťující podporu skladových položek Load Balanceru a veřejné IP adresy je k dispozici počínaje rozhraním API ze dne 1. 8. 2018. Obě skladové položky mají stejné obecné rozhraní API a stejnou strukturu.

V závislosti na zvolené skladové položce se však celková konfigurace scénáře může mírně lišit. V dokumentaci k Load Balanceru najdete upozornění, pokud se článek týká pouze určité skladové položky. Porovnání a vysvětlení rozdílů najdete v následující tabulce. Další informace najdete v tématu [Přehled Load Balanceru úrovně Standard](load-balancer-standard-overview.md).

>[!NOTE]
> V nových návrzích by se měl používat Load Balancer úrovně Standard. 

Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Pokud je použijete s veřejnými IP adresami, musí se shodovat skladové položky Load Balanceru i veřejné IP adresy. Skladové položky Load Balanceru a veřejné IP adresy není možné měnit.

_Skladové položky se doporučuje určovat explicitně, přestože to zatím není povinné._  V současné době se vyžaduje minimum změn. Pokud neurčíte skladovou položku, bude se to interpretovat jako záměr použít verzi rozhraní API skladové položky Basic ze dne 1. 8. 2018.

>[!IMPORTANT]
>Load Balancer úrovně Standard je novým produktem Load Balancer a z velké části se jedná o nadmnožinu Load Balanceru úrovně Standard. Mezi těmito dvěma produkty existují důležité a záměrné rozdíly. Jakýkoli kompletní scénář, který umožňuje Load Balancer úrovně Basic, je možné vytvořit také s použitím Load Balanceru úrovně Standard. Pokud jste už zvyklí na Load Balancer úrovně Basic, měli byste se seznámit s Load Balancerem úrovně Standard, abyste porozuměli nejnovějším změnám chování mezi úrovněmi Standard a Basic a jejich dopadu. Pečlivě si prostudujte tuto část.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Další informace najdete v tématu věnovaném [omezením služby pro Load Balancer](https://aka.ms/lblimits). Podrobnosti o Load Balanceru úrovně Standard najdete v [přehledu](load-balancer-standard-overview.md), na stránce s [cenami](https://aka.ms/lbpricing) a ve [smlouvě SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Koncepty

### <a name = "publicloadbalancer">Veřejný Load Balancer</a>

Veřejný Load Balancer mapuje veřejnou IP adresu a číslo portu příchozích přenosů na privátní IP adresu a číslo portu virtuálního počítače a naopak pro přenosy odpovědí z virtuálního počítače. Při použití pravidel vyrovnávání zatížení můžete distribuovat určité typy provozu mezi několik virtuálních počítačů nebo služeb. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který se sdílí mezi třemi virtuálními počítači pro veřejnost a portem TCP 80. Tyto tři virtuální počítače se nacházejí ve skupině s vyrovnáváním zatížení.

![Příklad veřejného Load Balanceru](./media/load-balancer-overview/IC727496.png)

*Obrázek: Služby Vyrovnávání zatížení webového provozu pomocí veřejného nástroje pro vyrovnávání zatížení*

Když internetoví klienti odešlou požadavky na webovou stránku na veřejnou IP adresu webové aplikace na portu TCP 80, Azure Load Balancer distribuuje požadavky mezi tři virtuální počítače ve skupině s vyrovnáváním zatížení. Další informace o algoritmech Load Balanceru najdete v části [Funkce Load Balanceru](load-balancer-overview.md##fundamental-load-balancer-features) tohoto článku.

Azure Load Balancer ve výchozím nastavení rovnoměrně distribuuje síťový provoz mezi několik instancí virtuálních počítačů. Můžete také nakonfigurovat spřažení relací. Další informace najdete v tématu [Distribuční režim Load Balanceru](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Interní Load Balancer

Interní Load Balancer směruje provoz pouze k prostředkům, které jsou ve virtuální síti nebo které přistupují k infrastruktuře Azure prostřednictvím sítě VPN. V tomto ohledu se interní Load Balancer liší od veřejného Load Balanceru. Infrastruktura Azure omezuje přístup k front-endovým IP adresám virtuální sítě s vyrovnáváním zatížení. Internetový koncový bod nemá nikdy přímý přístup k front-endovým IP adresám ani virtuálním sítím. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků.

Interní Load Balancer umožňuje následující typy vyrovnávání zatížení:

* **V rámci virtuální sítě**: Služby Vyrovnávání zatížení z virtuálních počítačů ve virtuální síti k sadě virtuálních počítačů, které se nacházejí ve stejné virtuální síti.
* **Pro virtuální síť mezi různými místy**: Vyrovnávání zatížení z místního počítače na sadu virtuálních počítačů, které se nacházejí ve stejné virtuální síti. 
* **Při vytváření víceúrovňových aplikací**: Vyrovnávání zatížení pro internetové vícevrstvé aplikace, kde úrovní back-end nejsou přístupem k Internetu. Back-endové vrstvy vyžadují vyrovnávání zatížení provozu z vrstvy s přístupem k internetu (viz další obrázek).
* **Obchodních aplikací**: Vyrovnávání zatížení u-obchodních aplikací, které jsou hostované v Azure bez dalších zatížení vyrovnávání hardwaru nebo softwaru. Tento scénář zahrnuje místní servery, které se nachází ve skupině počítačů s vyrovnáváním zatížení provozu.

![Příklad interního Load Balanceru](./media/load-balancer-overview/IC744147.png)

*Obrázek: Vícevrstvé aplikace s použitím veřejné a vnitřní nástroj pro vyrovnávání zatížení pro vyrovnávání zatížení*

## <a name="pricing"></a>Ceny
Využití Load Balanceru úrovně Standard se účtuje na základě počtu nakonfigurovaných pravidel vyrovnávání zatížení a množství zpracovaných příchozích a odchozích dat. Informace o cenách za Load Balancer úrovně Standard najdete na stránce s [cenami za Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer úrovně Basic se nabízí zdarma.

## <a name="sla"></a>SLA

Informace o smlouvě SLA pro Load Balancer úrovně Standard najdete na stránce [Smlouva SLA pro Load Balancer](https://aka.ms/lbsla). 

## <a name="limitations"></a>Omezení

- Load Balancer je produkt TCP nebo UDP určený k vyrovnávání zatížení a přesměrování portů pro tyto konkrétní protokoly IP.  Pravidla vyrovnávání zatížení a příchozí pravidla překladu adres (NAT) se podporují pro protokoly TCP a UDP, ale nikoli pro ostatní protokoly IP včetně protokolu ICMP. Load Balancer neukončuje datovou část toku protokolu UDP ani TCP, nereaguje na ni, ani s ní neprovádí jiné interakce. Nejedná se o proxy server. Aby se v klientovi zobrazila odpověď z front-endu, musí přes IP síť proběhnout úspěšné ověření připojení k front-endu s použitím stejného protokolu, jaký se používá v pravidle vyrovnávání zatížení nebo příchozím pravidle překladu adres (TCP nebo UDP), _a_ alespoň jeden z virtuálních počítačů musí vygenerovat odpověď.  Pokud z front-endu Load Balanceru neobdržíte odpověď přes IP síť, znamená to, že žádný virtuální počítač nebyl schopný reagovat.  Pokud žádný virtuální počítač není schopný reagovat, není možné provádět interakce s front-endem Load Balanceru.  To platí i pro odchozí připojení, kde se [maskovací SNAT portů](load-balancer-outbound-connections.md#snat) podporuje pouze pro protokoly TCP a UDP – všechny ostatní protokoly IP včetně protokolu ICMP také selžou.  Pokud chcete tento problém zmírnit, přiřaďte veřejnou IP adresu na úrovni instance.
- Na rozdíl od veřejných Load Balancerů, které zajišťují [odchozí připojení](load-balancer-outbound-connections.md) při přechodu z privátních IP adres v rámci virtuální sítě na veřejné IP adresy, interní Load Balancery nepřekládají odchozí připojení k front-endu interního Load Balaneru, protože se nacházejí v adresním prostoru privátních IP adres.  Tím se zabrání potenciálnímu vyčerpání portů SNAT v rámci adresního prostoru jedinečných interních IP adres, kde se překlad nevyžaduje.  Vedlejším efektem je, že pokud se odchozí tok z virtuálního počítače v back-endovém fondu pokusí téct do front-endu interního Load Balanceru ve stejném fondu _a_ mapuje se sám na sebe, nebudou se obě větve toku shodovat a tok selže.  Kdyby se tok nemapoval zpět na stejný virtuální počítač v back-endovém fondu, který vytvořil tok do front-endu, proběhnul by tok úspěšně.   Když se tok mapuje sám na sebe, odchozí tok pro front-end zdánlivě pochází z virtuálního počítače a odpovídající příchozí tok sám pro sebe zdánlivě pochází z virtuálního počítače. Z pohledu hostovaného operačního systému se příchozí a odchozí části stejného toku na virtuálním počítači neshodují. Zásobník protokolu TCP nerozpozná, že jsou tyto poloviny stejného toku součástí stejného toku, protože se zdroj neshoduje s cílem.  Kdyby se tok mapoval na jakýkoli jiný virtuální počítač v back-endovém fondu, tyto poloviny toku by se shodovaly a virtuální počítač by na tok mohl úspěšně reagovat.  Příznakem tohoto scénáře je přerušované vypršení časového limitu připojení, když se tok vrací do stejného back-endu, ze kterého pochází. Existuje několik běžných alternativních řešení, jak tohoto scénáře (směrování toků pocházejících z back-endového fondu do front-endu interního Load Balanceru odpovídajícího tomuto back-endovému fondu) spolehlivě dosáhnout, která zahrnují buď vložení vrstvy proxy serveru za interní Load Balancer, nebo [použití pravidel stylu DSR](load-balancer-multivip-overview.md).  Zákazníci můžou kombinovat interní Load Balancer s libovolným proxy serverem třetí strany nebo v případě scénářů proxy serverů omezených na HTTP a HTTPS nahradit interní službu [Application Gateway](../application-gateway/application-gateway-introduction.md). Přestože ke zmírnění problému můžete použít veřejný Load Balancer, výsledný scénář je náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md#snat) a měli byste se mu vyhnout, pokud nebude pečlivě řízený.

## <a name="next-steps"></a>Další postup

Teď máte přehled o Azure Load Balanceru. Pokud chcete začít používat Load Balancer, vytvořte ho, vytvořte virtuální počítače s nainstalovaným vlastním rozšířením služby IIS a zajistěte vyrovnávání zatížení webové aplikace mezi virtuálními počítači. Pokyny najdete v rychlém startu [Vytvoření Load Balanceru úrovně Basic](quickstart-create-basic-load-balancer-portal.md).
