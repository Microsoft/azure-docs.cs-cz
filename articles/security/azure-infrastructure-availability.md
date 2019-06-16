---
title: Dostupnost infrastruktury Azure – zabezpečení Azure
description: Tento článek obsahuje informace o co společnost Microsoft k zabezpečení infrastruktury Azure a zajištění maximální dostupnosti dat zákazníků.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: d13c79152afe00cb326f69b523e9fd7f0c3550b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872610"
---
# <a name="azure-infrastructure-availability"></a>Dostupnost infrastruktury Azure
Tento článek obsahuje informace o co společnost Microsoft k zabezpečení infrastruktury Azure a zajištění maximální dostupnosti dat zákazníků. Azure poskytuje robustní dostupnosti podle rozsáhlé redundance dosáhnout pomocí technologie virtualizace.

## <a name="temporary-outages-and-natural-disaster"></a>Dočasných výpadků a přírodní katastrofě
Cloudová infrastruktura Microsoftu a operace tým navrhuje, sestavení, funguje a zlepšuje zabezpečení cloudové infrastruktury. Tento tým zajišťuje, že vysokou dostupnost a spolehlivost, vysoké účinnosti a inteligentní škálovatelnost doručování infrastruktury Azure. Tým poskytuje zabezpečené, privátní a důvěryhodného cloudu.

Nepřerušitelný zdroj napájení a rozsáhlé bank baterie zajistit, aby zůstal elektřiny průběžné v případě krátkodobé přerušení napájení. Nouzový generátorů poskytují výkon zálohování pro delším výpadkům a plánované údržby. Pokud dojde k přírodní katastrofě, můžete použít datového centra rezervy posílit místní.

Optických sítí fiber robustní a vysokorychlostní připojení k datacentrům s jinými hlavní rozbočovače a uživatelé Internetu. Výpočetní uzly hostovat úlohy blíž uživatelům snížit latenci, zadejte geografickou redundancí a zvýšení celkové služby odolnost proti chybám. Tým techniků funguje neustále a zobrazuje zajistit, že služby jsou trvale dostupné.

Microsoft zajišťuje vysokou dostupnost prostřednictvím rozšířené monitorování a reakce na incidenty, podpora služby a možnosti zálohování převzetí služeb při selhání. Geograficky distribuované střediska společnosti Microsoft pro operace pracovat 24/7/365. Síť Azure je jedním z největší na světě. Síť distribuce obsahu a optického vlákna se připojuje datových center a hraničním uzlům zajistit vysoký výkon a spolehlivost.

## <a name="disaster-recovery"></a>Zotavení po havárii
Azure sleduje vaše data trvalý ve dvou umístěních. Můžete zvolit umístění zálohy lokality. V obou umístěních Azure stále udržuje tří replik vašich dat v pořádku.

## <a name="database-availability"></a>Databáze dostupnosti
Azure tak zajišťuje jistotu, že databáze je prostřednictvím Internetu brány s zachovaného databáze dostupnosti přístup k Internetu. Monitorování vyhodnocuje stav a stav aktivní databáze v pětiminutovém časových intervalech.

## <a name="storage-availability"></a>Dostupnost úložiště
Azure poskytuje úložiště prostřednictvím služby vysoce škálovatelného a odolného úložiště, která poskytuje koncové body připojení. To znamená, že aplikace může přímo přístup ke službě úložiště. Službu storage zpracovává příchozích požadavků na úložiště efektivně, s transakční integrity.

## <a name="next-steps"></a>Další postup
Další informace o co společnost Microsoft k zajištění infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)
