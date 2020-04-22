---
title: Komponenty a omezení
titleSuffix: Azure Load Balancer
description: Přehled součástí a omezení nástroje Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2020
ms.author: allensu
ms.openlocfilehash: 7a7210915920ed9ab2a5ddc47a27c2587895a57a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768264"
---
# <a name="load-balancer-components-and-limitations"></a>Komponenty a omezení pro vyrovnávání zatížení
Azure Load Balancer obsahuje několik klíčových součástí pro jeho provoz.  Tyto komponenty můžete nakonfigurovat ve vašem předplatném prostřednictvím portálu Azure, Azure CLI nebo Azure PowerShell.  

## <a name="load-balancer-components"></a>Komponenty pro vyrovnávání zatížení

* **Front-endOVÉ konfigurace IP**: ADRESA IP nástroje pro vyrovnávání zatížení. Je to kontaktní místo pro klienty. Tyto adresy mohou být buď: 

    - **[Veřejná IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privátní IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Back-endový fond**: Skupina virtuálních počítačů nebo instancí ve škálovací sadě virtuálních počítačů, které budou sloužit příchozímu požadavku. Chcete-li efektivně škálovat tak, aby splňovaly velké objemy příchozího provozu, výpočetní pokyny obecně doporučují přidat další instance do back-endového fondu. Nástroj pro vyrovnávání zatížení se okamžitě překonfiguruje prostřednictvím automatické změny konfigurace při škálování instancí nahoru nebo dolů. Přidání nebo odebrání virtuálních počítače z back-endového fondu překonfiguruje nástroj pro vyrovnávání zatížení bez dalších operací. Rozsah back-endového fondu je libovolný virtuální počítač ve virtuální síti. Fond back-endu může mít až 1000 back-endových instancí nebo konfigurací IP adres.
Základní nástroje pro vyrovnávání zatížení mají omezený rozsah (dostupnost) mohou škálovat až na 300 konfigurací IP. Další informace o limitech naleznete v tématu [Limity pro vyrovnávání zatížení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Při zvažování, jak navrhnout back-endového fondu, můžete navrhnout pro nejmenší počet jednotlivých prostředků back-endového fondu pro další optimalizaci doby trvání operací správy. Neexistuje žádný rozdíl ve výkonu roviny dat nebo měřítku.
* **Sondy stavu**: **[Sonda stavu](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** se používá k určení stavu instancí v back-endového fondu. Můžete definovat prahovou hodnotu není v pořádku pro vaše sondy stavu. Když sonda přestane reagovat, Load Balancer zastaví odesílání nových připojení k instancím, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. 
    
    Připojení pokračuje až do aplikace: 
    - Ukončí tok
    - Dojde k časovému limitu nečinnosti.
    - Virtuální mě se vypne.

    Vykladač zatížení poskytuje různé typy sond y stavu pro koncové body:
    - TCP
    - HTTP
    - HTTPS (HTTP sonda s obálkou zabezpečení transportní vrstvy (TLS)
     
     Základní vyrovnávání zatížení nepodporuje sondy HTTPS. Základní vykladač zatížení navíc ukončí všechna připojení TCP (včetně navazovaného připojení). 
    Další informace naleznete v tématu [Probe types](load-balancer-custom-probe-overview.md#types).

* **Pravidla vyrovnávání zatížení**: Pravidla vyrovnávání zatížení jsou pravidla, která říkají vykladaču zatížení, co je třeba udělat, když. 
* **Příchozí pravidla PŘEKLADU**: Pravidlo příchozího překladu adres předává přenosy z konkrétního portu konkrétní ip adresy front-endu na konkrétní port konkrétní instance back-endu uvnitř virtuální sítě. **[Předávání portů](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** se provádí stejným rozdělením založeným na hash jako vyrovnávání zatížení. Běžné scénáře pro tuto funkci jsou vzdáleného protokolu plochy (RDP) nebo zabezpečené prostředí (SSH) relace pro jednotlivé instance virtuálních počítačů uvnitř virtuální sítě Azure. Můžete mapovat více interních koncových bodů na porty na stejné front-endové IP adrese. Front-endOVÉ IP adresy můžete použít ke vzdálené správě virtuálních počítačů bez dalšího skokového rámečku.
* **Odchozí pravidla:** **[Odchozí pravidlo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfiguruje překlad odchozích síťových adres (NAT) pro všechny virtuální počítače nebo instance identifikované back-endovým fondem standardního nástroje pro vyrovnávání zatížení, které mají být přeloženy do front-endu.

  Základní vyrovnávání zatížení nepodporuje odchozí pravidla.

  ![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)
* **Transportní protokoly**: Vykladač zatížení nepodporuje protokol ICMP; IcMP ping na veřejné čelí vyrovnávání zatížení bude časový mačkový styk. Chcete-li příkaz ping použít příkaz pro vyrovnávání zatížení směřující k veřejnému přístupu, použijte příkaz TCP Ping

## <a name="load-balancer-concepts"></a><a name = "load-balancer-concepts"></a>Koncepce pro vyrovnávání zatížení

Load Balancer poskytuje pro aplikace TCP a UDP následující základní funkce:

* **Algoritmus vyrovnávání zatížení**: Pomocí Azure Load Balancer můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu, který dorazí na front-end do back-endu restancí fondu. Vykladač zatížení používá algoritmus hash pro distribuci příchozích toků (nikoli bajtů) a přepisuje záhlaví toků do instancí back-endového fondu. Server je k dispozici pro příjem nových toků, když sonda stavu označuje koncový bod v pořádku back-end.
Ve výchozím nastavení používá balancer 5 seřazené z řazené kolekce, které se vztahuje na 5 nit. 

   Hašiš zahrnuje: 

   - **Zdrojová IP adresa**
   - **Zdrojový port**
   - **Cílová IP adresa**
   - **Cílový port**
   - **Číslo protokolu IP pro mapování toků na dostupné servery** 

Můžete vytvořit spřažení zdrojové IP adresy pomocí 2 nebo 3 n-tice hash pro dané pravidlo. Všechny pakety ze stejného toku paketů se doručí do stejné instance za front-endem s vyrovnáváním zatížení. Když klient spustí nový tok ze stejné zdrojové IP adresy, zdrojový port se změní. V důsledku toho 5 řazené sady hash může způsobit, že provoz přejít na jiný koncový bod back-endu.
Další informace naleznete [v tématu Konfigurace distribučního režimu pro Azure Load Balancer](./load-balancer-distribution-mode.md). 

Následující obrázek ukazuje distribuci na základě hodnoty hash:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Distribuce na základě hodnoty hash">
</p>

  *Obrázek: Distribuce na základě hodnoty hash*

* **Nezávislost a průhlednost aplikací**: Nástroje pro vyrovnávání zatížení přímo neinteragují s protokolem TCP nebo UDP ani s aplikační vrstvou. Může být podporován jakýkoli scénář aplikace TCP nebo UDP. Vykladač zatížení neukončuje nebo nepochází z toků, nekomunikuje s datovou částí toku ani neposkytuje žádnou funkci brány aplikační vrstvy. Protokol handshakes vždy dojít přímo mezi klientem a back-end fondu instance. Odpověď na příchozí tok je vždy odpověď z virtuálního počítače. Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.
  * Odpovědi pro každý koncový bod zajišťuje pouze virtuální počítač. Například tcp handshake vždy dochází mezi klientem a vybraného virtuálního virtuálního serveru back-end. Odpověď na požadavek na front-end je odpověď generovaná back-endový virtuální ms. Při úspěšném ověření připojení k front-endu ověřujete připojení od konce do konce alespoň k jednomu back-endvirtuálnímu počítači.
  * Datové části aplikace jsou transparentní pro vyrovnávání zatížení. Všechny aplikace UDP nebo TCP mohou být podporovány.
  * Vzhledem k tomu, že systém vyrovnávání zatížení nespolupracuje s datovou částí Protokolu TCP a poskytuje nenačtení TLS, můžete vytvářet šifrované scénáře od konce do konce. Pomocí load balancer získá velké škálování pro aplikace TLS ukončením připojení TLS na samotném virtuálním počítači. Kapacita klíče relace TLS je například omezena pouze typem a počtem virtuálních klíčů, které přidáte do back-endového fondu.

* **Odchozí připojení**: Všechny odchozí toky z privátních IP adres uvnitř virtuální sítě do veřejných IP adres na internetu lze přeložit na front-endOVOU IP adresu nástroje Pro nástroj pro vyrovnávání zatížení. Když je veřejný front-end vázaný na back-endový virtuální počítač prostřednictvím pravidla vyrovnávání zatížení, Azure převede odchozí připojení na veřejnou ip adresu front-endu. Tato konfigurace má následující výhody:
  * Snadný upgrade a zotavení po havárii služeb, protože front-end lze dynamicky mapovat na jinou instanci služby.
  * Snadnější správa seznamu řízení přístupu (ACL). AcLs vyjádřené jako front-end IP adresy se nemění jako služby vertikálně nahoru nebo dolů nebo získat znovu nasadit. Překlad odchozích připojení na menší počet adres IP než počítačů snižuje zátěž spojenou s implementací seznamů bezpečných příjemců.

  Standardní nástroje pro vyrovnávání zatížení využívá [robustní, škálovatelný a předvídatelný algoritmus SNAT](load-balancer-outbound-connections.md#snat). Toto jsou klíčové principy, které si pamatujete při práci se standardním vyvažovačem zatížení:

    - pravidla vyrovnávání zatížení odvodí způsob naprogramování snatu. Pravidla vyrovnávání zatížení jsou specifická pro protokol. SNAT je specifický pro protokol a konfigurace by měla odrážet spíše to, než vytvořit vedlejší účinek.

    - **Více frontendů** Pokud je k dispozici více front-endů, použijí se všechny front-endy a každý front-end vynásobí počet dostupných portů SNAT. Pokud chcete více portů SNAT, protože očekáváte nebo již dochází k vysoké poptávce po odchozích připojeních, můžete také přidat přírůstkové inventářportů SNAT konfigurací dalších frontendů, pravidel a back-endových fondů pro stejné prostředky virtuálního počítače.

    - **Řízení front-endu, který se používá pro odchozí** Můžete zvolit a řídit, pokud si nepřejete, aby byl pro odchozí připojení použit určitý front-end. Pokud chcete omezit odchozí připojení pouze pocházejí z určité adresy IP front-endu, můžete volitelně zakázat odchozí SNAT na pravidlo, které vyjadřuje odchozí mapování.

    - **Řízení odchozí připojení** odchozí scénáře jsou explicitní a odchozí připojení neexistuje, dokud nebyl zadán. Standardní nástroj pro vyrovnávání zatížení existuje v kontextu virtuální sítě.  Virtuální síť je izolovaná privátní síť.  Pokud neexistuje přidružení s veřejnou IP adresou, není veřejné připojení povoleno.  Koncové [body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) můžete získat, protože jsou uvnitř a místní ve vaší virtuální síti.  Pokud chcete vytvořit odchozí připojení k cíli mimo virtuální síť, máte dvě možnosti:
        - přiřazení veřejné IP adresy Standardní skladové položky jako veřejné IP adresy na úrovni instance prostředku virtuálního počítače nebo
        - umístěte prostředek virtuálního počítače do back-endového fondu veřejného standardního nástroje pro vyrovnávání zatížení.

        Obě umožní odchozí připojení z virtuální sítě mimo virtuální síť. 

        Pokud máte _pouze_ interní standardní nástroj pro vyrovnávání zatížení přidružený k back-endovému fondu, ve kterém je umístěn prostředek virtuálního počítače, může váš virtuální počítač dosáhnout pouze prostředků virtuální sítě a [koncových bodů služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).  Chcete-li vytvořit odchozí připojení, postupujte podle kroků popsaných v předchozím odstavci.

        Odchozí připojení prostředku virtuálního počítače, který není přidružen ke standardním skum, zůstává jako dříve.

        Prohlédněte si [podrobnou diskusi o odchozích připojeních](load-balancer-outbound-connections.md).

* **Zóny dostupnosti**: Standardní vyvažovač zatížení podporuje další možnosti v oblastech, kde jsou k dispozici zóny dostupnosti. Tyto funkce jsou přírůstkové pro všechny standardní vyrovnávání zatížení poskytuje.  Konfigurace zón dostupnosti jsou k dispozici pro oba typy, veřejné i interní standardní nástroj pro vyrovnávání zatížení.
 Front-end redundantní zóny přežije selhání zóny a je obsluhován vyhrazenou infrastrukturou ve všech zónách současně. 
Kromě toho můžete zaručit frontend pro určitou zónu. Zónová frontend sdílí osud s příslušnou zónou a je obsluhována pouze vyhrazenou infrastrukturou v jedné zóně.
Vyrovnávání zatížení mezi zónami je k dispozici pro back-endový fond a jakýkoli prostředek virtuálního počítače ve virtuální síti může být součástí back-endového fondu.
Základní vyrovnávání zatížení nepodporuje zóny.
Další informace naleznete [v podrobné diskusi o schopnostech souvisejících s dostupností](load-balancer-standard-availability-zones.md) a o [zónách dostupnosti.](../availability-zones/az-overview.md)

* **Porty HA:** Můžete nakonfigurovat pravidla vyrovnávání zatížení tak, aby vaše škálování aplikace a byly vysoce spolehlivé. Při použití pravidla vyrovnávání zatížení portů HA bude standardní vyvažovač zatížení poskytovat vyrovnávání zatížení na každém dočasném portu front-endové IP adresy interního standardního vykladače zatížení.  Tato funkce je užitečná pro jiné scénáře, kde je nepraktické nebo nežádoucí určit jednotlivé porty. Pravidlo vyrovnávání zatížení portů HA umožňuje vytvářet scénáře n+1 aktivní pasivní nebo aktivní aktivní pro síťová virtuální zařízení a všechny aplikace, které vyžadují velké rozsahy příchozích portů.  Sondu stavu lze určit, které back-endy by měly přijímat nové toky.  K emulace scénáře rozsahu portů můžete použít skupinu zabezpečení sítě. Základní vyrovnávání zatížení nepodporuje porty HA.
Přezkoumat [podrobnou diskusi o portech HA](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Pokud plánujete použít síťové virtuální zařízení, obraťte se na svého dodavatele pokyny, zda byl jeho produkt testován s porty HA a postupujte podle jejich konkrétních pokynů pro implementaci. 

* **Více frontendů**: Vykladač zatížení podporuje více pravidel s více front-endy.  Standardní vyrovnávání zatížení rozšiřuje to odchozí scénáře.  Odchozí scénáře jsou v podstatě inverzní pravidlo příchozí vyrovnávání zatížení.  Pravidlo vyrovnávání vstupního zatížení také vytvoří přidruženého připojení pro odchozí připojení. Standardní nástroj pro vyrovnávání zatížení používá všechny frontendy přidružené k prostředku virtuálního počítače prostřednictvím pravidla vyrovnávání zatížení.  Kromě toho parametr na pravidlo vyrovnávání zatížení a umožňuje potlačit pravidlo vyrovnávání zatížení pro účely odchozí připojení, které umožňuje výběr konkrétnífront-endů včetně žádné.

Pro srovnání základní vyrovnávání zatížení vybere jeden front-end náhodně a není možné určit, který z nich byl vybrán.
## <a name="load-balancer-types"></a>Typy nástrojů pro vyrovnávání zatížení

### <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Veřejný Load Balancer

Veřejný systém vyrovnávání zatížení mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítačů. Balancer zatížení mapuje provoz na druhou stranu pro přenos odezvy z virtuálního provozu. Můžete distribuovat konkrétní typy provozu mezi více virtuálních počítačů nebo služeb pomocí pravidel vyrovnávání zatížení. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.

>[!NOTE]
>Můžete implementovat pouze jeden veřejný nástroj pro vyrovnávání zatížení a jeden interní nástroj pro vyrovnávání zatížení pro jednu sadu dostupnosti.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který je sdílen mezi třemi virtuálními servery pro veřejnost a portem TCP 80. Tyto tři virtuální počítače se nacházejí ve skupině s vyrovnáváním zatížení.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Veřejný odvykač zatížení">
</p>

*Obrázek: Vyladění webového provozu pomocí veřejného systému vyrovnávání zatížení*

Internetoví klienti odesílají požadavky na webové stránky na veřejnou IP adresu webové aplikace na portu TCP 80. Azure Balancer distribuuje požadavky napříč třemi virtuálními počítači v sadě s vyrovnáváním zatížení. Další informace o algoritmech vykladače zatížení naleznete v [tématu Koncepty vykladačů zatížení](concepts-limitations.md#load-balancer-concepts).

Azure Load Balancer distribuuje síťový provoz rovnoměrně mezi více instancí virtuálních počítače ve výchozím nastavení. Můžete také nakonfigurovat spřažení relací. Další informace naleznete [v tématu Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Interní systém vyrovnávání zatížení

Interní nástroj pro vyrovnávání zatížení směruje provoz pouze na prostředky, které jsou uvnitř virtuální sítě nebo které používají VPN pro přístup k infrastruktuře Azure, na rozdíl od veřejného nástroje pro vyrovnávání zatížení. Infrastruktura Azure omezuje přístup k front-endOVÝM IP adresám virtuální sítě s vyrovnáváním zatížení. Front-end IP adresy a virtuální sítě nejsou nikdy přímo vystaveny koncovému bodu internetu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků.

Interní Load Balancer umožňuje následující typy vyrovnávání zatížení:

* **V rámci virtuální sítě:** Vyrovnávání zatížení z virtuálních počítačů ve virtuální síti na sadu virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro virtuální síť mezi místními:** Vyrovnávání zatížení z místních počítačů do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro vícevrstvé aplikace:** Vyrovnávání zatížení pro vícevrstvé aplikace orientované na internet, kde back-endové vrstvy nejsou orientované na internet. Back-endové vrstvy vyžadují vyrovnávání zatížení provozu z úrovně směřující k internetu. Podívejte se na další obrázek.
* **Pro obchodní aplikace:** Vyrovnávání zatížení pro obchodní aplikace hostované v Azure bez dalšího hardwaru nebo softwaru nástroje pro vyrovnávání zatížení. Tento scénář zahrnuje místní servery, které jsou v sadě počítačů, jejichž provoz je vyrovnávání zatížení.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Veřejný odvykač zatížení">
</p>

*Obrázek: Vyladění vícevrstvých aplikací pomocí veřejného i interního vykladače zatížení*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a>Porovnání skladových položek Load Balanceru

Vyrovnávání zatížení podporuje základní i standardní skuty. Tyto skuse se liší ve velikosti scénáře, funkce a ceny. Jakýkoli scénář, který je možné se základní vyrovnávání zatížení lze vytvořit pomocí standardního vyrovnávání zatížení. Api pro obě skladové položky jsou podobné a jsou vyvolány prostřednictvím specifikace skladové položky. Rozhraní API pro podporu skum pro vyrovnávání zatížení a `2017-08-01` veřejné IP je k dispozici počínaje rozhraní API. Obě skum sdílejí stejné obecné rozhraní API a strukturu.

Kompletní scénář konfigurace se může mírně lišit v závislosti na skladové položky. Dokumentace nástroje pro vyrovnávání zatížení volá, když se článek vztahuje pouze na konkrétní skladovou položku. Porovnání a vysvětlení rozdílů najdete v následující tabulce. Další informace najdete [v tématu Přehled nástroje pro vyrovnávání zatížení Azure Standard](load-balancer-standard-overview.md).

Informace o upgradu základního vykladače zatížení na standardní, naleznete [v tématu Upgrade základního vytácení zatížení](upgrade-basic-standard.md).

>[!NOTE]
> Společnost Microsoft doporučuje standardní vyrovnávání zatížení.
Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Vykladač zatížení a veřejná lokace IP adresy se musí shodovat při jejich použití s veřejnými IP adresami. Vyrovnávání zatížení a veřejné sloky IP nejsou proměnlivé.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Další informace naleznete v tématu [Limity pro vyrovnávání zatížení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Podrobnosti o Load Balanceru úrovně Standard najdete v [přehledu](load-balancer-standard-overview.md), na stránce s [cenami](https://aka.ms/lbpricing) a ve [smlouvě SLA](https://aka.ms/lbsla).

## <a name="limitations"></a><a name = "limitations"></a>Omezení

- SKU nejsou proměnlivé. Skladovou položku existujícího prostředku nelze změnit.
- Prostředek samostatného virtuálního počítače, prostředek skupiny dostupnosti nebo prostředek škálovací sady virtuálních strojů může odkazovat na jednu skladovou položku, nikdy na obojí.
- Pravidlo nástroje pro vyrovnávání zatížení nemůže přesáhnout dvě virtuální sítě.  Frontendy a jejich související back-endové instance musí být umístěny ve stejné virtuální síti.  
- [Přesunout operace předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md) nejsou podporovány pro standardní LB a veřejné IP prostředky.
- Role webových pracovních dělníků bez virtuální sítě a dalších služeb platformy Microsoftu mohou být přístupné z instancí za pouze interním standardním vyrovnáváním zatížení. Nesmíte se na to spoléhat, protože příslušná služba sama nebo podkladová platforma se může změnit bez předchozího upozornění. Vždy musíte předpokládat, že je třeba vytvořit [odchozí připojení](load-balancer-outbound-connections.md) explicitně v případě potřeby při použití pouze interní standardní vyrovnávání zatížení.

- Služba vyrovnávání zatížení poskytuje vyrovnávání zatížení a předávání portů pro konkrétní protokoly TCP nebo UDP. Pravidla vyrovnávání zatížení a příchozí pravidla NAT podporují protokoly TCP a UDP, ale ne jiné protokoly IP včetně protokolu ICMP.

  Vykladač zatížení neukončuje, nereaguje ani jinak neinteraguje s datovou částí toku Protokolu UDP nebo TCP. Není to proxy. Úspěšné ověření připojení k front-endu musí probíhat v pásmu se stejným protokolem používaným v pravidle vyrovnávání zatížení nebo příchozím pravidlem NAT. Alespoň jeden z vašich virtuálních počítačů musí generovat odpověď pro klienta zobrazit odpověď z front-endu.

  Nepřijetí odezvy v pásmu z front-endu nástroje pro vyrovnávání zatížení znamená, že žádné virtuální počítače nemohou reagovat. Nic nemůže komunikovat s front-endnástroje pro vyrovnávání zatížení bez virtuálního počítače schopného reagovat. Tento princip platí také pro odchozí připojení, kde je podporována pouze maškaráda portu SNAT pro protokoly TCP a UDP. Všechny ostatní protokoly IP, včetně protokolu ICMP, se nezdaří. Přiřaďte veřejnou IP adresu na úrovni instance, abyste tento problém zmírnili. Další informace naleznete [v tématu Principy SNAT a PAT](load-balancer-outbound-connections.md#snat).

- Interní vykladače zatížení nepřekládají odchozí připojení pocházející z předníčásti interního vykladače zatížení, protože obě jsou v privátním adresním prostoru IP. Veřejné nástroje pro vyrovnávání zatížení poskytují [odchozí připojení](load-balancer-outbound-connections.md) z privátních IP adres uvnitř virtuální sítě k veřejným IP adresám. U interních vykladačů zatížení se tento přístup vyhýbá potenciálnímu vyčerpání portu SNAT v rámci jedinečného interního adresního prostoru IP, kde není překlad vyžadován.

  Vedlejším účinkem je, že pokud odchozí tok z virtuálního účtu ve fondu back-end pokusí tok do přední části vnitřní vyrovnávání zatížení ve svém fondu _a_ je mapován zpět na sebe, dvě části toku neshodují. Protože se neshodují, tok selže. Tok je úspěšný, pokud tok nebyl mapovat zpět na stejný virtuální virtuální virtuální proud ve fondu back-endu, který vytvořil tok do front-endu.

  Když se tok mapuje zpět na sebe, zdá se, že odchozí tok pochází z virtuálního počítače do front-endu a odpovídající příchozí tok zřejmě pochází z virtuálního počítače sám sobě. Z hlediska hostovaného operačního systému příchozí a odchozí části stejného toku neodpovídají uvnitř virtuálního počítače. Zásobník TCP nerozpozná tyto poloviny stejného toku jako součást stejného toku. Zdroj a cíl se neshodují. Když se tok mapuje na jiný virtuální virtuální proud ve fondu back-endu, poloviny toku se shodují a virtuální ho může reagovat na tok.

  Příznakem pro tento scénář je občasné časové limity připojení při toku vrátí do stejné back-endu, který vznikl toku. Mezi běžná zástupná řešení patří vložení vrstvy proxy za interní nástroj pro vyrovnávání zatížení a použití pravidel stylu návratu serveru (DSR). Další informace naleznete [v tématu Více frontových konců pro Azure Load Balancer](load-balancer-multivip-overview.md).

  Můžete kombinovat interní nástroj pro vyrovnávání zatížení s libovolným proxy serverem třetích stran nebo použít interní [aplikační bránu](../application-gateway/application-gateway-introduction.md) pro scénáře proxy s HTTP/HTTPS. Zatímco můžete použít veřejný nástroje pro vyrovnávání zatížení ke zmírnění tohoto problému, výsledný scénář je náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md#snat). Vyhněte se tomuto druhému přístupu, pokud není pečlivě řízen.

- Obecně platí, že předávání fragmentů IP není podporováno u pravidel vyrovnávání zatížení. Fragmentace PROTOKOLU IP paketů UDP a TCP není u pravidel vyrovnávání zatížení podporována. Pravidla vyrovnávání zatížení portů s vysokou dostupností lze použít k předání existujících fragmentů PROTOKOLU IP. Další informace naleznete v tématu [Přehled portů s vysokou dostupností](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Další kroky

- Viz [Upgrade základního vykladače zatížení](upgrade-basic-standard.md) a upgrade základního vykladače zatížení na standardní vyvažovač evidenční stav zatížení.
- Najdete [v tématu Vytvoření veřejného standardního systému vyrovnávání zatížení,](quickstart-load-balancer-standard-public-portal.md) abyste mohli začít používat systém Vyrovnávání zatížení: vytvořte ho, vytvořte virtuální servery s nainstalovaným vlastním rozšířením služby IIS a vyvážení zatížení webové aplikace mezi virtuálními zařízeními.
- Další informace o [Azure Load Balancer](load-balancer-overview.md).
- Informace o použití [standardního vykladače zatížení a zón dostupnosti](load-balancer-standard-availability-zones.md).
- Další informace o [sondách stavu](load-balancer-custom-probe-overview.md).
- Další informace o [standardní diagnostice nástroje pro vyrovnávání zatížení](load-balancer-standard-diagnostics.md).
- Informace o použití [služby Balancer pro odchozí připojení](load-balancer-outbound-connections.md).
- Další informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md).
- Informace o [obnovení protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- Přečtěte si o [standardním vyvažovači zatížení s pravidly vyrovnávání zatížení portů HA](load-balancer-ha-ports-overview.md).
- Další informace o používání [služby Balancer s více frontendy](load-balancer-multivip-overview.md).
- Další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
