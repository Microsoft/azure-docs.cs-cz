---
title: Komponenty a omezení
titleSuffix: Azure Load Balancer
description: Přehled Azure Load Balancerch komponent a omezení
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: allensu
ms.openlocfilehash: f570e0cd7361b365a4034e318511cf8227c425a2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722471"
---
# <a name="load-balancer-components-and-limitations"></a>Load Balancer komponenty a omezení
Azure Load Balancer obsahuje několik klíčových součástí pro jeho operaci.  Tyto komponenty můžete nakonfigurovat v předplatném prostřednictvím Azure Portal, Azure CLI nebo Azure PowerShell.  

## <a name="load-balancer-components"></a>Load Balancer komponenty

* **Konfigurace IP adresy front-endu**: IP adresa nástroje pro vyrovnávání zatížení. Je to kontaktní bod pro klienty. Tyto adresy můžou být buď: 

    - **[Veřejná IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privátní IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Back-end fond**: Skupina virtuálních počítačů nebo instancí v sadě škálování virtuálního počítače, které budou obsluhovat příchozí požadavek. Chcete-li efektivně škálovat náklad na velké objemy příchozích přenosů dat, výpočetní pokyny obecně doporučují přidání dalších instancí do back-endu fondu. Při horizontálním navýšení nebo snížení kapacity instancí se Load Balancer okamžitě znovu nakonfiguruje prostřednictvím automatické změny konfigurace. Přidáním nebo odebráním virtuálních počítačů z back-end fondu se překonfigurují Load Balancer bez dalších operací. Obor back-end fondu je libovolný virtuální počítač ve virtuální síti. Back-end fond může mít až 1000 instancí back-endu nebo konfigurace protokolu IP.
Základní nástroje pro vyrovnávání zatížení mají omezený rozsah (Skupina dostupnosti) může škálovat až 300 konfigurací IP adres. Další informace o omezeních najdete v tématu [omezení Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Při zvažování, jak navrhnout back-end fond, můžete navrhnout minimální počet jednotlivých prostředků fondu back-end serveru a dále tak optimalizovat dobu trvání operací správy. Nedochází k žádnému rozdílu nad výkonem nebo škálováním roviny dat.
* **Sondy stavu**: k určení stavu instancí ve fondu back-end se používá **[sonda stavu](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** . Pro sondy stavu můžete definovat prahovou hodnotu, která není v pořádku. Když sonda přestane reagovat, Load Balancer zastaví odesílání nových připojení k instancím, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. 
    
    Připojení pokračuje do aplikace: 
    - Ukončí tok.
    - Vypršel časový limit nečinnosti
    - Virtuální počítač se vypne.

    Load Balancer poskytuje pro koncové body různé typy sond stavu:
    - TCP
    - HTTP
    - Protokol HTTPS (test HTTP s obálkou TLS (Transport Layer Security))
     
     Základní Load Balancer nepodporuje testy HTTPS. Základní Load Balancer navíc ukončí všechna připojení TCP (včetně zavedených připojení). 
    Další informace najdete v tématu [typy sond](load-balancer-custom-probe-overview.md#types).

* **Pravidla vyrovnávání zatížení**: pravidla vyrovnávání zatížení jsou ta, která oznamují Load Balancer, co je potřeba udělat, když. 
* **Příchozí pravidla NAT**: pravidlo příchozího překladu adres (NAT) překládá provoz z konkrétního portu konkrétní front-endu na konkrétní port konkrétní back-endu v rámci virtuální sítě. **[Předávání portů](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** se provádí stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení. Mezi běžné scénáře této funkce patří relace protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) k jednotlivým instancím virtuálních počítačů v rámci služby Azure Virtual Network. Můžete namapovat několik vnitřních koncových bodů na porty ve stejné front-endové IP adrese. Front-endové IP adresy můžete použít k vzdálené správě virtuálních počítačů bez dalšího pole s odkazem.
* **Odchozí pravidla**: **[odchozí pravidlo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfiguruje odchozí překlad adres (NAT) pro všechny virtuální počítače nebo instance identifikované back-end fondem vašeho Standard Load Balancer, který se má přeložit na front-end.
Základní Load Balancer nepodporuje odchozí pravidla.
![Nástroj pro vyrovnávání zatížení Azure](./media/load-balancer-overview/load-balancer-overview.png)

## <a name = "load-balancer-concepts"></a>Load Balancer koncepty

Load Balancer poskytuje pro aplikace TCP a UDP následující základní funkce:

* **Algoritmus vyrovnávání zatížení**: pomocí Azure Load Balancer můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu, který dorazí do instancí fondu back-endu. Load Balancer používá algoritmus hash pro distribuci příchozích toků (nikoli bajtů) a přepíše hlavičky toků do instancí fondu back-endu. Server je k dispozici pro příjem nových toků, když sonda stavu indikuje back-end koncový bod, který je v pořádku.
Ve výchozím nastavení Load Balancer používá hodnotu hash s 5 řazenými kolekcemi členů. 

   Hodnota hash zahrnuje: 

   - **Zdrojová IP adresa**
   - **Zdrojový port**
   - **Cílová IP adresa**
   - **Cílový port**
   - **Číslo protokolu IP pro mapování toků na dostupné servery** 

Spřažení můžete vytvořit na zdrojové IP adrese pomocí 2 nebo 3 řazené kolekce členů (hash) pro dané pravidlo. Všechny pakety ze stejného toku paketů se doručí do stejné instance za front-endem s vyrovnáváním zatížení. Když klient spustí nový tok ze stejné zdrojové IP adresy, změní se zdrojový port. Výsledkem je, že hodnota hash 5 řazených kolekcí by mohla způsobit, že se provoz přejde do jiného koncového bodu back-endu.
Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](./load-balancer-distribution-mode.md). 

Následující obrázek ukazuje distribuci na základě hodnoty hash:

  ![Distribuce na základě hodnoty hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Obrázek: Distribuce na základě hodnoty hash*

* **Nezávislost a průhlednost aplikace**: Load Balancer přímo nekomunikuje s protokolem TCP nebo UDP nebo aplikační vrstvou. Může být podporován jakýkoli scénář aplikace TCP nebo UDP. Load Balancer neukončí ani nepocházející toky, komunikují s datovou částí toku nebo poskytne libovolnou funkci brány aplikační vrstvy. K protokolu handshake se vždy dochází přímo mezi klientem a instancí back-end fondu. Odpověď na příchozí tok je vždy odpověď z virtuálního počítače. Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.
  * Odpovědi pro každý koncový bod zajišťuje pouze virtuální počítač. Například protokol handshake TCP vždy probíhá mezi klientem a vybraným back-end virtuálním počítačem. Odpověď na požadavek na front-end je odpověď generovaná back-end virtuálním počítačem. Po úspěšném ověření připojení k front-endu ověříte, že se jedná o koncové připojení k alespoň jednomu back-endovému virtuálnímu počítači.
  * Datové části aplikace jsou transparentní pro Load Balancer. Je možné, že bude podporována jakákoli aplikace UDP nebo TCP.
  * Vzhledem k tomu, že Load Balancer nekomunikuje s datovou částí TCP a zajišťuje snižování zátěže TLS, můžete vytvořit komplexní šifrované scénáře. Použití Load Balancer využívá velké škálování pro aplikace TLS tím, že ukončuje připojení TLS na samotném virtuálním počítači. Například kapacita klíče relace TLS je omezená jenom typem a počtem virtuálních počítačů, které přidáte do fondu back-end.

* **Odchozí připojení**: všechny odchozí toky z privátních IP adres uvnitř virtuální sítě na veřejné IP adresy na internetu se dají PŘELOŽIT na IP adresu front-endu Load Balancer. Když je veřejný front-end vázaný na back-end virtuální počítač prostřednictvím pravidla vyrovnávání zatížení, Azure převede odchozí připojení na veřejnou IP adresu front-endu. Tato konfigurace má následující výhody:
  * Snadný upgrade a zotavení po havárii služeb, protože front-end lze dynamicky namapovat na jinou instanci služby.
  * Jednodušší Správa seznamů řízení přístupu (ACL). Seznamy řízení přístupu vyjádřené jako IP adresy front-endu se nemění, protože služby se škálují nahoru nebo dolů nebo se znovu nasazují. Překlad odchozích připojení k menšímu počtu IP adres, než jsou počítače, snižuje zátěž pro implementaci seznamů bezpečných příjemců.

  Standard Load Balancer využívá [robustní, škálovatelný a předvídatelný algoritmus SNAT](load-balancer-outbound-connections.md#snat). Toto je klíčová principy, která se zapamatuje při práci s Standard Load Balancer:

    - pravidla vyrovnávání zatížení odvozují, jak je SNAT naprogramováno. Pravidla vyrovnávání zatížení jsou specifická pro protokol. SNAT je specifická pro protokol a konfigurace by se měla projevit místo vytvoření vedlejšího efektu.

    - **Několik front-endu** Pokud je k dispozici více front-endu, použijí se všechny front-endové a každý front-end vynásobí počet dostupných portů SNAT. Pokud potřebujete další porty SNAT, protože očekáváte nebo už máte vysoký požadavek na odchozí připojení, můžete taky přidat přírůstkový inventář portů SNAT tím, že nakonfigurujete další front-endové, pravidla a back-end fondy na stejný virtuální počítač. prostředky.

    - **Řízení, který front-end používá pro odchozí** připojení Můžete zvolit a řídit, jestli nechcete, aby se pro odchozí připojení používal konkrétní front-end. Pokud chcete omezit odchozí připojení jenom na základě konkrétní IP adresy front-endu, můžete pro pravidlo, které vyjadřuje odchozí mapování, volitelně zakázat odchozí SNAT.

    - **Řízení** odchozích scénářů odchozího připojení je explicitní a odchozí připojení neexistuje, dokud není zadané. Standard Load Balancer existuje v kontextu virtuální sítě.  Virtuální síť je izolovaná privátní síť.  Pokud neexistuje přidružení s veřejnou IP adresou, není veřejné připojení povoleno.  Můžete získat přístup k [koncovým bodům služby virtuální](../virtual-network/virtual-network-service-endpoints-overview.md) sítě, protože jsou uvnitř a místní k vaší virtuální síti.  Pokud chcete vytvořit odchozí připojení k cíli mimo vaši virtuální síť, máte dvě možnosti:
        - Přiřaďte veřejnou IP adresu standardní SKU jako veřejnou IP adresu na úrovni instance k prostředku virtuálního počítače nebo
        - Umístěte prostředek virtuálního počítače do back-endu fondu veřejných Standard Load Balancer.

        Oba umožní odchozí připojení z virtuální sítě k mimo virtuální síť. 

        Pokud máte k back-endu, ve kterém je umístěný prostředek virtuálního počítače, přidružená _jenom_ interní standard Load Balancer, váš virtuální počítač může dosáhnout jenom virtuálních síťových prostředků a [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md)virtuální sítě.  Pro vytvoření odchozího připojení můžete postupovat podle kroků popsaných v předchozím odstavci.

        Odchozí připojení prostředku virtuálního počítače, které není přidružené ke standardním SKU, zůstane stejné jako předtím.

        Přečtěte si [podrobné informace o odchozích připojeních](load-balancer-outbound-connections.md).

* **Zóny dostupnosti**: Standard Load Balancer podporuje další možnosti v oblastech, kde jsou k dispozici zóny dostupnosti. Tyto funkce jsou přírůstkové na všechny Standard Load Balancer.  Zóny dostupnosti konfigurace jsou k dispozici pro oba typy, veřejné i interní Standard Load Balancer.
 Zóna redundantní front-end zaznamená selhání zóny a obsluhuje vyhrazenou infrastrukturu ve všech zónách současně. 
Navíc můžete zaručit front-end pro konkrétní zónu. Oblast front-endu se podílí s příslušnou zónou a je obsluhována pouze pomocí vyhrazené infrastruktury v jedné zóně.
Pro back-end fond je k dispozici vyrovnávání zatížení mezi zónami a každý prostředek virtuálního počítače ve virtuální síti může být součástí fondu back-endu.
Základní Load Balancer nepodporují zóny.
Další informace najdete [v podrobné diskuzi o schopnostech souvisejících s zóny dostupnosti](load-balancer-standard-availability-zones.md) a [zóny dostupnosti přehled](../availability-zones/az-overview.md) .

* **Porty ha**: můžete nakonfigurovat pravidla vyrovnávání zatížení, která zajistí škálování aplikace a vysokou spolehlivost. Když použijete pravidlo vyrovnávání zatížení s porty HA, Standard Load Balancer bude poskytovat vyrovnávání zatížení na každém dočasném portu IP adresy interního Standard Load Balancer front-endu.  Tato funkce je užitečná pro jiné scénáře, kdy je nepraktické nebo nežádoucí určení jednotlivých portů. Pravidlo vyrovnávání zatížení portů HA vám umožní vytvářet aktivní – pasivní nebo aktivní – aktivní n + 1 scénáře pro síťová virtuální zařízení a všechny aplikace, které vyžadují velké rozsahy vstupních portů.  Sondu stavu lze použít k určení, které back-endy by měly dostávat nové toky.  Skupinu zabezpečení sítě můžete použít k emulaci scénáře rozsahu portů. Základní Load Balancer nepodporují porty HA.
Přečtěte si [podrobné informace o portech ha](load-balancer-ha-ports-overview.md) .
>[!IMPORTANT]
> Pokud plánujete použít síťové virtuální zařízení, obraťte se na dodavatele a Projděte si informace o tom, jestli se jejich produkt testuje pomocí portů HA, a postupujte podle svých specifických pokynů k implementaci. 

* **Více front-endu**: Load Balancer podporuje více pravidel s více front-endu.  Standard Load Balancer tuto situaci rozšíří do odchozích scénářů.  Odchozí scénáře jsou v podstatě invertované příchozí pravidlo vyrovnávání zatížení.  Příchozí pravidlo vyrovnávání zatížení také vytvoří přidružení pro odchozí připojení. Standard Load Balancer používá všechny front-endové služby přidružené k prostředku virtuálního počítače prostřednictvím pravidla vyrovnávání zatížení.  Kromě toho parametr pravidla vyrovnávání zatížení, který umožňuje potlačit pravidlo vyrovnávání zatížení pro účely odchozího připojení, které umožňuje výběr určitých front-endu, včetně žádné.

Pro srovnání základní Load Balancer vybere jeden front-end s náhodným umístěním a neexistuje možnost řízení toho, která z nich byla vybrána.
## <a name="load-balancer-types"></a>Typy Load Balancer

### <a name = "publicloadbalancer">Veřejný Load Balancer</a>

Veřejný Load Balancer mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Load Balancer mapuje provoz jiným způsobem pro přenos odpovědí z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.

>[!NOTE]
>Pro každou skupinu dostupnosti můžete implementovat jenom jednu veřejnou Load Balancer a jednu interní Load Balancer.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který je sdílen mezi třemi virtuálními počítači pro veřejný a port TCP 80. Tyto tři virtuální počítače se nacházejí ve skupině s vyrovnáváním zatížení.

![Příklad veřejného Load Balanceru](./media/load-balancer-overview/IC727496.png)

*Obrázek: vyrovnávání webového provozu pomocí veřejné Load Balancer*

Internetoví klienti odesílají žádosti webové stránky na veřejnou IP adresu webové aplikace na portu TCP 80. Azure Load Balancer distribuuje požadavky mezi tři virtuální počítače v sadě s vyrovnáváním zatížení. Další informace o algoritmech Load Balancer najdete v tématu [Load Balancer koncepty](concepts-limitations.md#load-balancer-concepts).

Ve výchozím nastavení Azure Load Balancer distribuuje síťový provoz rovnoměrně mezi několik instancí virtuálních počítačů. Můžete také nakonfigurovat spřažení relací. Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Interní Load Balancer

Interní nástroj pro vyrovnávání zatížení směruje provoz jenom na prostředky, které jsou uvnitř virtuální sítě nebo které používají VPN pro přístup k infrastruktuře Azure, na rozdíl od veřejného nástroje pro vyrovnávání zatížení. Infrastruktura Azure omezuje přístup k IP adresám front-end s vyrovnáváním zatížení virtuální sítě. Front-endové IP adresy a virtuální sítě se nikdy přímo nezveřejňují do internetového koncového bodu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků.

Interní Load Balancer umožňuje následující typy vyrovnávání zatížení:

* **V rámci virtuální sítě**: Vyrovnávání zatížení virtuálních počítačů ve virtuální síti až do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro virtuální síť mezi místními sítěmi**: Vyrovnávání zatížení z místních počítačů do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro vícevrstvé aplikace**: Vyrovnávání zatížení pro vícevrstvé aplikace s přístupem k Internetu, kde back-endové vrstvy nejsou přístupné z Internetu. Úrovně back-endu vyžadují vyrovnávání zatížení provozu z internetových vrstev. Podívejte se na další obrázek.
* **Pro obchodní aplikace:** Vyrovnávání zatížení pro obchodní aplikace hostované v Azure bez dalšího hardwaru nebo softwaru nástroje pro vyrovnávání zatížení. Tento scénář zahrnuje místní servery, které jsou v sadě počítačů, jejichž provoz je vyrovnaný vyrovnávání zatížení.

![Příklad interního Load Balanceru](./media/load-balancer-overview/IC744147.png)

*Obrázek: vyrovnávání vícevrstvých aplikací s využitím veřejného i interního Load Balancer*

## <a name="skus"></a>Porovnání skladových položek Load Balanceru

Nástroj pro vyrovnávání zatížení podporuje skladové položky Basic i Standard. Tyto SKU se liší ve scénáři škálování, funkce a ceny. Jakýkoli scénář, který je možné použít u základních Load Balancer, se dá vytvořit pomocí Standard Load Balancer. Rozhraní API pro obě SKU jsou podobná a jsou vyvolána prostřednictvím specifikace SKU. Rozhraní API pro podporu SKU pro vyrovnávání zatížení a veřejnou IP adresu je dostupné Počínaje rozhraním `2017-08-01` API. Oba sklady sdílejí stejné obecné rozhraní API a strukturu.

Konfigurace kompletního scénáře se může mírně lišit v závislosti na SKU. Dokumentace k nástroji pro vyrovnávání zatížení volá, když se článek vztahuje jenom na konkrétní SKLADOVOU položku. Porovnání a vysvětlení rozdílů najdete v následující tabulce. Další informace najdete v tématu [Přehled služby Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Společnost Microsoft doporučuje Standard Load Balancer.
Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Load Balancer a SKU veřejné IP adresy se musí shodovat, když je používáte s veřejnými IP adresami. Load Balancer a veřejné SKU IP nejsou proměnlivé.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Další informace najdete v tématu [omezení nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Podrobnosti o Load Balanceru úrovně Standard najdete v [přehledu](load-balancer-standard-overview.md), na stránce s [cenami](https://aka.ms/lbpricing) a ve [smlouvě SLA](https://aka.ms/lbsla).

## <a name = "limitations"></a>Určitá

- SKU nejsou proměnlivé. Nemůžete změnit SKU existujícího prostředku.
- Samostatný prostředek virtuálního počítače, prostředek sady dostupnosti nebo prostředek sady škálování virtuálního počítače můžou odkazovat na jednu SKU, nikdy ne obojí.
- Pravidlo Load Balancer nemůže zasahovat do dvou virtuálních sítí.  Front-endové a jejich související instance back-end musí být umístěné ve stejné virtuální síti.  
- [Operace přesunu předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md) se u standardních a veřejných IP prostředků nepodporují.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné jenom z instancí za interním Standard Load Balancer. Nesmíte spoléhat na to, že se jedná o samotnou službu nebo že se může změnit základní platforma bez předchozího upozornění. Vždy musíte předpokládat, že pokud budete chtít používat jenom interní Standard Load Balancer, musíte v případě potřeby explicitně vytvořit [odchozí připojení](load-balancer-outbound-connections.md) .

- Load Balancer poskytuje vyrovnávání zatížení a přesměrování portů pro konkrétní protokoly TCP nebo UDP. Pravidla vyrovnávání zatížení a pravidla příchozího překladu adres (NAT) podporují protokoly TCP a UDP, ale ne jiné protokoly IP, včetně protokolu ICMP.

  Load Balancer neukončí, odpoví nebo jinak nekomunikuje s datovou částí toku UDP nebo TCP. Nejedná se o proxy server. Úspěšné ověření připojení k front-endu se musí uskutečnit v rámci pásma stejným protokolem použitým v pravidle vyrovnávání zatížení nebo příchozího překladu adres (NAT). Aspoň jeden z vašich virtuálních počítačů musí vygenerovat odpověď pro klienta, aby zobrazil odpověď z front-endu.

  Nepříjem vložené odpovědi z Load Balancer front-endu indikuje, že žádné virtuální počítače nemohly reagovat. Žádná akce nemůže komunikovat s Load Balancer front-end bez toho, aby virtuální počítač mohl reagovat. Tato zásada platí také pro odchozí připojení, kde je maskovací port SNAT podporován pouze pro protokoly TCP a UDP. Všechny ostatní protokoly IP, včetně protokolu ICMP, selžou. Pokud chcete tento problém zmírnit, přiřaďte veřejnou IP adresu na úrovni instance. Další informace najdete v tématu [Princip SNAT a Pat](load-balancer-outbound-connections.md#snat).

- Interní nástroje pro vyrovnávání zatížení nepřevádějí odchozí vytvořená připojení na front-end interního Load Balancer, protože obě jsou v privátním adresním prostoru IP adres. Veřejné nástroje pro vyrovnávání zatížení poskytují [odchozí připojení](load-balancer-outbound-connections.md) z privátních IP adres uvnitř virtuální sítě k veřejným IP adresám. U interních nástrojů pro vyrovnávání zatížení tento přístup zabraňuje možnému vyčerpání portů SNAT v rámci jedinečného interního adresního prostoru IP adres, kde není požadován překlad.

  Vedlejším účinkem je to, že pokud odchozí tok z virtuálního počítače v záložním fondu pokusy o tok do front-endu interního Load Balancer ve svém fondu _a_ namapuje se zpátky na sebe, dvě ramena toku se neshodují. Vzhledem k tomu, že se neshodují, tok se nezdařil. Tok se úspěšně dokončí, pokud tok nemapoval zpátky na stejný virtuální počítač ve fondu back-end, který vytvořil tok do front-endu.

  Když se tok mapuje zpátky na sebe samé, zdá se, že odchozí tok pochází z virtuálního počítače na front-end a odpovídající příchozí tok se zdá, že pochází z virtuálního počítače do sebe samé. Z pohledu hostovaného operačního systému se v rámci tohoto virtuálního počítače neshodují vstupní a výstupní části stejného toku. Zásobník TCP nerozpozná tyto poloviny stejného toku jako součást stejného toku. Zdroj a cíl se neshodují. Když se tok mapuje na jakýkoli jiný virtuální počítač ve fondu back-end, budou se tyto poloviny toku shodovat a virtuální počítač může na tento tok reagovat.

  Příznakem pro tento scénář je přerušovaná prodleva připojení, když se tok vrátí ke stejnému back-endu, který daný tok vytvořil. K běžným řešením patří vložení vrstvy proxy za interní Load Balancer a použití pravidel stylu "přímé vrácení serveru (DSR)". Další informace naleznete v tématu [více front-endy pro Azure Load Balancer](load-balancer-multivip-overview.md).

  Interní Load Balancer můžete kombinovat s jakýmkoli proxy třetích stran nebo použít interní [Application Gateway](../application-gateway/application-gateway-introduction.md) pro scénáře proxy pomocí protokolu HTTP/HTTPS. I když můžete použít veřejný Load Balancer k zmírnění tohoto problému, je výsledný scénář náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md#snat). Vyhněte se tomuto druhému přístupu, pokud se pečlivě nespravuje.

- Obecně platí, že při vyrovnávání zatížení nejsou přesměrovací fragmenty IP adresy podporovány. Pro pravidla vyrovnávání zatížení se nepodporují fragmentace paketů UDP a TCP. Pravidla pro vyrovnávání zatížení portů vysoké dostupnosti se dají použít k přeposílání stávajících fragmentů IP adres. Další informace najdete v tématu [Přehled portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Další kroky

- V tématu [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) můžete začít s používáním Load Balancer: Vytvořte si virtuální počítače s nainstalovanou vlastní příponou IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači.
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Přečtěte si o používání [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Další informace o použití [nástroje pro vyrovnávání zatížení pro odchozí připojení](load-balancer-outbound-connections.md).
- Přečtěte si o [odchozích pravidlech](load-balancer-outbound-rules-overview.md).
- Přečtěte si o [resetování protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- Přečtěte si o [Standard Load Balancer s pravidly pro vyrovnávání zatížení portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Přečtěte si o použití [Load Balancer s více front-endu](load-balancer-multivip-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
