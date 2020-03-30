---
title: Správa produkční sítě Azure – Microsoft Azure
description: Tento článek popisuje, jak Microsoft spravuje a provozuje produkční síť Azure k zabezpečení datových center Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727113"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Správa a provoz produkční sítě Azure    
Tento článek popisuje, jak Microsoft spravuje a provozuje produkční síť Azure k zabezpečení datových center Azure.

## <a name="monitor-log-and-report"></a>Sledování, protokolování a sestav

Správa a provoz produkční sítě Azure je koordinované úsilí mezi provozními týmy Azure a Azure SQL Database. Týmy používají několik nástrojů pro monitorování výkonu systému a aplikací v prostředí. A používají vhodné nástroje pro sledování síťových zařízení, serverů, služeb a aplikačních procesů.

Aby bylo zajištěno bezpečné spouštění služeb spuštěných v prostředí Azure, operační týmy implementují několik úrovní monitorování, protokolování a vytváření sestav, včetně následujících akcí:

- Primárně Microsoft Monitoring Agent (MMA) shromažďuje informace o monitorování a diagnostické protokol z mnoha míst, včetně řadiče prostředků infrastruktury (FC) a kořenového operačního systému (OS) a zapisuje do souborů protokolu. Agent nakonec odešle restravované podmnožiny informací do předem nakonfigurovaného účtu úložiště Azure. Kromě toho volně stojící monitorovací a diagnostická služba čte různá data protokolu monitorování a diagnostiky a shrnuje informace. Monitorovací a diagnostická služba zapíše informace do integrovaného protokolu. Azure používá vlastní monitorování zabezpečení Azure, což je rozšíření monitorovacího systému Azure. Má komponenty, které sledují, analyzují a hlásí události relevantní pro zabezpečení z různých míst v platformě.

- Platforma Azure SQL Database Windows Fabric poskytuje služby pro správu, nasazení, vývoj a provozní dohled pro Azure SQL Database. Platforma nabízí distribuované služby nasazení ve více krocích, monitorování stavu, automatické opravy a dodržování verzí služeb. Poskytuje následující služby:

   - Možnosti modelování služeb s prostředím pro vývoj s vysokou věrností (clustery datových center jsou nákladné a vzácné).
   - Pracovní postupy nasazení a upgradu jedním kliknutím pro zaváděcí a údržbu služeb.
   - Sestavy stavu s automatizovanými pracovními postupy oprav, které umožňují samoléčení.
   - Monitorování, upozorňování a ladění zařízení v reálném čase napříč uzly distribuovaného systému.
   - Centralizovaná sbírka provozních dat a metrik pro distribuovanou analýzu hlavních příčin a přehled služeb.
   - Provozní nástroje pro nasazení, správu změn a monitorování.
   - Platforma Azure SQL Database Windows Fabric a skripty watchdog běží nepřetržitě a monitorují v reálném čase.

Pokud dojde k anomáliím, aktivuje se proces reakce na incidenty následovaný týmem třídění incidentů Azure. Příslušní pracovníci podpory Azure jsou upozorněni reagovat na incident. Sledování a řešení problémů jsou dokumentovány a spravovány v centralizovaném systému prodeje jízdenek. Metriky dostupnosti systému jsou k dispozici v rámci smlouvy o mlčenlivosti (NDA) a na vyžádání.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Podniková síť a vícefaktorový přístup k výrobě
Uživatelská základna podnikové sítě zahrnuje pracovníky podpory Azure. Podniková síť podporuje interní podnikové funkce a zahrnuje přístup k interním aplikacím, které se používají pro zákaznickou podporu Azure. Podniková síť je logicky i fyzicky oddělená od produkční sítě Azure. Pracovníci Azure přistupují k podnikové síti pomocí pracovních stanic a přenosných počítačů Azure. Všichni uživatelé musí mít účet Azure Active Directory (Azure AD), včetně uživatelského jména a hesla, pro přístup k podnikovým síťovým prostředkům. Přístup k podnikové síti používá účty Azure AD, které jsou vydávány všem zaměstnancům společnosti Microsoft, dodavatelům a dodavatelům a spravovány společností Microsoft Information Technology. Jedinečné identifikátory uživatelů rozlišují pracovníky na základě jejich stavu zaměstnání v Microsoftu.

Přístup k interním aplikacím Azure je řízen prostřednictvím ověřování pomocí služby AD FS (AD FS). Služba AD FS je služba hostovaná společností Microsoft Information Technology, která poskytuje ověřování uživatelů podnikové sítě pomocí zabezpečeného tokenu a uživatelských deklarací. Služba AD FS umožňuje interním aplikacím Azure ověřovat uživatele v doméně firemního adresáře Microsoftu. Chcete-li získat přístup k produkční síti z podnikového síťového prostředí, musí se uživatelé ověřit pomocí vícefaktorového ověřování.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
