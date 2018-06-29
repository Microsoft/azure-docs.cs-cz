---
title: Monitorování infrastruktury Azure
description: Tento článek popisuje, monitorování produkční sítě Azure.
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
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102214"
---
# <a name="monitoring-of-azure-infrastructure"></a>Monitorování infrastruktury Azure   

## <a name="configuration-and-change-management"></a>Konfigurace a správu změn
Microsoft Azure kontroluje a aktualizuje nastavení konfigurace a standardních hodnot konfigurace hardwaru, softwaru a síťová zařízení ročně. Změny vyvinuté, otestovány a schváleny před zadáním produkčního prostředí z vývoj nebo testovací prostředí.

Standardní hodnoty konfigurace požadované pro služeb založených na Azure je zkontrolovat tým zabezpečení Azure a dodržování předpisů a týmy služby. Zkontrolujte týmu service je součástí testování před nasazením služby jejich produkční.

## <a name="vulnerability-management"></a>Ohrožení zabezpečení správy
Správa aktualizací zabezpečení pomáhá chránit systémy ze známých slabých míst. Systémy integrované nasazení Azure používá ke správě distribuce a instalaci aktualizací zabezpečení softwaru společnosti Microsoft. Azure je také možné využívat zdroje z Microsoft Security Response Center (MSRC). Střediska MSRC identifikuje, sleduje, odpoví na a vyřeší incidenty zabezpečení a cloudové ohrožení zabezpečení po celý den, každý den v roce.

## <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení
Zjišťování ohrožení zabezpečení se provádí na serverové operační systémy, databáze a síťových zařízení. Čtvrtletně minimálně jsou prováděny prověřování ohrožení zabezpečení. Kontrakty Microsoft Azure s nezávislí posuzovatelé provést průnikům testování hranice Microsoft Azure. Cvičení Red týmu se také pravidelně provádí a výsledky používat k vylepšení zabezpečení.

## <a name="protective-monitoring"></a>Ochranné monitorování
Požadavky pro aktivní monitorování má definováno zabezpečení Microsoft Azure. Služba týmy nakonfigurovat active nástroje monitorování v souladu s těmito požadavky. Aktivní monitorování nástroje patří sledování agenta (MA) a System Center Operations Manager. Tyto nástroje jsou nakonfigurované na poskytování čas výstrahy zabezpečení Microsoft Azure osobám v situacích, které vyžadují okamžitou akci.

## <a name="incident-management"></a>Správa incidentů
Microsoft implementuje proces správy incidentů zabezpečení pro usnadnění koordinovanou reakci na incidenty, musí dojít k některým.

Zjistí-li Microsoft neoprávněného přístupu k zákaznická data uložená na jeho zařízení nebo v jeho zařízení nebo neoprávněného přístupu k těchto zařízení nebo zařízení, což vede ke ztrátě, zpřístupnění nebo změnou dat zákazníka, společnost Microsoft má následující akce:

- Neprodleně upozorní zákazník bezpečnostního incidentu
- Rychle prověří incidentu zabezpečení a poskytuje podrobné informace o incidentu zabezpečení zákazníka
- Trvá přiměřené a vyzvat postup zmírněna rizika a minimalizovat škod vzniklých v důsledku incidentu zabezpečení.

Představuje rozhraní správy incidentů bylo vytvořeno s definovány role a odpovědnosti přidělené. Tým služby Windows Azure Security Incident správy (WASIM) zodpovídá za správu incidenty zabezpečení, včetně Eskalace a zajištění zapojení specialisty týmy, pokud je to nezbytné. Azure vedoucí provozu, kteří jsou zodpovědní za dohled nad vyšetřování a řešení incidentů zabezpečení a ochrana osobních údajů.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)
