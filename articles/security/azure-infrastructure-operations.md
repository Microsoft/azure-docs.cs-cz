---
title: Azure produkční operace a Správa
description: Tento článek obsahuje obecné popis sady pro správu a operace produkční sítě Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102176"
---
# <a name="azure-production-operations-and-management"></a>Azure produkční operace a Správa    
Správa a provoz Azure produkční sítě je koordinovaný proces mezi týmy operace ve službě Azure a Azure SQL Database. Několik systému a aplikací nástroje pro sledování výkonu se používají v prostředí. Síťová zařízení, serverů, služeb a procesů aplikace jsou monitorovány pomocí vhodných nástrojů.

Více úrovní sledování, protokolování a generování sestav jsou implementované k zajištění zabezpečeného spuštění služby spuštěné v prostředí Microsoft Azure, včetně následujících akcí:

- Především se stává Microsoft Azure Monitoring Agent (MA) shromáždí monitorovací a diagnostické informace o protokolu z mnoha místech, včetně FC a kořenu operačního systému a zapíše ho do souborů protokolu. Nakonec vynutí Natrávený podmnožinu informace do předem nakonfigurovaný účet úložiště Azure. Kromě toho monitorovací a diagnostické služby MDS () je samostatně stojící služba, která čte různých monitorování a diagnostických dat protokolu a shrnuje informace. MDS zapíše informace do protokolu integrované. Azure používá uživatelské zabezpečení monitorování ASM (Azure), což je rozšíření k monitorování systému Azure. Obsahuje součásti, které sledovat a analyzovat, sestavy zabezpečení příslušné události z různých bodů v platformě.
- Platforma Microsoft Azure SQL Database WinFabric poskytuje správu, nasazení, vývoj a provozní dohledu služby pro Microsoft Azure SQL Database. Nabízí služby pro distribuované, několika kroky nasazení, monitorování stavu, automatické oprav a verze služby dodržování předpisů. Poskytuje tyto služby:

   - Služba modelování možnosti se zachováním vývojové prostředí (datacenter clustery je nákladné a omezených).
   - Jedním kliknutím nasazení a upgrade pracovní postupy pro služby bootstrap a údržby.
   - Stav vytváření sestav s pracovními postupy automatizované opravy povolit self-healing.
   - Monitorování, výstrahy a ladění zařízení napříč uzly distribuovaného systému reálném čase.
   - Centralizované shromažďování provozních dat a metriky pro distribuované kořenové způsobit přehledy analýzy a služby.
   - Provozní nástrojů pro nasazení, monitorování a správa změn.
   - Microsoft Azure SQL Database WinFabric platformy a sledovací zařízení skripty spouštět nepřetržitě a sledování v reálném čase.

Pokud dojde k anomálie, je aktivován proces reakcí na incidenty následuje týmem třídění Incident Azure. Pracovníci podpory odpovídající Azure je informován o reagovat na incident. Problém, sledování a řešení jsou zdokumentované a spravovat centralizované systém lístků podpory. Metrika doba provozu systému jsou k dispozici v rámci smlouvy bez zpřístupnění (NDA) a na vyžádání.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Podnikové síti a multi-Factor přístup do produkčního prostředí
Podnikové sítě uživatelské základny zahrnuje pracovníky technické podpory Microsoft Azure. V podnikové síti podporuje interní podnikové funkce a zahrnuje přístup k interní aplikace, které se používají pro Azure zákaznickou podporu. V podnikové síti je logicky i fyzicky oddělená od Azure produkční sítě. Microsoft Azure pracovníky přístup k podnikové síti pomocí Microsoft Azure pracovní stanice a přenosné počítače. Všichni uživatelé musí mít účet služby Active Directory (AD), včetně uživatelského jména a hesla pro přístup k podnikovým síťovým prostředkům. Přístup k podnikové síti používá účty AD, které jsou vydané pracovníky společnosti Microsoft, dodavatelů, Dodavatelé a spravuje MSIT. Identifikátory jedinečná uživatelská rozlišit pracovníky na základě jejich zaměstnání stavu ve společnosti Microsoft.

Přístup k interní aplikace Azure je řízen pomocí ověřování s Active Directory Federation Services (ADFS). Služba AD FS je služba hostovaná společností MSIT, který poskytuje ověřování uživatelů CorpNet prostřednictvím použití zabezpečeného token a uživatelské deklarace identity. Služba AD FS umožňuje interní aplikace Microsoft Azure k ověření uživatele na základě AD domény společnosti Microsoft. Pro přístup k produkční síti z prostředí CorpNet, uživatel musí provést ověření pomocí služby Multi-Factor authentication.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)
