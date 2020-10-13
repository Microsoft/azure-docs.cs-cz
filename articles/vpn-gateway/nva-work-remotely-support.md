---
title: 'Vzdálená práce: požadavky na síťové virtuální zařízení (síťové virtuální zařízení) pro vzdálenou práci | VPN Gateway Azure'
description: Tento článek vám pomůže porozumět tomu, co byste měli vzít v úvahu při práci se síťovými virtuálními zařízeními (síťová virtuální zařízení) v Azure během COVID-19 PANDEMIC.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 4783016e472907392f2d379efa0fed2d90ed21bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595355"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Vzdálená práce: požadavky na síťové virtuální zařízení (síťové virtuální zařízení) pro vzdálenou práci

>[!NOTE]
>Tento článek popisuje, jak můžete využívat síťová virtuální zařízení, Azure, síť Microsoftu a partnerský ekosystém Azure k tomu, abyste mohli vzdáleně pracovat a zmírnit problémy se sítí, ke kterým jste se přistali kvůli COVID-19 krizí.
>

Někteří zákazníci Azure využívají síťová virtuální zařízení (síťová virtuální zařízení) od jiných výrobců z Azure Marketplace k poskytování důležitých služeb, jako je například síť VPN typu Point-to-site pro své zaměstnance, kteří pracují z domova během epidemie COVID-19. Tento článek popisuje některé pokyny vysoké úrovně, které je potřeba vzít v úvahu při využívání síťová virtuální zařízení v Azure k poskytování řešení vzdáleného přístupu.

## <a name="nva-performance-considerations"></a>Požadavky na výkon síťové virtuální zařízení

Všichni hlavní dodavatelé síťové virtuální zařízení v Azure Marketplace by měli mít doporučení pro velikost virtuálního počítače a počet instancí, které se mají použít při nasazení jejich řešení.  I když téměř všichni dodavatelé síťové virtuální zařízení vám umožní vybrat jakoukoli velikost, která je k dispozici v dané oblasti, je velmi důležité dodržovat doporučení dodavatelů pro velikosti instancí virtuálních počítačů Azure, protože tato doporučení jsou velikostmi virtuálních počítačů, které dodavatel dokončil testování výkonu v Azure.  

### <a name="consider-the-following"></a>Vezměte v úvahu následující

- **Kapacita a počet souběžných uživatelů** – toto číslo je zvláště důležité pro uživatele sítě VPN typu Point-to-site, protože každý připojený uživatel vytvoří jedno šifrované tunelové propojení (IPSec nebo SSL VPN).  
- **Agregovaná propustnost** – jedná se o agregovanou šířku pásma, kterou budete potřebovat pro zajištění, že budete potřebovat vzdálený přístup.
- **Velikost virtuálního počítače, kterou budete potřebovat** – měli byste vždycky používat velikosti virtuálních počítačů doporučené dodavatelem síťové virtuální zařízení.  V případě sítě VPN typu Point-to-site, pokud budete mít hodně souběžných uživatelských připojení, byste měli používat větší velikosti virtuálních počítačů, jako jsou virtuální počítače [Dv2 a DSv2 Series](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Řady Dv2 a Dsv2") . Tyto virtuální počítače mají za následek větší vCPU a můžou zpracovávat víc souběžných relací VPN.  Kromě virtuálních jader mají větší velikost virtuálních počítačů v Azure větší agregovanou kapacitu šířky pásma než menší velikosti virtuálních počítačů.
    > **Důležité informace:** Každý dodavatel využívá prostředky odlišně.  Pokud není jasné, jaké velikosti instancí byste měli použít k uspokojení odhadovaného uživatelského zatížení, obraťte se na dodavatele softwaru přímo a požádejte ho o doporučení.
- **Počet instancí** – Pokud očekáváte, že budete mít velký počet uživatelů a připojení, dojde k omezením toho, co může dosáhnout škálování velikosti instancí síťové virtuální zařízení.  Zvažte nasazení více instancí virtuálních počítačů.
- **IPSec VPN vs SSL VPN** – v obecných implementacích IPSec VPN je lepší než implementace SSL VPN.  
- **Licencování** – zajistěte, aby licence k softwaru, které jste zakoupili pro řešení síťové virtuální zařízení, pokryly prudký nárůst, ke kterému může docházet během epidemie COVID-19.  Řada multilicenčních programů síťové virtuální zařízení omezuje počet připojení nebo šířku pásma, na které je řešení schopné.
- **Akcelerované síťové služby** – uvažujte o řešení síťové virtuální zařízení, které podporuje urychlené síťové služby.  Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což výrazně zlepšuje výkon sítě. Tato cesta s vysokým výkonem obchází hostitele z cesty k datům, snižuje latenci, kolísání a využití procesoru pro použití s nejnáročnějšími síťovými úlohami na podporovaných typech virtuálních počítačů. Akcelerované sítě se podporují na většině účelových a výpočetních instancí s optimálním využitím dvou nebo více vCPU.

## <a name="monitoring-resources"></a>Monitorování prostředků

Každé řešení síťové virtuální zařízení má vlastní nástroje a prostředky pro sledování výkonu jejich síťové virtuální zařízení.  Projděte si dokumentaci ke svým dodavatelům a ujistěte se, že rozumíte omezením výkonu a můžete zjistit, kdy se síťové virtuální zařízení blíží nebo dosahuje kapacity.  Kromě toho se můžete podívat na Azure Monitor Network Insights a zobrazit základní informace o výkonu síťových virtuálních zařízení, jako jsou:

- Využití procesoru
- Síťové vstupy
- Síťové výstupy
- Příchozí toky
- Odchozí toky

## <a name="next-steps"></a>Další kroky

Většina hlavních partnerů síťové virtuální zařízení zveřejnila pokyny týkající se škálování pro náhlé a neočekávaný nárůst během COVID-19. Tady je několik užitečných odkazů na zdroje informací o partnerech.

[Barracuda povolit práci z domova při zabezpečení vašich dat během COVID – 19](https://www.barracuda.com/covid-19/work-from-home "Povolit práci z domova při zabezpečení dat během COVID-19")

[Kontrolní bod zabezpečené vzdálené pracovní síly během coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Zabezpečení vzdálených zaměstnanců během coronavirus")

[Reference k implementaci a výkonu Cisco AnyConnect pro přípravu COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Reference k implementaci a výkonu Cisco AnyConnect pro přípravu COVID-19")

[Citrix COVID – 19 Response Support Center](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID – 19 Response Support Center")

[F5 – pokyny k výraznému nárůstu vzdálených pracovních procesů](https://www.f5.com/business-continuity "F5 – pokyny k výraznému nárůstu vzdálených pracovních procesů")

[Fortinet aktualizace COVID-19 pro zákazníky a partnery](https://www.fortinet.com/covid-19.html "COVID – 19 aktualizace pro zákazníky a partnery")

[Palo Alto Networks COVID – 19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID – 19 Response Center")
