---
title: Dostupnost infrastruktury Azure
description: Článek popisuje úrovně redundance k dosažení maximální dostupnosti dat zákazníků.
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
ms.openlocfilehash: db13e4835e483b4738074a71861737c4851d8dbc
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102164"
---
# <a name="availability-of-azure-infrastructure"></a>Dostupnost infrastruktury Azure
Azure poskytuje robustní dostupnost podle rozsáhlé redundance dosáhnout pomocí technologie virtualizace. Azure poskytuje mnoho úrovní redundance k dosažení maximální dostupnosti dat zákazníků.

## <a name="temporary-outages-and-natural-disaster"></a>Dočasných výpadků a přírodní havárie
Tým Microsoft cloudové infrastruktury a operací návrhy, sestavení, funguje a zabezpečuje infrastruktury cloudu. Tento tým zajistí, že infrastruktura Azure doručování vysokou dostupnost a spolehlivost, vysoce účinné, inteligentní škálovatelnost a cloudové zabezpečení, privátní a důvěryhodné.

Nepřerušitelný zdroj napájení a valná bankami baterie zajistit, aby zůstal průběžné v případě krátkodobé přerušení spotřeby elektrické energie.

Nouzové generátory umožňují zálohování napájení delším výpadkům a plánované údržby. Datového centra je provozována s dohlížející na bezpečný paliva rezervy případě přírodní havárie.

Vysokorychlostní a robustní fiber optického sítě připojení datových centrech s jinými hlavní centra a uživatelů Internetu. Výpočetní uzly blíže zatížení hostitele koncovým uživatelům ke snížení latence, zadejte geografická redundance a zvýšit celkový služby odolnost proti chybám. Tým technici pracuje po celý den na Ujistěte se, že služby jsou trvale dostupné pro zákazníky.

Microsoft zajišťuje vysokou dostupnost prostřednictvím pokročilé monitorování a reakce na incidenty, podporu služby a zálohujte schopnosti převzetí služeb při selhání. Geograficky rozptýlené centrech operations Microsoft fungovat 24/7/365. Síť Azure patří mezi největší na světě. Fiber optického a obsahu distribuční sítě připojí datovými centry a uzly okraj zajistit vysoký výkon a spolehlivost.

## <a name="disaster-recovery"></a>Zotavení po havárii
Azure držela zákaznická data odolná ve dvou umístěních s zákazníka, které má schopnost zvolte umístění zálohy lokality. V obou umístěních Azure neustále udržuje více (3) v pořádku repliky dat zákazníka.

## <a name="database-availability"></a>Databáze dostupnosti
Azure zajišťuje, že databáze přístup prostřednictvím brány sítě Internet s dlouhodobě databáze dostupnosti z Internetu. Monitorování vyhodnocuje stav a stav aktivní databází na 5 minut časové intervaly.

## <a name="storage-availability"></a>Dostupnost úložiště
Azure nabízí úložiště prostřednictvím služby vysoce škálovatelného a odolná úložiště, které poskytuje připojení koncové body, díky kterému jej být přímo přístupné spotřebitelskou aplikací. Pomocí služby úložiště bude příchozí požadavky na úložiště efektivně zpracování s transakcí integrity.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)
