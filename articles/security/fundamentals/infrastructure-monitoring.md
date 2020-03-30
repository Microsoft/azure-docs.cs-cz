---
title: Monitorování infrastruktury Azure
description: Tento článek popisuje monitorování produkční sítě Azure.
services: security
documentationcenter: na
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: af9c157b4644156edc6dcdb1b53c141263576500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727157"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorování infrastruktury Azure   

## <a name="configuration-and-change-management"></a>Správa konfigurace a změn
Azure kontroluje a aktualizuje nastavení konfigurace a základní konfigurace hardwaru, softwaru a síťových zařízení ročně. Změny jsou vyvíjeny, testovány a schváleny před vstupem do produkčního prostředí z vývojového nebo testovacího prostředí.

Konfigurace podle směrného plánu, které jsou vyžadovány pro služby založené na Azure, jsou kontrolovány týmem azure security and compliance team a týmy služeb. Kontrola týmu služeb je součástí testování, ke kterému dochází před nasazením jejich produkční služby.

## <a name="vulnerability-management"></a>Správa zranitelnosti
Správa aktualizací zabezpečení pomáhá chránit systémy před známými chybami zabezpečení. Azure používá integrované systémy nasazení ke správě distribuce a instalace aktualizací zabezpečení pro software Microsoftu. Azure je také možné čerpat z prostředků Microsoft Security Response Center (MSRC). MSRC identifikuje, monitoruje, reaguje a řeší bezpečnostní incidenty a cloudové chyby zabezpečení nepřetržitě, každý den v roce.

## <a name="vulnerability-scanning"></a>Prohledávání chyb zabezpečení
Prohledávání chyb zabezpečení se provádí na operačních systémech, databázích a síťových zařízeních. Kontrola zranitelnosti se provádí minimálně čtvrtletně. Azure smlouvy s nezávislými posuzovatelé provádět penetrační testování hranice Azure. Cvičení červeného týmu jsou také běžně prováděny a výsledky se používají k vylepšení zabezpečení.

## <a name="protective-monitoring"></a>Ochranné monitorování
Azure security má definované požadavky na aktivní monitorování. Servisní týmy konfigurují aktivní monitorovací nástroje v souladu s těmito požadavky. Mezi aktivní nástroje pro monitorování patří Microsoft Monitoring Agent (MMA) a System Center Operations Manager. Tyto nástroje jsou nakonfigurované tak, aby poskytovaly časové výstrahy pracovníkům zabezpečení Azure v situacích, které vyžadují okamžitou akci.

## <a name="incident-management"></a>Správa incidentů
Společnost Microsoft implementuje proces správy incidentů zabezpečení, aby usnadnila koordinovanou reakci na incidenty, pokud by k nim došlo.

Pokud se společnost Microsoft dozví o neoprávněném přístupu k zákaznickým datům uloženým v jejím zařízení nebo v jejích zařízeních nebo se dozví o neoprávněném přístupu k těmto zařízením nebo zařízením, což má za následek ztrátu, zveřejnění nebo změnu zákaznických dat, Společnost Microsoft provádí následující akce:

- Neprodleně upozorní zákazníka na bezpečnostní incident.
- Okamžitě vyšetří incident zabezpečení a poskytne zákazníkům podrobné informace o incidentu zabezpečení.
- Provede přiměřené a rychlé kroky ke zmírnění dopadů a minimalizaci škod způsobených bezpečnostním incidentem.

Byl vytvořen rámec pro správu incidentů, který definuje role a přiděluje odpovědnosti. Tým pro správu bezpečnostních incidentů Azure je zodpovědný za správu bezpečnostních incidentů, včetně eskalace, a zajištění zapojení specializovaných týmů v případě potřeby. Provozní manažeři Azure jsou zodpovědní za dohled nad vyšetřováním a řešením incidentů se zabezpečením a ochranou osobních údajů.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
