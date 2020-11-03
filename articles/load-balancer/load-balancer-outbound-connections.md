---
title: Azure Load Balancer odchozího proxy serveru
description: Popisuje, jak se používá Azure Load Balancer jako proxy pro odchozí připojení k Internetu.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 185bb47677e978a3098f39024995da6399f90658
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241765"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Azure Load Balancer odchozího proxy serveru

Nástroj pro vyrovnávání zatížení Azure je možné použít jako proxy server pro odchozí připojení k Internetu. Nástroj pro vyrovnávání zatížení poskytuje odchozí připojení pro instance back-end. 

Tato konfigurace používá **Překlad zdrojového síťového adres (SNAT)** . SNAT přepíše IP adresu back-endu na veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení. 

SNAT umožňuje **maskování IP** instance back-endu. Tato maskování brání vnějším zdrojům, aby měly přímo adresu back-end instancí. 

Sdílení IP adresy mezi back-end instancemi snižuje náklady na statické veřejné IP adresy a podporuje scénáře, jako je například zjednodušení seznamů povolených IP adres, ze známých veřejných IP adres. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Sdílení portů mezi prostředky

Pokud back-end prostředky nástroje pro vyrovnávání zatížení nemají adresy veřejných IP adres na úrovni instance (ILPIP), navážou odchozí připojení prostřednictvím front-endu veřejného nástroje pro vyrovnávání zatížení.

Porty slouží ke generování jedinečných identifikátorů používaných k údržbě různých toků. Internet používá k poskytnutí tohoto rozlišení pět-Tuple.

Pět-Tuple se skládá z těchto:

* Cílová IP adresa
* Cílový port
* Zdrojová IP adresa
* Zdrojový port a protokol pro zajištění tohoto rozlišení.

Pokud se port používá pro příchozí připojení, bude mít **naslouchací proces** pro požadavky na příchozí připojení na tomto portu a nedá se použít pro odchozí připojení. 

Aby bylo možné navázat odchozí připojení, musí být k dispozici **dočasný port** pro určení portu, na kterém má být zajištěna komunikace a údržba odlišného toku přenosů. 

Každá IP adresa má 65 535 portů. Prvních 1024 portů je vyhrazeno jako **systémové porty** . Každý port se dá použít pro příchozí nebo odchozí připojení pro TCP a UDP. 

Ze zbývajících portů poskytuje Azure 64 000 pro použití jako **dočasné porty** . Pokud se IP adresa přidá jako konfigurace IP adresy front-endu, můžou se tyto dočasné porty použít pro SNAT.

Prostřednictvím odchozích pravidel se tyto porty SNAT dají distribuovat do back-endu instancí, aby mohly sdílet veřejné IP adresy nástroje pro vyrovnávání zatížení pro odchozí připojení.

Sítě na hostiteli pro jednotlivé instance back-endu budou probíhají až do paketů, které jsou součástí odchozího připojení. Hostitel přepíše zdrojovou IP adresu jednou z veřejných IP adres. Hostitel přepíše zdrojový port každého odchozího paketu na jeden z portů SNAT.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Vyčerpání portů

Každé připojení ke stejné cílové IP adrese a cílovému portu bude používat port SNAT. Toto připojení udržuje odlišný **přenosový tok** z instance back-endu nebo z **klienta** na **Server** . Tento proces přidělí serveru jedinečný port, na který se má adresovat přenos. Bez tohoto procesu nevíte na klientském počítači, který tok paketu je součástí.

Představte si, že budete mít více prohlížečů https://www.microsoft.com , což je:

* Cílová IP adresa = 23.53.254.142
* Cílový port = 443
* Protokol = TCP

Bez různých cílových portů pro návratový provoz (port SNAT použitý k navázání připojení) nebude mít klient žádný způsob, jak oddělit jeden výsledek dotazu od jiného.

Odchozí připojení se můžou rozpojovat. Instanci back-endu je možné přidělit nedostatečným portům. Bez povoleného **opětovného použití připojení** se zvyšuje riziko **vyčerpání portů** SNAT.

