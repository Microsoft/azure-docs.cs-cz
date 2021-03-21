---
title: Správa produkční sítě Azure – Microsoft Azure
description: Tento článek popisuje, jak Microsoft spravuje a provozuje produkční síť Azure za účelem zabezpečení datových center Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727113"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Správa a provoz produkční sítě Azure    
Tento článek popisuje, jak Microsoft spravuje a provozuje produkční síť Azure za účelem zabezpečení datových center Azure.

## <a name="monitor-log-and-report"></a>Monitorování, protokolování a sestava

Správa a provoz produkční sítě Azure je koordinovaným úsilím mezi provozními týmy Azure a Azure SQL Database. Týmy využívají v prostředí několik nástrojů pro monitorování výkonu systému a aplikací. A používají vhodné nástroje pro monitorování síťových zařízení, serverů, služeb a procesů aplikací.

Aby bylo zajištěno zabezpečené spouštění služeb spuštěných v prostředí Azure, implementují provozní týmy více úrovní monitorování, protokolování a vytváření sestav, včetně následujících akcí:

- Primárně Microsoft Monitoring Agent (MMA) shromažďuje informace o monitorování a diagnostickém protokolu z mnoha míst, včetně řadiče prostředků infrastruktury (FC) a kořenového operačního systému (OS) a zapisuje je do souborů protokolu. Agent nakonec vloží vydanou podmnožinu informací do předem nakonfigurovaného účtu úložiště Azure. Kromě toho se na volně sledující a diagnostické služby načtou různá data protokolu monitorování a diagnostiky a shrnují informace. Služba monitorování a diagnostiky zapisuje informace do integrovaného protokolu. Azure používá vlastní monitorování zabezpečení Azure, které je rozšířením systému monitorování Azure. Obsahuje komponenty, které sledují, analyzují a oznamují události související se zabezpečením z různých bodů na platformě.

- Platforma Azure SQL Database Windows Fabric poskytuje pro Azure SQL Database správu, nasazení, vývoj a provozní služby pro dohled nad nimi. Platforma nabízí distribuované, služby pro více kroků nasazení, monitorování stavu, automatické opravy a dodržování předpisů pro verze služby. Poskytuje následující služby:

   - Možnosti modelování služeb s vysoce věrným vývojovým prostředím (clustery Datacenter jsou nákladné a omezených).
   - Pracovní postupy pro nasazení a údržbu spouštěné jedním kliknutím a aktualizace služby.
   - Vytváření sestav o stavu s automatizovanými pracovními postupy pro zajištění automatických oprav.
   - Monitorování, upozorňování a ladění v reálném čase napříč uzly distribuovaného systému.
   - Centralizované shromažďování provozních dat a metrik pro analýzu distribuovaných hlavních příčin a Přehled služeb.
   - Provozní nástroje pro nasazení, správu změn a monitorování.
   - Azure SQL Database Windows Fabric platforma a sledovacích zařízení se průběžně spouští a monitorují v reálném čase.

Pokud dojde k jakýmkoli anomáliím, aktivuje se proces reakce na incident následovaný týmem pro třídění incidentů Azure. Příslušné pracovníky podpory Azure budou upozorněni na reakci na incident. Sledování a řešení problémů jsou zdokumentovány a spravovány v centralizovaném systému lístků. Metriky provozu systému jsou k dispozici v rámci smlouvy o utajení (NDA) a na vyžádání.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Podniková síť a Multi-Factor Access k produkci
Uživatelská základna podnikové sítě zahrnuje pracovníky podpory Azure. Podniková síť podporuje interní podnikové funkce a zahrnuje přístup k interním aplikacím, které se používají pro zákaznické podpory Azure. Podniková síť je logicky i fyzicky oddělená od produkční sítě Azure. Pracovníci Azure získávají přístup k podnikové síti pomocí pracovních stanic Azure a notebooků. Všichni uživatelé musí mít účet Azure Active Directory (Azure AD), včetně uživatelského jména a hesla, pro přístup k podnikovým síťovým prostředkům. Přístup k podnikové síti používá účty Azure AD, které jsou vydávány všem pracovníkům, dodavatelům a dodavatelům společnosti Microsoft a spravuje je společnost Microsoft Information Technology. Jedinečné identifikátory uživatelů odlišují zaměstnance na základě jejich stavu zaměstnanosti v Microsoftu.

Přístup k interním aplikacím Azure se řídí ověřováním pomocí Active Directory Federation Services (AD FS) (AD FS). AD FS je služba hostovaná technologií Microsoft Information Technology, která poskytuje ověřování uživatelů podnikové sítě prostřednictvím použití zabezpečeného tokenu a deklarací identity uživatelů. AD FS umožňuje interním aplikacím Azure ověřovat uživatele před podnikovou doménou Microsoft Active Directory. Chcete-li získat přístup k produkční síti z podnikového síťového prostředí, musí se uživatelé ověřit pomocí služby Multi-Factor Authentication.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
