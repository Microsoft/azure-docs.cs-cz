---
title: Monitorování infrastruktury Azure
description: Tento článek popisuje monitorování produkční sítě Azure.
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
ms.openlocfilehash: 59f54487d89aee199e35e741ac4683d4784818a0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172479"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorování infrastruktury Azure   

## <a name="configuration-and-change-management"></a>Konfiguraci a správu změn
Azure kontroluje a aktualizuje nastavení konfigurace a standardních hodnot konfigurace hardwaru, softwaru a síťová zařízení pořizují na rok. Změny jsou vyvíjeny, testovat a schválení před vstupem z vývojové a testovací prostředí v provozním prostředí.

Standardní hodnoty konfigurace, které jsou požadovány pro služby založené na Azure jsou kontrolovány týmem Azure zabezpečení a dodržování předpisů a týmy služeb. Přehled týmu služeb je součástí testování, který se nachází před nasazením služby v produkčním prostředí.

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení
Správa aktualizací zabezpečení pomáhá chránit systémy před známými chybami zabezpečení. Azure používá integrované nasazovací systémy zvládají distribuci a instalaci aktualizací zabezpečení softwaru společnosti Microsoft. Azure je také možnost využívat prostředky z Microsoft Security Response Center (MSRC). MSRC identifikuje, monitoruje, odpovídá a řeší bezpečnostní incidenty a cloud neustále a zobrazuje ohrožení zabezpečení, každý den v roce.

## <a name="vulnerability-scanning"></a>Zjišťování ohrožení zabezpečení
Zjišťování ohrožení zabezpečení se provádí na serverové operační systémy, databáze a síťová zařízení. Prověřování ohrožení zabezpečení jsou prováděny čtvrtletně na minimum. Azure smluv s nezávislými posuzovateli provádět testování průniku hranice Azure. Cvičení týmu se také pravidelně provádí a výsledky se používají k vylepšování zabezpečení.

## <a name="protective-monitoring"></a>Ochranná monitorování
Zabezpečení Azure je definován požadavky při monitorování aktivní. Týmy služeb konfigurace aktivní nástrojů pro monitorování v souladu s těmito požadavky. Aktivní monitorování nástroje patří Microsoft Monitoring Agent (MMA) a System Center Operations Manager. Tyto nástroje umožňují poskytovat čas upozornění k zabezpečení Azure pracovníky v situacích, které vyžadují okamžitou akci.

## <a name="incident-management"></a>Správa incidentů
Microsoft implementuje proces správy incidentů zabezpečení pro usnadnění koordinovanou reakci na incidenty, musí dojít k některým.

Pokud Microsoft obdrží informace o neoprávněný přístup k zákaznickým datům, která je uložena na jeho zařízení nebo v jeho zařízení, nebo se dozví o neoprávněný přístup do těchto zařízení nebo zařízení, což vede ke ztrátě, zpřístupnění nebo změně zákaznických dat, Microsoft provede následující akce:

- Okamžitě upozorní zákazníky incidentu zabezpečení.
- Rychle prověří incidentu zabezpečení a poskytuje zákazníkům podrobné informace o incidentu zabezpečení.
- Přijímá přiměřené a rychlé kroky pro zmírněna rizika a minimalizovali škody vzniklé incidentu zabezpečení.

Vytvořilo se rozšiřovatelnou platformu pro správu incidentů, který definuje role a zodpovědnosti přiděluje. Tým správy incidentů zabezpečení Azure je zodpovědný za Správa incidentů zabezpečení, včetně Eskalace a zajišťuje zapojení specializované týmy, pokud je to nezbytné. Vedoucí provozní technici Azure zodpovídají za dohled vyšetření a řešení incidentů zabezpečení a ochrana osobních údajů.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)
