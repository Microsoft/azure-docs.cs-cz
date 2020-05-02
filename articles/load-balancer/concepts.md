---
title: Azure Load Balancer koncepty
description: Přehled konceptů Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 1fc6721ede07100b4a6769eef8893857cbde119d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629991"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer koncepty

Nástroj pro vyrovnávání zatížení poskytuje několik možností pro aplikace UDP i TCP. 

## <a name="load-balancing-algorithm"></a>Algoritmus vyrovnávání zatížení
Můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu z front-endu do back-endového fondu. Azure Load Balancer používá algoritmus hash pro distribuci příchozích toků (ne bajtů). Nástroj pro vyrovnávání zatížení přepíše hlavičky toků do instancí fondu back-endu. Server je k dispozici pro příjem nových toků, když sonda stavu indikuje back-end koncový bod, který je v pořádku.

Nástroj pro vyrovnávání zatížení ve výchozím nastavení používá hodnotu hash s pěti řazenými kolekcemi členů.

Hodnota hash zahrnuje:

- **Zdrojová IP adresa**
- **Zdrojový port**
- **Cílová IP adresa**
- **Cílový port**
- **Číslo protokolu IP pro mapování toků na dostupné servery**

Spřažení ke zdrojové IP adrese je vytvořeno pomocí dvou nebo tří řazených kolekcí hash. Pakety stejného toku přicházejí na stejnou instanci za front-end s vyrovnáváním zatížení. 

Zdrojový port se změní, když klient spustí nový tok ze stejné zdrojové IP adresy. Výsledkem je, že hodnota hash pěti řazených kolekcí by mohla způsobit, že se provoz přejde do jiného koncového bodu back-endu.
Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](./load-balancer-distribution-mode.md).

