---
title: 'Práce na dálku: Síťové virtuální zařízení (NVA) aspekty pro práci na dálku | Brána Azure VPN'
description: Tento článek vám pomůže pochopit věci, které byste měli vzít v úvahu při práci se síťovými virtuálními zařízeními (NVA) v Azure během pandemie COVID-19.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337095"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Práce na dálku: Aspekty síťového virtuálního zařízení (NVA) pro práci na dálku

>[!NOTE]
>Tento článek popisuje, jak můžete využít síťová virtuální zařízení, Azure, síť Microsoft a ekosystém partnerů Azure k vzdálené práci a zmírnění problémů se sítí, kterým čelíte kvůli krizi COVID-19.
>

Někteří zákazníci Azure využívají síťová virtuální zařízení (NVA) třetích stran z Azure Marketplace k poskytování důležitých služeb, jako je vpn z bodu na webu pro své zaměstnance, kteří pracují z domova během epidemie COVID-19. Tento článek popisuje některé pokyny na vysoké úrovni, které je třeba vzít v úvahu při využití virtuálních zařízení v Azure k poskytování řešení vzdáleného přístupu.

## <a name="nva-performance-considerations"></a>Důležité informace o výkonu nva

Všichni hlavní dodavatelé nva na Azure Marketplace by měli mít doporučení ohledně velikosti virtuálního počítače a počtu instancí, které se mají použít při nasazování svých řešení.  Zatímco téměř všichni dodavatelé NVA vám umožní vybrat libovolnou velikost, která je k dispozici v dané oblasti, je velmi důležité, abyste postupovali podle doporučení dodavatelů pro velikosti instancí virtuálních počítačích Azure, protože tato doporučení jsou velikosti virtuálních her, které dodavatel provedl výkon testování v Azure.  

### <a name="consider-the-following"></a>Zvažte následující

- **Kapacita a počet souběžných uživatelů** - Toto číslo je obzvláště důležité pro uživatele VPN point-to-site, protože každý připojený uživatel vytvoří jeden šifrovaný tunel (IPSec nebo SSL VPN).  
- **Agregovaná propustnost** - Jaká je agregovaná šířka pásma, kterou budete potřebovat, abyste vyhověli počtu uživatelů, které potřebujete k poskytnutí vzdáleného přístupu.
- **Velikost virtuálního počítače, kterou budete potřebovat** – vždy byste měli používat velikosti virtuálních počítače doporučené dodavatelem nva.  Pro vpn point-to-site, pokud budete mít hodně souběžných uživatelských připojení, měli byste používat větší velikosti virtuálních počítače, jako jsou virtuální počítače [řady Dv2 a DSv2.](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Řada Dv2 a Dsv2") Tyto virtuální servery mají tendenci mít více virtuálních procesorů a zvládnou více souběžných relací VPN.  Kromě toho, že mají více virtuálních jader, větší velikosti virtuálních virtuálních zařízení v Azure mají větší agregotní kapacitu šířky pásma než menší velikosti virtuálních počítačích.
    > **Důležité:** Každý dodavatel využívá prostředky jinak.  Pokud není jasné, jaké velikosti instancí byste měli použít k přizpůsobení odhadovanému zatížení uživatele, měli byste se obrátit přímo na dodavatele softwaru a požádat ho o doporučení.
- **Počet instancí** – Pokud očekáváte, že budete mít velký počet uživatelů a připojení, existují omezení, jaké škálování velikosti instancí nva může dosáhnout.  Zvažte nasazení více instancí virtuálních počítačů.
- **IPSec VPN vs SSL VPN** - Obecně ipsec VPN implementace fungují lépe než Implementace SSL VPN.  
- **Licencování** – Ujistěte se, že softwarové licence, které jste zakoupili pro řešení NVA, pokryjí náhlý růst, ke kterému můžete dojít během epidemie COVID-19.  Mnoho licenčních programů NVA omezuje počet připojení nebo šířku pásma, které je řešení schopno.
- **Zrychlené vytváření sítí** – zvažte řešení NVA, které má podporu pro zrychlené vytváření sítí.  Zrychlená síť umožňuje virtualizaci vstupně-videa jednoho kořene (SR-IOV) do virtuálního počítače, což výrazně zlepšuje jeho síťový výkon. Tato cesta vysokého výkonu obchází hostitele z datové cesty, snižuje latenci, kolísání a využití procesoru pro použití s nejnáročnějšími síťovými úlohami na podporovaných typech virtuálních počítačů. Akcelerovaná síť je podporována pro většinu velikostí instancí pro obecné účely a optimalizované pro výpočetní výkon se dvěma nebo více virtuálními procesory.

## <a name="monitoring-resources"></a>Sledování zdrojů

Každé řešení NVA má své vlastní nástroje a zdroje pro sledování výkonu jejich NVA.  Informace o tom, zda rozumíte omezením výkonu, můžete zjistit, kdy se vaše služba NVA blíží nebo nadosahuje kapacity, abyste se ujistili, že rozumíte omezením výkonu.  Kromě toho se můžete podívat na Azure Monitor Network Insights a zobrazit základní informace o výkonu o vašich síťových virtuálních zařízeních, jako jsou:

- Využití procesoru
- Síťové vstupy
- Síťové výstupy
- Příchozí toky
- Odchozí toky

## <a name="next-steps"></a>Další kroky

Většina hlavních partnerů NVA zaslali pokyny týkající se škálování pro náhlý, neočekávaný růst během COVID-19. Zde je několik užitečných odkazů na partnerské zdroje.

[Barracuda Povolit práci z domova při zabezpečení dat během COVID-19](https://www.barracuda.com/covid-19/work-from-home "Povolit práci z domova při zabezpečení dat během COVID-19")

[Cisco AnyConnect Implementace a výkon / Škálování Reference pro COVID-19 Příprava](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect Implementace a výkon / Škálování Reference pro COVID-19 Příprava")

[Centrum podpory odezvy Citrix COVID-19](https://www.citrix.com/support/covid-19-coronavirus.html "Centrum podpory odezvy Citrix COVID-19")

[F5 Pokyny k řešení dramatického nárůstu vzdálených pracovníků](https://www.f5.com/business-continuity "F5 Pokyny k řešení dramatického nárůstu vzdálených pracovníků")

[Aktualizace Fortinet COVID-19 pro zákazníky a partnery](https://www.fortinet.com/covid-19.html "Aktualizace COVID-19 pro zákazníky a partnery")

[Palo Alto Sítě COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Sítě COVID-19 Response Center")