Nová odchozí připojení k cílové IP adrese selžou, když dojde k vyčerpání portů. Po zpřístupnění portu budou připojení úspěšná. K této vyčerpání dojde, když se porty 64 000 z IP adresy šíří dynamicky napříč celou řadou back-end instancí. Pokyny ke zmírnění vyčerpání portů SNAT najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

Pro připojení TCP bude nástroj pro vyrovnávání zatížení používat pro každou cílovou IP adresu a port jeden port SNAT. Tento Multiuse umožňuje více připojení ke stejné cílové IP adrese se stejným portem SNAT. Tato Multiuse je omezená, pokud není připojení k různým cílovým portům.

V případě připojení UDP využívá nástroj pro vyrovnávání zatížení algoritmus **překladu adres (NAT) s omezeným portem** , který využívá jeden port SNAT na cílovou IP adresu bez ohledu na cílový port. 

Port se znovu používá pro neomezený počet připojení. Port se znovu použije jenom v případě, že cílová IP adresa nebo port je jiný.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Přidělení portu

Každé veřejné IP adresy přiřazené jako front-endové IP službě pro vyrovnávání zatížení mají porty 64 000 SNAT pro své členy fondu back-endu. Porty nelze sdílet se členy fondu back-end. Rozsah portů SNAT může použít jenom jediná instance back-endu, aby se zajistilo správné směrování vrácených paketů. 

Doporučuje se použít explicitní odchozí pravidlo pro konfiguraci přidělování portů SNAT. Toto pravidlo maximalizuje počet portů SNAT, které každá instance back-end má k dispozici pro odchozí připojení. 

Pokud používáte automatické přidělování odchozích SNAT prostřednictvím pravidla vyrovnávání zatížení, bude tabulka alokace definovat vaše přidělení portu.

V následující <a name="snatporttable"></a> tabulce jsou uvedena předalokace portů SNAT pro vrstvy velikostí back-endu:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené porty SNAT na konfiguraci IP adres |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 | 

>[!NOTE]
> Pokud máte back-end fond s maximální velikostí 10, každá instance může mít 64000/10 = 6 400 portů, pokud definujete explicitní odchozí pravidlo. Podle výše uvedené tabulky bude mít každá z nich jenom 1 024, pokud zvolíte automatické přidělování.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Odchozí pravidla a Virtual Network překlad adres (NAT)

Azure Load Balancer odchozí pravidla a Virtual Network překlad adres (NAT) jsou dostupné možnosti pro odchozí přenos dat z virtuální sítě.

Další informace o odchozích pravidlech najdete v tématu [odchozí pravidla](outbound-rules.md).

Další informace o službě Azure Virtual Network NAT najdete v tématu [co je azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Omezení

*   Pokud se přijme nebo pošle **TCP RST** , budou porty vydány po 15 sekundách.
*   Pokud se přijme nebo pošle **FINACK** , porty se uvolní po 240 sekund.
*   Když je připojení nečinné, protože se neodesílají žádné nové pakety, porty se uvolní po 4 až 120 minutách.
  * Tato prahová hodnota se dá nakonfigurovat prostřednictvím odchozích pravidel.
*   Každá IP adresa poskytuje 64 000 portů, které se dají použít pro SNAT.
*   Každý port se dá použít pro připojení TCP i UDP k cílové IP adrese.
  * Port UDP SNAT je vyžadován, pokud je cílový port jedinečný nebo nikoli. Pro každé připojení UDP k cílové IP adrese se použije jeden port UDP SNAT.
  * Port TCP SNAT lze použít pro více připojení ke stejné cílové IP adrese, pokud jsou cílové porty odlišné.
*   K vyčerpání SNAT dojde v případě, že instance back-endu běží z daných portů SNAT. Nástroj pro vyrovnávání zatížení může mít stále nepoužívané porty SNAT. Pokud použité porty SNAT překročí zadané porty SNAT, nebude možné vytvořit nová odchozí připojení.

## <a name="next-steps"></a>Další kroky

*   [Řešení selhání odchozího připojení kvůli vyčerpání SNAT](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Projděte si metriky SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) a seznamte se se správným způsobem, jak je filtrovat, rozdělit a zobrazit.

