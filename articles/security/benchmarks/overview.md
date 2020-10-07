---
title: Přehled služby Azure Security test benchmark v2
description: Přehled služby Azure Security test test v2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6628d693a8df3614097e23785ac234a451565ca3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777101"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Přehled srovnávacích testů zabezpečení Azure (v2)

Srovnávací test zabezpečení Azure (dokumentem SSAE) poskytuje doporučené osvědčené postupy a doporučení, které vám pomůžou zlepšit zabezpečení zatížení, dat a služeb v Azure.

Tento srovnávací test je součástí sady holistických pokynů pro zabezpečení, které také zahrnují:

- **Rozhraní pro přijetí do cloudu** – doprovodné materiály k zabezpečení, včetně [strategie](/azure/cloud-adoption-framework/strategy/define-security-strategy), [rolí a odpovědností](/azure/cloud-adoption-framework/organize/cloud-security), [osvědčených postupů pro Azure Top 10](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)a [referenční implementace](/azure/cloud-adoption-framework/ready/enterprise-scale/).
- **Architekturu Azure ve správném prostředí** – pokyny k [zabezpečení vašich úloh](https://docs.microsoft.com/assessments/?mode=pre-assessment&session=local) v Azure.
- **Osvědčené postupy zabezpečení od Microsoftu** – [doporučení](/security/compass/microsoft-security-compass-introduction) s příklady v Azure.

 Srovnávací test zabezpečení Azure se zaměřuje na oblasti řízení zaměřené na Cloud. Tyto ovládací prvky jsou konzistentní s dobře známými srovnávacími testy zabezpečení, jako jsou ty, které jsou popsány v centru pro řízení internetového zabezpečení (CIS) verze 7,1 a National Institute of Standards and Technology (NIST) SP800-53.
Srovnávací testy zabezpečení Azure obsahují následující ovládací prvky:

| DOKUMENTEM SSAE – domény ovládacích prvků | Popis 
|--|--|
| [&nbsp;Zabezpečení sítě &nbsp; (NS)](security-controls-v2-network-security.md) | Zabezpečení sítě pokrývá ovládací prvky pro zabezpečení a ochranu sítí Azure, včetně zabezpečení virtuálních sítí, vytváření privátních připojení, prevenci a zmírnění externích útoků a zabezpečení DNS. |
| [&nbsp;Správa identit &nbsp; (IM)](security-controls-v2-identity-management.md) | Správa identit pokrývá ovládací prvky pro vytvoření zabezpečeného řízení identity a přístupu pomocí Azure Active Directory, včetně použití jednotného přihlašování, silného ověřování, spravovaných identit (a principů služeb) pro aplikace, podmíněný přístup a monitorování anomálií. |
| [Privilegovaný &nbsp; přístup &nbsp; (PA)](security-controls-v2-privileged-access.md) | Privilegovaný přístup pokrývá ovládací prvky pro ochranu privilegovaného přístupu k vašemu tenantovi a prostředkům Azure, včetně řady ovládacích prvků pro ochranu administrativního modelu, účtů pro správu a pracovních stanic s privilegovaným přístupem proti úmyslnému a neúmyslnému riziku. |
| [&nbsp;Ochrana dat &nbsp; (DP)](security-controls-v2-data-protection.md) | Ochrana dat pokrývá kontrolu ochrany dat v klidovém provozu, při přenosu a prostřednictvím autorizovaných mechanismů přístupu, včetně zjišťování, klasifikace, ochrany a monitorování citlivých datových prostředků pomocí řízení přístupu, šifrování a protokolování v Azure. |
| [&nbsp;Správa prostředků &nbsp; (am)](security-controls-v2-asset-management.md) | Správa prostředků pokrývá ovládací prvky pro zajištění viditelnosti zabezpečení a zásad správného řízení pro prostředky Azure, včetně doporučení týkajících se zabezpečení, přístupu k inventáři prostředků a správě schválení pro služby a prostředky (inventarizace, sledování a opravy). |
| [Protokolování &nbsp; a &nbsp; &nbsp; detekce hrozeb (lt)](security-controls-v2-logging-threat-detection.md) | Protokolování a detekce hrozeb pokrývá ovládací prvky pro detekci hrozeb v Azure a povolování, shromažďování a ukládání protokolů auditu pro služby Azure, včetně povolování zjišťování, vyšetřování a nápravných procesů s ovládacími prvky pro generování vysoce kvalitních výstrah s detekcí nativních hrozeb ve službách Azure. zahrnuje také shromažďování protokolů s Azure Monitor, centralizaci analýzy zabezpečení s použitím Sentinel Azure, synchronizaci času a uchovávání protokolů. |
| [Reakce na incidenty &nbsp; &nbsp; (IR)](security-controls-v2-incident-response.md) | Reakce na incidenty pokrývá ovládací prvky v životním cyklu reakce na incidenty – přípravu, zjišťování a analýzu, zahrnutí a následné aktivity, včetně použití služeb Azure, jako jsou Azure Security Center a Sentinel, k automatizaci procesu reakce na incidenty. |
| [Stav &nbsp; a &nbsp; Správa ohrožení zabezpečení &nbsp; &nbsp; (PV)](security-controls-v2-posture-vulnerability-management.md) | Stav a Správa ohrožení zabezpečení se zaměřují na ovládací prvky pro hodnocení a zlepšení stav zabezpečení Azure, včetně kontroly ohrožení zabezpečení, testování průniku a nápravy, jakož i sledování konfigurace zabezpečení, vytváření sestav a opravy v prostředcích Azure. |
| [Zabezpečení koncového bodu &nbsp; &nbsp; (ES)](security-controls-v2-endpoint-security.md) | Služba Endpoint Security pokrývá ovládací prvky zjišťování a odezvy koncových bodů, včetně použití zjišťování koncových bodů a odezvy (EDR) a služby anti-malware pro koncové body v prostředích Azure. |
| [Zálohování &nbsp; a &nbsp; obnovení &nbsp; (BR)](security-controls-v2-backup-recovery.md) | Zálohování a obnovení pokrývá ovládací prvky, aby se zajistilo, že se budou provádět, ověřovat a chránit data a konfigurace v různých úrovních služeb. |
| [Řízení &nbsp; a &nbsp; strategie &nbsp; (GS)](security-controls-v2-governance-strategy.md) | Zásady správného řízení a strategie poskytují pokyny k zajištění soudržné strategie zabezpečení a dokumentovaného přístupu pro řízení a zajištění zabezpečení, včetně stanovení rolí a odpovědností pro různé funkce cloudového zabezpečení, jednotnou technickou strategii a podpůrné zásady a standardy. |

## <a name="azure-security-benchmark-recommendations"></a>Doporučení pro srovnávací testy zabezpečení Azure

Každé doporučení zahrnuje následující informace:

- **Azure ID**: ID srovnávacího testu zabezpečení Azure, které odpovídá doporučení.
- **ID ovládacích prvků CIS v 7.1**: ovládací prvky CIS v 7.1, které odpovídají tomuto doporučení.
- **NIST SP800-53 R4 ID**: ovládací prvky NIST SP800-53 R4 (mírný), které odpovídají tomuto doporučení.
- **Podrobnosti**: odůvodnění doporučení a odkazy na pokyny k jeho implementaci. Pokud Azure Security Center doporučení podporuje, budou tyto informace také uvedené.
- **Zodpovědnost**: Určuje, zda je pro implementaci tohoto doporučení zodpovědný zákazník, poskytovatel služeb nebo obojí. Bezpečnostní odpovědnost se sdílí ve veřejném cloudu. Některé ovládací prvky zabezpečení jsou dostupné jenom poskytovateli cloudové služby, a proto je poskytovatel zodpovědný za jejich adresování. Jedná se o obecná pozorování – pro některé jednotlivé služby se bude odpovědnost lišit od toho, co je uvedeno v srovnávacím testu zabezpečení Azure. Tyto rozdíly jsou popsané v základních doporučeních pro jednotlivé služby.
- **Účastníci zabezpečení zákazníka**: [bezpečnostní funkce](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) v organizaci zákazníka, které mohou být pro příslušný ovládací prvek vyúčtovány, zodpovědné nebo konzultované. Může se lišit od organizace až po organizaci v závislosti na struktuře organizace zabezpečení vaší společnosti a rolích a zodpovědností, které jste si nastavili v souvislosti se zabezpečením Azure.

> [!NOTE]
> Mapování ovládacích prvků mezi dokumentem SSAE a oborovými srovnávacími testy (jako je NIST a SNS) znamená pouze to, že konkrétní funkce Azure lze použít k plnému nebo částečnému vyřešení požadavku na řízení definovaného v NIST nebo CIS. Je třeba si uvědomit, že taková implementace nemusí nutně překládat na celé dodržování předpisů odpovídajícím ovládacím prvkům v CIS nebo NIST.

Uvítáme vaši podrobnou zpětnou vazbu a aktivní účast v úsilí Azure Security test. Pokud chcete zadat přímý vstup týmu Azure Security test, vyplňte formulář na https://aka.ms/AzSecBenchmark

## <a name="download"></a>Stáhnout

Srovnávací test zabezpečení Azure si můžete stáhnout ve [formátu tabulky](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark).

## <a name="next-steps"></a>Další kroky 
- Podívejte se na první ovládací prvek zabezpečení: [zabezpečení sítě](security-control-network-security.md)
- Přečtěte si [Úvod k zabezpečení Azure Security test](introduction.md)
- Seznamte se se [Základy zabezpečení Azure](../fundamentals/index.yml)
