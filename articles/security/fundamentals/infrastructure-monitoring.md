---
title: Monitorování infrastruktury Azure
description: Přečtěte si o aspektech monitorování infrastruktury produkční sítě Azure, jako je například kontrola ohrožení zabezpečení.
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
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87542938"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorování infrastruktury Azure   

## <a name="configuration-and-change-management"></a>Konfigurace a Správa změn
Azure kontroluje a aktualizuje konfigurační nastavení a základní konfigurace hardwaru, softwaru a síťových zařízení. Změny se vypracovávají, testují a schvalují před vstupem do produkčního prostředí z vývojového nebo testovacího prostředí.

Základní konfigurace, které jsou nutné pro služby založené na Azure, jsou přezkoumány týmem zabezpečení a dodržování předpisů Azure a týmy služby. Kontrola týmu služby je součástí testování, ke kterému dochází před nasazením provozní služby.

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení
Správa aktualizací zabezpečení pomáhá chránit systémy před známými chybami zabezpečení. Azure používá integrované systémy nasazení ke správě distribuce a instalace aktualizací zabezpečení pro software Microsoftu. Azure je také možné nakreslit na prostředky centra Microsoft Security Response Center (MSRC). MSRC identifikuje, monitoruje, reaguje na a řeší incidenty zabezpečení a zranitelnosti cloudu v průběhu každého dne v roce.

## <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení
Kontrola ohrožení zabezpečení se provádí na serverových operačních systémech, databázích a síťových zařízeních. Kontroly ohrožení zabezpečení se provádějí čtvrtletně na základě minima. Smlouvy Azure s nezávislými hodnotiteli k provádění testování průniku na hranici Azure. Také se provádí rutiny červeného a vykonávání výsledků, které se používají k vylepšování zabezpečení.

## <a name="protective-monitoring"></a>Monitorování ochrany
Zabezpečení Azure má definované požadavky na aktivní monitorování. Týmy služby konfigurují aktivní nástroje monitorování v souladu s těmito požadavky. Mezi aktivní nástroje monitorování patří Microsoft Monitoring Agent (MMA) a System Center Operations Manager. Tyto nástroje jsou nakonfigurované tak, aby poskytovaly časové výstrahy pracovníkům zabezpečení Azure v situacích, které vyžadují okamžitý zásah.

## <a name="incident-management"></a>Správa incidentů
Microsoft implementuje proces správy incidentů zabezpečení, který usnadňuje koordinovaný reakci na incidenty, které by měly nastat.

Pokud se společnost Microsoft dozvěděla o neoprávněném přístupu k zákaznickým datům, která jsou uložená na svém zařízení nebo ve svých zařízeních, nebo se dozví o neoprávněném přístupu k takovému zařízení nebo zařízení, které má za následek ztrátu, zveřejnění nebo změnu zákaznických dat, Microsoft provede tyto akce:

- Okamžitě upozorní zákazníka na incident zabezpečení.
- Vyšetří incident zabezpečení a poskytuje zákazníkům podrobné informace o incidentu zabezpečení.
- Přijímá rozumné a dotazové kroky pro zmírnění dopadů a minimalizaci případné škody vyplývající z incidentu zabezpečení.

Bylo navázáno rozhraní správy incidentů, které definuje role a přiřazuje zodpovědnosti. Tým správy incidentů Azure Security je zodpovědný za správu incidentů zabezpečení, včetně eskalace a zajištění zapojení odborných týmů, pokud je to nutné. Manažeři Azure Operations zodpovídá za dohled nad vyšetřováním a řešením incidentů zabezpečení a ochrany osobních údajů.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
