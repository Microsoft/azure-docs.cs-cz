---
title: Dostupnost infrastruktury Azure
description: Tento článek popisuje úrovně redundance pro zajištění maximální dostupnosti dat zákazníků.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902573"
---
# <a name="azure-infrastructure-availability"></a>Dostupnost infrastruktury Azure
Azure poskytuje robustní dostupnost podle rozsáhlé redundance dosáhnout pomocí technologie virtualizace. Azure poskytuje mnoho úrovně redundance pro zajištění maximální dostupnosti dat zákazníků.

## <a name="temporary-outages-and-natural-disaster"></a>Dočasných výpadků a přírodní katastrofě
Cloudová infrastruktura Microsoftu a operace tým navrhuje, sestavení, funguje a zajišťuje zabezpečení cloudové infrastruktury. Tento tým zajistí, že infrastrukturu Azure se dosahuje vysoké dostupnosti a spolehlivosti, vysoké účinnosti, inteligentní škálovatelnost a zabezpečení, privátní a důvěryhodného cloudu.

Nepřerušitelný zdroj napájení a rozsáhlé bank baterie zajistit, aby zůstal elektřiny průběžné v případě krátkodobé přerušení napájení.

Nouzový generátorů poskytují výkon zálohování pro delším výpadkům a plánované údržby. Datové centrum je provozována s místním posílit rezervy případě přírodní katastrofě.

Vysokorychlostní a robustní fiber optických sítí připojení k datacentrům s jinými hlavní rozbočovače a uživatelé Internetu. Výpočetní uzly hostovat úlohy blíže koncovým uživatelům snížit latenci, poskytovat geografickou redundanci a zvýšit celkové služby odolnost proti chybám. Tým techniků funguje neustále a zobrazuje zajistit, že služby jsou trvale dostupné pro zákazníky.

Microsoft zajišťuje vysokou dostupnost prostřednictvím rozšířené monitorování a reakce na incidenty, podpora služby a zálohujte funkce převzetí služeb při selhání. Geograficky distribuované střediska společnosti Microsoft pro operace pracovat 24/7/365. Síť Azure je jedním z největší na světě. Síť optického a obsahu distribuční fiber se připojuje, datovými centry a hraničních uzlů zajistit vysoký výkon a spolehlivost.

## <a name="disaster-recovery"></a>Zotavení po havárii
Azure uchová zákaznická data trvalý dvěma umístěními se zákazníkem, máte možnost zvolit umístění zálohy lokality. V obou umístěních Azure udržuje neustále více (3) v dobrém stavu repliky zákaznická data.

## <a name="database-availability"></a>Databáze dostupnosti
Azure tak zajišťuje jistotu, že databáze je prostřednictvím Internetu brány s zachovaného databáze dostupnosti přístup k Internetu. Monitorování vyhodnocuje stav a stav aktivní databáze v 5minutových časových intervalech.

## <a name="storage-availability"></a>Dostupnost úložiště
Azure poskytuje úložiště prostřednictvím služby vysoce škálovatelného a odolného úložiště, které poskytuje připojení koncových bodů, což umožňuje být přímo přístupné spotřebitelskou aplikací. Prostřednictvím služby úložiště bude příchozích požadavků na úložiště efektivně zpracování s transakcí integrity.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrany zákaznických dat v Azure](azure-protection-of-customer-data.md)
