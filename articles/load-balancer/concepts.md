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
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: cb8b3b58f1029a722121f491d202e245300d1aee
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801008"
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

## <a name="load-balancer-terminology"></a>Load Balancer terminologie
| Koncepce | Co to znamená? | Podrobný dokument |
| ---------- | ---------- | ----------|
Odchozí připojení | Toky z back-endu fondu do veřejných IP adres se mapují na front-end. Azure překládá odchozí připojení k veřejné IP adrese front-endu prostřednictvím odchozího pravidla vyrovnávání zatížení. Tato konfigurace má následující výhody. Snadný upgrade a zotavení po havárii služeb, protože front-end lze dynamicky namapovat na jinou instanci služby. Jednodušší Správa seznamů řízení přístupu (ACL). Seznamy řízení přístupu vyjádřené jako IP adresy front-endu se nemění, protože služby se škálují nahoru nebo dolů nebo se znovu nasazují. Překlad odchozích připojení k menšímu počtu IP adres, než jsou počítače, snižuje zátěž pro implementaci seznamů bezpečných příjemců.| Další informace o překladu zdrojového síťového adres (SNAT) a Azure Load Balancer najdete v části [SNAT a Azure Load Balancer](load-balancer-outbound-connections.md).
Zóny dostupnosti | Load Balancer úrovně Standard podporuje další možnosti v oblastech, kde jsou k dispozici Zóny dostupnosti. Tyto funkce jsou přírůstkové pro všechny služby Load Balancer úrovně Standard.  Konfigurace Zóny dostupnosti jsou k dispozici pro oba typy standardního nástroje pro vyrovnávání zatížení. veřejné a interní. Zóna redundantní ve frontě zadržela selhání zóny pomocí vyhrazené infrastruktury ve všech zónách současně. Navíc můžete zaručit front-end pro konkrétní zónu. Oblast front-endu se obsluhuje pomocí vyhrazené infrastruktury v jedné zóně. Pro back-end fond je k dispozici vyrovnávání zatížení mezi zónami. Každý prostředek virtuálního počítače ve virtuální síti může být součástí fondu back-endu. Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje zóny.| Další informace najdete [v podrobné diskuzi o schopnostech souvisejících s zóny dostupnosti](load-balancer-standard-availability-zones.md) a [zóny dostupnosti přehled](../availability-zones/az-overview.md) .
| Porty HA | Pravidla vyrovnávání zatížení portu HA můžete nakonfigurovat tak, aby se škálování aplikace a vysoce spolehlivě zajistila. Tato pravidla poskytují vyrovnávání zatížení podle toku na krátkodobém portu IP adresy front-endu interního nástroje pro vyrovnávání zatížení. Tato funkce je užitečná v případě, že je nepraktické nebo nežádoucí zadání jednotlivých portů. Pravidlo portů HA umožňuje vytvořit scénáře aktivní – pasivní nebo aktivní – aktivní n + 1. Tyto scénáře jsou pro síťová virtuální zařízení a všechny aplikace, které vyžadují velké rozsahy vstupních portů. Sondu stavu lze použít k určení, které back-endy by měly přijímat nové toky.  Skupinu zabezpečení sítě můžete použít k emulaci scénáře rozsahu portů. Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje porty HA. | Přečtěte si [podrobné informace o portech ha](load-balancer-ha-ports-overview.md) .
| Několik front-endů | Nástroj pro vyrovnávání zatížení podporuje více pravidel s více front-endu.  Standard Load Balancer rozšiřuje tuto schopnost na odchozí scénáře. Odchozí pravidla jsou inverzní k příchozímu pravidlu. Odchozí pravidlo vytvoří přidružení pro odchozí připojení. Load Balancer úrovně Standard používá všechny front-endové služby přidružené k prostředku virtuálního počítače prostřednictvím pravidla vyrovnávání zatížení. Kromě toho parametr pravidla vyrovnávání zatížení umožňuje potlačit pravidlo vyrovnávání zatížení pro účely odchozího připojení, které umožňuje výběr určitých front-endu, včetně žádného. Za účelem porovnání vybere nástroj Load Balancer úrovně Single-Endu s náhodným stavem. Není možnost řídit, který front-end byl vybrán.|


## <a name="limitations"></a><a name = "limitations"></a>Omezení

- Pravidlo nástroje pro vyrovnávání zatížení nemůže zahrnovat dvě virtuální sítě.  Front-endové a jejich instance back-endu se musí nacházet ve stejné virtuální síti.  

- Role webového pracovního procesu bez virtuální sítě a dalších služeb platformy Microsoft můžou být dostupné jenom z instancí za standardním interním nástrojem pro vyrovnávání zatížení. Nespoléhá se na tuto přístupnost, protože příslušná služba nebo podkladová platforma se mohou změnit bez předchozího upozornění. Pokud se při použití standardního interního nástroje pro vyrovnávání zatížení vyžaduje odchozí připojení, musí se nakonfigurovat [odchozí připojení](load-balancer-outbound-connections.md) .

- Nástroj pro vyrovnávání zatížení poskytuje vyrovnávání zatížení a přesměrování portů pro konkrétní protokoly TCP a UDP. Pravidla vyrovnávání zatížení a pravidla příchozího překladu adres (NAT) podporují protokoly TCP a UDP, ale ne jiné protokoly IP, včetně protokolu ICMP.

- Odchozí tok z back-endu virtuálního počítače do front-endu interního Load Balancer se nezdaří.

- Předávání fragmentů IP adres není u pravidel vyrovnávání zatížení podporováno. Pro pravidla vyrovnávání zatížení se nepodporují fragmentace paketů UDP a TCP. Pravidla pro vyrovnávání zatížení portů HA lze použít k přeposílání stávajících fragmentů IP adres. Další informace najdete v tématu [Přehled portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Další kroky

- V tématu [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) můžete začít s používáním Load Balancer: Vytvořte si virtuální počítače s nainstalovanou vlastní příponou IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači.
- Přečtěte si o [Azure Load Balancer odchozích připojeních](load-balancer-outbound-connections.md).
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