Následující obrázek ukazuje distribuci na základě hodnoty hash:

  ![Distribuce na základě hodnoty hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Obrázek: Distribuce na základě hodnoty hash*

## <a name="application-independence-and-transparency"></a>Nezávislost a průhlednost aplikace

 Nástroj pro vyrovnávání zatížení přímo nekomunikuje s protokolem TCP nebo UDP nebo aplikační vrstvou. Může být podporován jakýkoli scénář aplikace TCP nebo UDP. Load Balancer neukončí ani nepochází z toků ani nekomunikuje s datovou částí toku. Nástroj pro vyrovnávání zatížení neposkytuje funkci brány aplikační vrstvy. K protokolu handshake se vždy dochází přímo mezi klientem a instancí back-end fondu. Odpověď na příchozí tok je vždy odpověď z virtuálního počítače. Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.

* Každý koncový bod je zodpovězený virtuálním počítačem. Například dojde k chybě handshake TCP mezi klientem a vybraným back-end virtuálním počítačem. Odpověď na požadavek na front-end je odpověď generovaná back-end virtuálním počítačem. Když úspěšně ověříte připojení k front-endu, ověřujete připojení v rámci alespoň jednoho back-endu virtuálního počítače.
* Datové části aplikace jsou pro nástroj pro vyrovnávání zatížení transparentní. Je možné, že bude podporována jakákoli aplikace UDP nebo TCP.
* Vzhledem k tomu, že nástroj pro vyrovnávání zatížení nekomunikuje s datovou částí TCP a poskytuje přesměrování zpracování TLS, můžete vytvořit komplexní šifrované scénáře. Použití nástroje pro vyrovnávání zatížení získává velký rozsah pro aplikace TLS tím, že ukončuje připojení TLS na samotném virtuálním počítači. Například kapacita klíče relace TLS je omezená jenom typem a počtem virtuálních počítačů, které přidáte do fondu back-end.

## <a name="outbound-connections"></a>Odchozí připojení 
Toky z back-endu fondu do veřejných IP adres se mapují na front-end. Azure překládá odchozí připojení k veřejné IP adrese front-endu prostřednictvím odchozího pravidla vyrovnávání zatížení. 

Tato konfigurace má následující výhody:

* Snadný upgrade a zotavení po havárii služeb, protože front-end lze dynamicky namapovat na jinou instanci služby.
* Jednodušší Správa seznamů řízení přístupu (ACL). Seznamy řízení přístupu vyjádřené jako IP adresy front-endu se nemění, protože služby se škálují nahoru nebo dolů nebo se znovu nasazují. Překlad odchozích připojení k menšímu počtu IP adres, než jsou počítače, snižuje zátěž pro implementaci seznamů bezpečných příjemců.

Další informace o překladu zdrojového síťového adres (SNAT) a Azure Load Balancer najdete v části [SNAT a Azure Load Balancer](load-balancer-outbound-connections.md#snat).

## <a name="availability-zones"></a>Zóny dostupnosti

Load Balancer úrovně Standard podporuje další možnosti v oblastech, kde jsou k dispozici Zóny dostupnosti. Tyto funkce jsou přírůstkové pro všechny služby Load Balancer úrovně Standard.  Konfigurace Zóny dostupnosti jsou k dispozici pro oba typy standardního nástroje pro vyrovnávání zatížení. veřejné a interní.


Zóna redundantní ve frontě zadržela selhání zóny pomocí vyhrazené infrastruktury ve všech zónách současně. Navíc můžete zaručit front-end pro konkrétní zónu. 

Oblast front-endu se obsluhuje pomocí vyhrazené infrastruktury v jedné zóně. Pro back-end fond je k dispozici vyrovnávání zatížení mezi zónami. Každý prostředek virtuálního počítače ve virtuální síti může být součástí fondu back-endu.

Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje zóny.
Další informace najdete [v podrobné diskuzi o schopnostech souvisejících s zóny dostupnosti](load-balancer-standard-availability-zones.md) a [zóny dostupnosti přehled](../availability-zones/az-overview.md) .

## <a name="ha-ports"></a>Porty HA

Pravidla vyrovnávání zatížení portu HA můžete nakonfigurovat tak, aby se škálování aplikace a vysoce spolehlivě zajistila. 

Tato pravidla poskytují vyrovnávání zatížení podle toku na krátkodobém portu IP adresy front-endu interního nástroje pro vyrovnávání zatížení.

Tato funkce je užitečná v případě, že je nepraktické nebo nežádoucí zadání jednotlivých portů. Pravidlo portů HA umožňuje vytvořit scénáře aktivní – pasivní nebo aktivní – aktivní n + 1. Tyto scénáře jsou pro síťová virtuální zařízení a všechny aplikace, které vyžadují velké rozsahy vstupních portů. Sondu stavu lze použít k určení, které back-endy by měly přijímat nové toky.  Skupinu zabezpečení sítě můžete použít k emulaci scénáře rozsahu portů. 

Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje porty HA. Přečtěte si [podrobné informace o portech ha](load-balancer-ha-ports-overview.md) .

>[!IMPORTANT]
> Pokud plánujete použít síťové virtuální zařízení, obraťte se na dodavatele a Projděte si informace o tom, jestli se jejich produkt testuje pomocí portů HA, a postupujte podle svých specifických pokynů k implementaci. 

## <a name="multiple-frontends"></a>Několik front-endů

Nástroj pro vyrovnávání zatížení podporuje více pravidel s více front-endu.  Standard Load Balancer rozšiřuje tuto schopnost na odchozí scénáře. 

Odchozí pravidla jsou inverzní k příchozímu pravidlu. Odchozí pravidlo vytvoří přidružení pro odchozí připojení. Load Balancer úrovně Standard používá všechny front-endové služby přidružené k prostředku virtuálního počítače prostřednictvím pravidla vyrovnávání zatížení.

Kromě toho parametr pravidla vyrovnávání zatížení umožňuje potlačit pravidlo vyrovnávání zatížení pro účely odchozího připojení, které umožňuje výběr určitých front-endu, včetně žádného.

Za účelem porovnání vybere nástroj Load Balancer úrovně Single-Endu s náhodným stavem. Není možnost řídit, který front-end byl vybrán.

## <a name="limitations"></a><a name = "limitations"></a>Omezení

- SKU nejsou proměnlivé. SKU existujícího prostředku nemůžete změnit.
- Samostatný prostředek virtuálního počítače, prostředek sady dostupnosti nebo prostředek sady škálování virtuálního počítače můžou odkazovat na jednu SKU, nikdy ne obojí.
- Pravidlo nástroje pro vyrovnávání zatížení nemůže zahrnovat dvě virtuální sítě.  Front-endy a jejich související back-end instance se musí nacházet ve stejné virtuální síti.  
- [Operace přesunu předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md) se pro standardní nástroj pro vyrovnávání zatížení a prostředky veřejné IP adresy nepodporují.
- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné jenom z instancí za interním standardním nástrojem pro vyrovnávání zatížení. Nespoléhá se na tuto přístupnost, protože příslušná služba nebo podkladová platforma se mohou změnit bez předchozího upozornění. Pokud se při použití standardního interního nástroje pro vyrovnávání zatížení vyžaduje odchozí připojení, musí se nakonfigurovat [odchozí připojení](load-balancer-outbound-connections.md) .
- Nástroj pro vyrovnávání zatížení poskytuje vyrovnávání zatížení a přesměrování portů pro konkrétní protokoly TCP a UDP. Pravidla vyrovnávání zatížení a pravidla příchozího překladu adres (NAT) podporují protokoly TCP a UDP, ale ne jiné protokoly IP, včetně protokolu ICMP.

  Nástroj pro vyrovnávání zatížení nezavřá, reaguje nebo jinak nepracuje s datovou částí toku UDP nebo TCP. Load Balancer nefunguje jako proxy. 
  
  Musí proběhnout úspěšné připojení k front-endu. Toto připojení musí být stejného portu, ve kterém se používá Vyrovnávání zatížení nebo příchozí pravidlo NAT. Pokud chcete zobrazit odpověď z front-endu, jeden virtuální počítač ve fondu back-endu musí reagovat.

  Nepovedlo se přijmout odpověď od front-endu, protože nemůžou odpovědět žádné virtuální počítače. Interakce s front-endu nástroje pro vyrovnávání zatížení selže, aniž by virtuální počítač mohl reagovat. 
  
  Tato zásada platí také pro odchozí připojení, kde je maskovací port SNAT podporován pouze pro protokoly TCP a UDP. Všechny ostatní protokoly IP, včetně protokolu ICMP, selžou. 
  
  Pokud chcete tento problém vyřešit, přiřaďte prostředku veřejnou IP adresu. Další informace najdete v tématu [Princip SNAT a Pat](load-balancer-outbound-connections.md#snat).

- Interní nástroje pro vyrovnávání zatížení nepřevádějí odchozí vytvořená připojení na front-end interního nástroje pro vyrovnávání zatížení, protože obě jsou v privátním adresním prostoru IP adres. Veřejné nástroje pro vyrovnávání zatížení poskytují [odchozí připojení](load-balancer-outbound-connections.md) z privátních IP adres uvnitř virtuální sítě k veřejným IP adresám. U interních nástrojů pro vyrovnávání zatížení tento přístup zabraňuje možnému vyčerpání portů SNAT v rámci jedinečného interního adresního prostoru IP adres, kde není požadován překlad.

  Odchozí tok z back-endového virtuálního počítače do front-endu interního nástroje pro vyrovnávání zatížení se nezdaří. K selhání dojde, když je tok mapován zpět na sebe. Dvě ramena toku se neshodují a tok selže.
  
  Když se tok mapuje zpátky na sebe samé, zdá se, že odchozí tok pochází z virtuálního počítače do front-endu.

  Tok se úspěšně dokončí, pokud nebyl namapován zpátky na stejný virtuální počítač v back-endu, který tok vytvořil.
  
  V rámci virtuálního počítače se neshodují vstupní a výstupní části toku. Zásobník TCP nerozpozná tyto poloviny stejného toku jako součást stejného toku. Zdroj a cíl se neshodují. Virtuální počítač může reagovat, když se tok mapuje na jiný virtuální počítač v back-endu. Počet polovin pro porovnávání toků a připojení může pokračovat.

  Příznakem pro tento scénář jsou přerušované časové limity připojení. Mezi běžná řešení patří vložení vrstvy proxy za interním nástrojem pro vyrovnávání zatížení a použití pravidel typu "přímé vrácení serveru (DSR)". Další informace naleznete v tématu [více front-endy pro Azure Load Balancer](load-balancer-multivip-overview.md).

  Interní nástroj pro vyrovnávání zatížení můžete zkombinovat s jakýmkoli proxy třetích stran. K dispozici jsou také použití vnitřních [Application Gateway](../application-gateway/application-gateway-introduction.md) pro scénáře proxy s protokolem HTTP/HTTPS. I když můžete použít veřejný Nástroj pro vyrovnávání zatížení k zmírnění tohoto problému, je výsledný scénář náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md#snat). Vyhněte se tomuto druhému přístupu, pokud se pečlivě nespravuje.

- Obecně platí, že při vyrovnávání zatížení nejsou přesměrovací fragmenty IP adresy podporovány. Pro pravidla vyrovnávání zatížení se nepodporují fragmentace paketů UDP a TCP. Pravidla pro vyrovnávání zatížení portů HA lze použít k přeposílání stávajících fragmentů IP adres. Další informace najdete v tématu [Přehled portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Další kroky

- V tématu [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) můžete začít s používáním Load Balancer: Vytvořte si virtuální počítače s nainstalovanou vlastní příponou IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači.
- Přečtěte si o [Azure Load Balancer odchozích připojeních](load-balancer-outbound-connections.md).
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
