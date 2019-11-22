---
title: Co je Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Přehled funkcí, architektury a implementace Azure Load Balanceru. Zjistěte, jak Load Balancer funguje, a využijte ho v cloudu.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: fca055558f8c2e0da4041b367ede70974872e640
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280952"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

S Azure Load Balancerem můžete škálovat aplikace a zajistit vysokou dostupnost služeb. Load Balancer podporuje scénáře odchozího i příchozího přenosu, poskytuje nízkou latenci a vysokou propustnost a pro všechny aplikace TCP a UDP umožňuje škálování až na úrovni milionů toků.

Load Balancer distribuuje nové příchozí toky, které dorazí na front-end fond Load Balancer na back-endové instance, podle zadaných pravidel a sond stavu.

Veřejné Load Balancer můžou poskytovat odchozí připojení k virtuálním počítačům v rámci virtuální sítě tak, že se jejich privátní IP adresy přeloží na veřejné IP adresy.

Azure Load Balancer je k dispozici ve dvou cenových úrovních nebo *SKU*: Basic a Standard. Ty se liší škálováním, funkcemi a cenou. Jakýkoli scénář, který je možné použít u základních Load Balancer, se taky dá vytvořit s Standard Load Balancer, i když se tyto přístupy mírně liší. Jak se o Load Balancer seznámíte, Seznamte se se základy a rozdíly specifickými pro SKU.

## <a name="why-use-load-balancer"></a>Proč používat Load Balancer?

Azure Load Balancer můžete použít k následujícím účelům:

* Vyrovnávání zatížení příchozího internetového provozu do virtuálních počítačů. Tato konfigurace se označuje jako [veřejná Load Balancer](#publicloadbalancer).
* Vyrovnávání zatížení napříč virtuálními počítači uvnitř virtuální sítě. V hybridním scénáři můžete s front-endem Load Balanceru navázat připojení také z místní sítě. Oba tyto scénáře používají konfiguraci, která se označuje jako [interní Load Balancer](#internalloadbalancer).
* Přesměrování portů u přenosů na konkrétní port na konkrétních virtuálních počítačích pomocí příchozích pravidel překladu adres (NAT).
* Zajištění [možností odchozího připojení](load-balancer-outbound-connections.md) pro virtuální počítače ve vaší virtuální síti pomocí veřejného Load Balanceru.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud hledáte žádost o ukončení protokolu TLS (Transport Layer Security) ("snižování zátěže SSL") nebo požadavek na protokol HTTP/HTTPS, zpracování aplikační vrstvy, přečtěte si téma [co je Azure Application Gateway](../application-gateway/overview.md). Pokud hledáte globální vyrovnávání zatížení DNS, přečtěte si téma [co je > Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vaše ucelené scénáře můžou využít kombinaci těchto řešení.
>
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="what-are-load-balancer-resources"></a>Co jsou prostředky Load Balanceru?

Load Balancer prostředky jsou objekty určující, jak by měl Azure naprogramovat svoji víceklientské infrastrukturu, aby dosáhli scénáře, který chcete vytvořit. Mezi prostředky Load Balancer a skutečnou infrastrukturou nejsou žádné přímé vztahy. Vytvořením Load Balanceru nedojde k vytvoření instance a kapacita je vždy dostupná.

Prostředek Load Balancer může být buď veřejný Load Balancer, nebo interní Load Balancer. Funkce prostředku Load Balancer jsou definovány front-end, pravidlem, sondou stavu a definicí fondu back-end. Virtuální počítače umístíte do fondu back-end tím, že na virtuálním počítači zadáte fond back-end.

## <a name="fundamental-load-balancer-features"></a>Základní funkce Load Balanceru

Load Balancer poskytuje pro aplikace TCP a UDP následující základní funkce:

* **Vyrovnávání zatížení**

  Pomocí Azure Load Balancer můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu, který dorazí na front-end instance fondu back-end. Load Balancer používá algoritmus hash pro distribuci příchozích toků a Přepisuje hlavičky toků do back-endové instance fondu. Server je k dispozici pro příjem nových toků, když sonda stavu indikuje back-end koncový bod, který je v pořádku.

  Ve výchozím nastavení Load Balancer používá hodnotu hash s 5 řazenými kolekcemi členů. Hodnota hash zahrnuje zdrojovou IP adresu, zdrojový port, cílovou IP adresu, cílový port a číslo protokolu IP pro mapování toků na dostupné servery. Spřažení můžete vytvořit na zdrojové IP adrese pomocí 2 nebo 3 řazené kolekce členů (hash) pro dané pravidlo. Všechny pakety ze stejného toku paketů se doručí do stejné instance za front-endem s vyrovnáváním zatížení. Když klient inicializuje nový tok ze stejné zdrojové IP adresy, změní se zdrojový port. Výsledkem je, že hodnota hash 5 řazených kolekcí by mohla způsobit, že provoz přejde na jiný koncový bod back-endu.

  Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md). Následující obrázek ukazuje distribuci na základě hodnoty hash:

  ![Distribuce na základě hodnoty hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Obrázek: Distribuce na základě hodnoty hash*

* **Přesměrování portů**

  Pomocí Load Balancer můžete vytvořit pravidlo příchozího překladu adres (NAT). Toto pravidlo překladu adres (NAT) překládá provoz z konkrétního portu konkrétní front-endové IP adresy na konkrétní port konkrétní back-endové instance v rámci virtuální sítě. Toto předání je provedeno stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení. Mezi běžné scénáře této funkce patří relace protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) k jednotlivým instancím virtuálních počítačů v rámci služby Azure Virtual Network.
  
  Můžete namapovat několik vnitřních koncových bodů na porty ve stejné front-endové IP adrese. Front-endové IP adresy můžete použít k vzdálené správě virtuálních počítačů bez dalšího pole s odkazem.

* **Nezávislost a průhlednost aplikace**

  Load Balancer přímo nekomunikuje s protokolem TCP nebo UDP nebo aplikační vrstvou. Může být podporován jakýkoli scénář aplikace TCP nebo UDP. Load Balancer neukončí ani nepocházející toky, komunikují s datovou částí toku nebo poskytne libovolnou funkci brány aplikační vrstvy. K protokolu handshake se vždy dochází přímo mezi klientem a instancí back-end fondu. Odpověď na příchozí tok je vždy odpověď z virtuálního počítače. Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.

  * Odpovědi pro každý koncový bod zajišťuje pouze virtuální počítač. Například protokol handshake TCP vždy probíhá mezi klientem a vybraným back-end virtuálním počítačem. Odpověď na požadavek na front-end je odpověď generovaná back-end virtuálním počítačem. Po úspěšném ověření připojení k front-endu ověříte, že se jedná o koncové připojení k alespoň jednomu back-endovému virtuálnímu počítači.
  * Datové části aplikace jsou transparentní pro Load Balancer. Je možné, že bude podporována jakákoli aplikace UDP nebo TCP. Pro úlohy, které vyžadují zpracování požadavků HTTP nebo manipulaci s datovou částí aplikační vrstvy, jako je například analýza adres URL protokolu HTTP, byste měli použít nástroj pro vyrovnávání zatížení vrstvy 7, jako je [Application Gateway](https://azure.microsoft.com/services/application-gateway).
  * Vzhledem k tomu, že Load Balancer nekomunikuje s datovou částí TCP a zajišťuje snižování zátěže TLS, můžete vytvořit komplexní šifrované scénáře. Použití Load Balancer pro aplikace TLS ve velkém měřítku zamění ukončení připojení TLS na samotném virtuálním počítači. Například kapacita klíče relace TLS je omezená jenom typem a počtem virtuálních počítačů, které přidáte do fondu back-end. Pokud vyžadujete snižování zátěže SSL, zpracování aplikační vrstvy nebo chcete delegovat správu certifikátů do Azure, použijte místo toho službu Azure Load Balancer [Application Gateway](https://azure.microsoft.com/services/application-gateway) vrstvy 7.

* **Automatická rekonfigurace**

  Když vertikálně navýšíte nebo snížíte kapacitu instancí, Load Balancer se okamžitě automaticky rekonfiguruje. Přidáním nebo odebráním virtuálních počítačů ze záložního fondu se znovu nakonfigurují Load Balancer bez dalších operací s prostředkem Load Balancer.

* **Sondy stavu**

  K určení stavu instancí v rámci fondu back-endu Load Balancer využívá testy stavu, které definujete. Když sonda přestane reagovat, Load Balancer zastaví odesílání nových připojení k instancím, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. Připojení pokračuje, dokud aplikace neukončí tok, dojde k vypršení časového limitu nečinnosti nebo se virtuální počítač vypne.

  Load Balancer poskytuje různé typy sond stavu pro koncové body TCP, HTTP a HTTPS. Další informace najdete v tématu [typy sond](load-balancer-custom-probe-overview.md#types).

  Při použití klasických cloudových služeb je povolený další typ: [Agent hosta](load-balancer-custom-probe-overview.md#guestagent). Agent hosta by měl být považován za sondu stavu poslední. Microsoft je nedoporučuje, pokud jsou k dispozici další možnosti.

* **Odchozí připojení (SNAT)**

  Všechny odchozí toky z privátních IP adres uvnitř virtuální sítě až po veřejné IP adresy na internetu je možné přeložit na front-end IP adresu Load Balancer. Když je veřejný front-end vázaný k back-endovému virtuálnímu počítači prostřednictvím pravidla vyrovnávání zatížení, Azure převede odchozí připojení k veřejné front-endové IP adrese. Tato konfigurace má následující výhody:

  * Snadný upgrade a zotavení po havárii služeb, protože front-end lze dynamicky namapovat na jinou instanci služby.
  * Jednodušší Správa seznamů řízení přístupu (ACL). Seznamy řízení přístupu vyjádřené jako IP adresy front-endu se nemění, protože služby se škálují nahoru nebo dolů nebo se znovu nasazují. Překlad odchozích připojení k menšímu počtu IP adres, než jsou počítače, snižuje zátěž pro implementaci seznamů bezpečných příjemců.

  Další informace najdete v tématu [odchozí připojení v Azure](load-balancer-outbound-connections.md).

Standard Load Balancer obsahuje další možnosti specifické pro SKU nad rámec těchto základních informací, jak je popsáno níže.

## <a name="skus"></a>Porovnání skladových položek Load Balanceru

Load Balancer podporuje skladové položky Basic i Standard. Tyto SKU se liší ve scénáři škálování, funkce a ceny. Jakýkoli scénář, který je možné použít u základních Load Balancer, se dá vytvořit pomocí Standard Load Balancer. Rozhraní API pro obě SKU jsou podobná a jsou vyvolána prostřednictvím specifikace SKU. Rozhraní API pro podporu SKU pro Load Balancer a veřejnou IP adresu je k dispozici počínaje rozhraním `2017-08-01` API. Oba sklady sdílejí stejné obecné rozhraní API a strukturu.

Konfigurace kompletního scénáře se může mírně lišit v závislosti na SKU. V dokumentaci k Load Balanceru najdete upozornění, pokud se článek týká pouze určité skladové položky. Porovnání a vysvětlení rozdílů najdete v následující tabulce. Další informace najdete v tématu [Přehled služby Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> V nových návrzích by se měl používat Load Balancer úrovně Standard.

Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Load Balancer a SKU veřejné IP adresy se musí shodovat, když je používáte s veřejnými IP adresami. Load Balancer a veřejné SKU IP nejsou proměnlivé.

Osvědčeným postupem je explicitně určit skladové jednotky. V současné době se vyžaduje minimum změn. Pokud se skladová položka nezadá, výchozí hodnota je `2017-08-01` verze rozhraní API základní SKU.

>[!IMPORTANT]
>Standard Load Balancer je nový Load Balancer produkt. Je převážně nadmnožinou základních Load Balancer, ale existují důležité rozdíly mezi těmito dvěma produkty. Jakýkoli kompletní scénář, který umožňuje Load Balancer úrovně Basic, je možné vytvořit také s použitím Load Balanceru úrovně Standard. Pokud jste už použili základní Load Balancer, porovnejte ji s Standard Load Balancer a pochopte nejnovější změny v jejich chování.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Další informace najdete v tématu [omezení nástroje pro vyrovnávání zatížení](https://aka.ms/lblimits). Podrobnosti o Load Balanceru úrovně Standard najdete v [přehledu](load-balancer-standard-overview.md), na stránce s [cenami](https://aka.ms/lbpricing) a ve [smlouvě SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Koncepty

### <a name = "publicloadbalancer">Veřejný Load Balancer</a>

Veřejný Load Balancer mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Load Balancer mapuje provoz jiným způsobem pro přenos odpovědí z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.

>[!NOTE]
>Pro každou skupinu dostupnosti můžete implementovat jenom jednu veřejnou Load Balancer a jednu interní Load Balancer.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který je sdílen mezi třemi virtuálními počítači pro veřejný a port TCP 80. Tyto tři virtuální počítače se nacházejí ve skupině s vyrovnáváním zatížení.

![Příklad veřejného Load Balanceru](./media/load-balancer-overview/IC727496.png)

*Obrázek: vyrovnávání webového provozu pomocí veřejné Load Balancer*

Internetoví klienti odesílají žádosti webové stránky na veřejnou IP adresu webové aplikace na portu TCP 80. Azure Load Balancer distribuuje požadavky mezi tři virtuální počítače v sadě s vyrovnáváním zatížení. Další informace o algoritmech Load Balancer najdete v tématu [základní funkce Load Balancer](load-balancer-overview.md##fundamental-load-balancer-features).

Ve výchozím nastavení Azure Load Balancer distribuuje síťový provoz rovnoměrně mezi několik instancí virtuálních počítačů. Můžete také nakonfigurovat spřažení relací. Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Interní Load Balancer

Interní Load Balancer směruje provoz jenom na prostředky, které jsou uvnitř virtuální sítě nebo které používají VPN pro přístup k infrastruktuře Azure, na rozdíl od veřejné Load Balancer. Infrastruktura Azure omezuje přístup k IP adresám front-end s vyrovnáváním zatížení virtuální sítě. Front-endové IP adresy a virtuální sítě se nikdy přímo nezveřejňují do internetového koncového bodu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků.

Interní Load Balancer umožňuje následující typy vyrovnávání zatížení:

* **V rámci virtuální sítě**: Vyrovnávání zatížení virtuálních počítačů ve virtuální síti až do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro virtuální síť mezi místními sítěmi**: Vyrovnávání zatížení z místních počítačů do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro vícevrstvé aplikace**: Vyrovnávání zatížení pro vícevrstvé aplikace s přístupem k Internetu, kde back-endové vrstvy nejsou přístupné z Internetu. Úrovně back-endu vyžadují vyrovnávání zatížení provozu z internetových vrstev. Podívejte se na další obrázek.
* **Pro obchodní aplikace:** Vyrovnávání zatížení pro obchodní aplikace hostované v Azure bez dalšího hardwaru nebo softwaru nástroje pro vyrovnávání zatížení. Tento scénář zahrnuje místní servery, které jsou v sadě počítačů, jejichž provoz je vyrovnaný vyrovnávání zatížení.

![Příklad interního Load Balanceru](./media/load-balancer-overview/IC744147.png)

*Obrázek: vyrovnávání vícevrstvých aplikací s využitím veřejného i interního Load Balancer*

## <a name="pricing"></a>Ceny

Účtuje se Standard Load Balancer využití.

* Počet nakonfigurovaných pravidel vyrovnávání zatížení a odchozích pravidel. Pravidla příchozího překladu adres (NAT) se nepočítají v celkovém počtu pravidel.
* Množství zpracovaných příchozích a odchozích dat nezávisle na pravidlech.

Informace o cenách Standard Load Balancer najdete v tématu [Load Balancer ceny](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer úrovně Basic se nabízí zdarma.

## <a name="sla"></a>SLA

Informace o smlouvě SLA pro Standard Load Balancer najdete v tématu [SLA pro Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Omezení

* Load Balancer poskytuje vyrovnávání zatížení a přesměrování portů pro konkrétní protokoly TCP nebo UDP. Pravidla vyrovnávání zatížení a pravidla příchozího překladu adres (NAT) podporují protokoly TCP a UDP, ale ne jiné protokoly IP, včetně protokolu ICMP.

  Load Balancer neukončí, odpoví nebo jinak nekomunikuje s datovou částí toku UDP nebo TCP. Nejedná se o proxy server. Úspěšné ověření připojení k front-endu se musí uskutečnit v rámci pásma stejným protokolem použitým v pravidle vyrovnávání zatížení nebo příchozího překladu adres (NAT). Aspoň jeden z vašich virtuálních počítačů musí vygenerovat odpověď pro klienta, aby zobrazil odpověď z front-endu.

  Nepříjem vložené odpovědi z Load Balancer front-endu indikuje, že žádné virtuální počítače nemohly reagovat. Žádná akce nemůže komunikovat s Load Balancer front-end bez toho, aby virtuální počítač mohl reagovat. Tato zásada platí také pro odchozí připojení, kde je maskovací port SNAT podporován pouze pro protokoly TCP a UDP. Všechny ostatní protokoly IP, včetně protokolu ICMP, selžou. Pokud chcete tento problém zmírnit, přiřaďte veřejnou IP adresu na úrovni instance. Další informace najdete v tématu [Princip SNAT a Pat](load-balancer-outbound-connections.md#snat).

* Interní nástroje pro vyrovnávání zatížení nepřevádějí odchozí vytvořená připojení na front-end interního Load Balancer, protože obě jsou v privátním adresním prostoru IP adres. Veřejné nástroje pro vyrovnávání zatížení poskytují [odchozí připojení](load-balancer-outbound-connections.md) z privátních IP adres uvnitř virtuální sítě k veřejným IP adresám. U interních nástrojů pro vyrovnávání zatížení tento přístup zabraňuje možnému vyčerpání portů SNAT v rámci jedinečného interního adresního prostoru IP adres, kde není požadován překlad.

  Vedlejším účinkem je to, že pokud odchozí tok z virtuálního počítače v záložním fondu pokusy o tok do front-endu interního Load Balancer ve svém fondu _a_ namapuje se zpátky na sebe, dvě ramena toku se neshodují. Vzhledem k tomu, že se neshodují, tok se nezdařil. Tok se úspěšně dokončí, pokud tok nemapoval zpátky na stejný virtuální počítač ve fondu back-end, který vytvořil tok do front-endu.

  Když se tok mapuje zpátky na sebe samé, zdá se, že odchozí tok pochází z virtuálního počítače na front-end a odpovídající příchozí tok se zdá, že pochází z virtuálního počítače do sebe samé. Z pohledu hostovaného operačního systému se v rámci tohoto virtuálního počítače neshodují vstupní a výstupní části stejného toku. Zásobník TCP nerozpozná tyto poloviny stejného toku jako součást stejného toku. Zdroj a cíl se neshodují. Když se tok mapuje na jakýkoli jiný virtuální počítač ve fondu back-end, budou se tyto poloviny toku shodovat a virtuální počítač může na tento tok reagovat.

  Příznakem pro tento scénář je přerušované připojení časových limitů, když se tok vrátí na stejný back-end, který tento tok vytvořil. K běžným řešením patří vložení vrstvy proxy za interní Load Balancer a použití pravidel stylu "přímé vrácení serveru (DSR)". Další informace naleznete v tématu [více front-endy pro Azure Load Balancer](load-balancer-multivip-overview.md).

  Interní Load Balancer můžete kombinovat s jakýmkoli proxy třetích stran nebo použít interní [Application Gateway](../application-gateway/application-gateway-introduction.md) pro scénáře proxy pomocí protokolu HTTP/HTTPS. I když můžete použít veřejný Load Balancer k zmírnění tohoto problému, je výsledný scénář náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md#snat). Vyhněte se tomuto druhému přístupu, pokud se pečlivě nespravuje.

* Obecně platí, že při vyrovnávání zatížení nejsou přesměrovací fragmenty IP adresy podporovány. Pro pravidla vyrovnávání zatížení se nepodporují fragmentace paketů UDP a TCP. Pravidla pro vyrovnávání zatížení portů vysoké dostupnosti se dají použít k přeposílání stávajících fragmentů IP adres. Další informace najdete v tématu [Přehled portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Další kroky

V tématu [Vytvoření základní Load Balancer](quickstart-create-basic-load-balancer-portal.md) můžete začít s používáním Load Balancer: Vytvořte si virtuální počítače s nainstalovanou vlastní příponou IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači.
