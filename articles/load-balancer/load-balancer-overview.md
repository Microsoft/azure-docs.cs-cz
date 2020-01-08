---
title: Co je Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Přehled funkcí, architektury a implementace Azure Load Balanceru. Přečtěte si, jak Load Balancer funguje a jak ho využít v cloudu.
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
ms.date: 12/05/2019
ms.author: allensu
ms.openlocfilehash: 4f6a0a2631c3f7458548a2ff435c3e565e744f41
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660204"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

*Vyrovnávání zatížení* označuje efektivní distribuci zatížení nebo příchozích síťových přenosů napříč skupinou back-end prostředků nebo serverů. Azure nabízí [celou řadu možností pro vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) , ze kterých si můžete vybrat podle svých potřeb. Tento dokument popisuje Azure Load Balancer.

Azure Load Balancer funguje ve vrstvě čtyř modelu propojení Open Systems (OSI). Je jedním kontaktním bodem pro klienty. Load Balancer distribuuje nové příchozí toky, které dorazí na front-endové instance fondu Load Balancer na front-end, podle zadaných pravidel vyrovnávání zatížení a sond stavu. Instance fondu back-endu můžou být Azure Virtual Machines nebo instancemi v sadě škálování virtuálního počítače. 

Pomocí Azure Load Balancer můžete škálovat aplikace a vytvářet vysoké dostupné služby. Load Balancer podporuje scénáře příchozího i odchozího přenosu, nabízí nízkou latenci a vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

**[Veřejné Load Balancer](#publicloadbalancer)** můžou poskytovat odchozí připojení k virtuálním počítačům v rámci virtuální sítě tak, že se jejich privátní IP adresy přeloží na veřejné IP adresy. Veřejné nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení internetového provozu na vašich virtuálních počítačích.

**[Interní (nebo privátní) Load Balancer](#internalloadbalancer)** lze použít ve scénářích, kde jsou na front-endu vyžadovány pouze privátní IP adresy. Interní nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení provozu v rámci virtuální sítě. Můžete se taky spojit s Load Balancer front-endu z místní sítě v hybridním scénáři.

## <a name="load-balancer-components"></a>Load Balancer komponenty
* **Konfigurace IP adresy front-endu**: IP adresa Load Balancer. Je kontaktním bodem pro klienty. Může to být buď veřejná, nebo privátní IP adresa, takže se vytvoří buď veřejné, nebo interní nástroje pro vyrovnávání zatížení.
* **Back-end fond**: Skupina Virtual Machines nebo instancí v sadě škálování virtuálního počítače, která bude obsluhovat příchozí požadavek. Aby bylo možné zajistit vysokou škálu nákladů na vysoce velké objemy osvědčených postupů příchozího provozu, obecně doporučujeme přidat do fondu back-end více instancí. Při horizontálním navýšení nebo snížení kapacity instancí se Load Balancer okamžitě znovu nakonfiguruje prostřednictvím automatické změny konfigurace. Přidáním virtuálních počítačů do back-endového fondu nebo jejich odebráním se Load Balancer rekonfiguruje bez dalších operací s prostředkem Load Balanceru.
* **Sondy stavu**: k určení stavu instancí ve fondu back-end se používá sonda stavu. Pro sondy stavu můžete definovat prahovou hodnotu, která není v pořádku. Když sonda přestane reagovat, Load Balancer zastaví odesílání nových připojení k instancím, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. Připojení pokračuje, dokud aplikace neukončí tok, dojde k vypršení časového limitu nečinnosti nebo se virtuální počítač vypne. Load Balancer poskytuje různé typy sond stavu pro koncové body TCP, HTTP a HTTPS. Další informace najdete v tématu [typy sond](load-balancer-custom-probe-overview.md#types).
* **Pravidla vyrovnávání zatížení**: pravidla vyrovnávání zatížení jsou ta, která sdělí Load Balancer, co je potřeba udělat, když. 
* **Příchozí pravidla NAT**: pravidlo příchozího překladu adres (NAT) překládá provoz z konkrétního portu konkrétní front-endu na konkrétní port konkrétní back-endu v rámci virtuální sítě. Předávání portů se provádí stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení. Mezi běžné scénáře této funkce patří relace protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) k jednotlivým instancím virtuálních počítačů v rámci služby Azure Virtual Network. Můžete namapovat několik vnitřních koncových bodů na porty ve stejné front-endové IP adrese. Front-endové IP adresy můžete použít k vzdálené správě virtuálních počítačů bez dalšího pole s odkazem.
* **Odchozí pravidla**: odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro všechny virtuální počítače nebo instance identifikované back-end fondem k překladu na front-end.

## <a name="load-balancer-concepts"></a>Load Balancer koncepty

Load Balancer poskytuje pro aplikace TCP a UDP následující základní funkce:

* **Algoritmus vyrovnávání zatížení**: pomocí Azure Load Balancer můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu, který dorazí do instancí fondu back-endu. Load Balancer používá algoritmus hash pro distribuci příchozích toků a Přepisuje hlavičky toků do instancí fondu back-endu. Server je k dispozici pro příjem nových toků, když sonda stavu indikuje back-end koncový bod, který je v pořádku.
Ve výchozím nastavení Load Balancer používá hodnotu hash s 5 řazenými kolekcemi členů. Hodnota hash zahrnuje zdrojovou IP adresu, zdrojový port, cílovou IP adresu, cílový port a číslo protokolu IP pro mapování toků na dostupné servery. Spřažení můžete vytvořit na zdrojové IP adrese pomocí 2 nebo 3 řazené kolekce členů (hash) pro dané pravidlo. Všechny pakety ze stejného toku paketů se doručí do stejné instance za front-endem s vyrovnáváním zatížení. Když klient inicializuje nový tok ze stejné zdrojové IP adresy, změní se zdrojový port. Výsledkem je, že hodnota hash 5 řazených kolekcí by mohla způsobit, že se provoz přejde do jiného koncového bodu back-endu.
Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md). Následující obrázek ukazuje distribuci na základě hodnoty hash:

  ![Distribuce na základě hodnoty hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Obrázek: Distribuce na základě hodnoty hash*

* **Nezávislost a průhlednost aplikace**: Load Balancer přímo nekomunikuje s protokolem TCP nebo UDP nebo aplikační vrstvou. Může být podporován jakýkoli scénář aplikace TCP nebo UDP. Load Balancer neukončí ani nepocházející toky, komunikují s datovou částí toku nebo poskytne libovolnou funkci brány aplikační vrstvy. K protokolu handshake se vždy dochází přímo mezi klientem a instancí back-end fondu. Odpověď na příchozí tok je vždy odpověď z virtuálního počítače. Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.
  * Odpovědi pro každý koncový bod zajišťuje pouze virtuální počítač. Například protokol handshake TCP vždy probíhá mezi klientem a vybraným back-end virtuálním počítačem. Odpověď na požadavek na front-end je odpověď generovaná back-end virtuálním počítačem. Po úspěšném ověření připojení k front-endu ověříte, že se jedná o koncové připojení k alespoň jednomu back-endovému virtuálnímu počítači.
  * Datové části aplikace jsou transparentní pro Load Balancer. Je možné, že bude podporována jakákoli aplikace UDP nebo TCP.
  * Vzhledem k tomu, že Load Balancer nekomunikuje s datovou částí TCP a zajišťuje snižování zátěže TLS, můžete vytvořit komplexní šifrované scénáře. Použití Load Balancer pro aplikace TLS ve velkém měřítku zamění ukončení připojení TLS na samotném virtuálním počítači. Například kapacita klíče relace TLS je omezená jenom typem a počtem virtuálních počítačů, které přidáte do fondu back-end.

* **Odchozí připojení (SNAT)** : všechny odchozí toky z privátních IP adres uvnitř virtuální sítě až po veřejné IP adresy na internetu je možné přeložit na front-end ip adresu Load Balancer. Když je veřejný front-end vázaný k back-endovému virtuálnímu počítači prostřednictvím pravidla vyrovnávání zatížení, Azure převede odchozí připojení k veřejné front-endové IP adrese. Tato konfigurace má následující výhody:
  * Snadný upgrade a zotavení po havárii služeb, protože front-end lze dynamicky namapovat na jinou instanci služby.
  * Jednodušší Správa seznamů řízení přístupu (ACL). Seznamy řízení přístupu vyjádřené jako IP adresy front-endu se nemění, protože služby se škálují nahoru nebo dolů nebo se znovu nasazují. Překlad odchozích připojení k menšímu počtu IP adres, než jsou počítače, snižuje zátěž pro implementaci seznamů bezpečných příjemců.
Další informace najdete v tématu [odchozí připojení v Azure](load-balancer-outbound-connections.md).
Standard Load Balancer obsahuje další možnosti specifické pro SKU nad rámec těchto základních informací, jak je popsáno níže.

## <a name="skus"></a>Porovnání skladových položek Load Balanceru

Load Balancer podporuje skladové položky Basic i Standard. Tyto SKU se liší ve scénáři škálování, funkce a ceny. Jakýkoli scénář, který je možné použít u základních Load Balancer, se dá vytvořit pomocí Standard Load Balancer. Rozhraní API pro obě SKU jsou podobná a jsou vyvolána prostřednictvím specifikace SKU. Rozhraní API pro podporu SKU pro Load Balancer a veřejnou IP adresu je k dispozici počínaje rozhraním `2017-08-01` API. Oba sklady sdílejí stejné obecné rozhraní API a strukturu.

Konfigurace kompletního scénáře se může mírně lišit v závislosti na SKU. V dokumentaci k Load Balanceru najdete upozornění, pokud se článek týká pouze určité skladové položky. Porovnání a vysvětlení rozdílů najdete v následující tabulce. Další informace najdete v tématu [Přehled služby Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Společnost Microsoft doporučuje Standard Load Balancer.
Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Load Balancer a SKU veřejné IP adresy se musí shodovat, když je používáte s veřejnými IP adresami. Load Balancer a veřejné SKU IP nejsou proměnlivé.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Další informace najdete v tématu [omezení nástroje pro vyrovnávání zatížení](https://aka.ms/lblimits). Podrobnosti o Load Balanceru úrovně Standard najdete v [přehledu](load-balancer-standard-overview.md), na stránce s [cenami](https://aka.ms/lbpricing) a ve [smlouvě SLA](https://aka.ms/lbsla).

## <a name="load-balancer-types"></a>Typy Load Balancer

### <a name = "publicloadbalancer">Veřejný Load Balancer</a>

Veřejný Load Balancer mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Load Balancer mapuje provoz jiným způsobem pro přenos odpovědí z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.

>[!NOTE]
>Pro každou skupinu dostupnosti můžete implementovat jenom jednu veřejnou Load Balancer a jednu interní Load Balancer.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který je sdílen mezi třemi virtuálními počítači pro veřejný a port TCP 80. Tyto tři virtuální počítače se nacházejí ve skupině s vyrovnáváním zatížení.

![Příklad veřejného Load Balanceru](./media/load-balancer-overview/IC727496.png)

*Obrázek: vyrovnávání webového provozu pomocí veřejné Load Balancer*

Internetoví klienti odesílají žádosti webové stránky na veřejnou IP adresu webové aplikace na portu TCP 80. Azure Load Balancer distribuuje požadavky mezi tři virtuální počítače v sadě s vyrovnáváním zatížení. Další informace o algoritmech Load Balancer najdete v tématu [Load Balancer koncepty](load-balancer-overview.md##load-balancer-concepts).

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

## <a name="sla"></a>Smlouva SLA

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

V tématu [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) můžete začít s používáním Load Balancer: Vytvořte si virtuální počítače s nainstalovanou vlastní příponou IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači.
