---
title: Dostupnost infrastruktury Azure – zabezpečení Azure
description: Tento článek obsahuje informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure a poskytují maximální dostupnost dat zákazníků.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727217"
---
# <a name="azure-infrastructure-availability"></a>Dostupnost infrastruktury Azure
Tento článek obsahuje informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure a poskytují maximální dostupnost dat zákazníků. Azure poskytuje robustní dostupnost na základě rozsáhlé redundance dosažené pomocí virtualizační technologie.

## <a name="temporary-outages-and-natural-disaster"></a>Dočasné výpadky a přírodní katastrofy
Tým Microsoft Cloud Infrastructure and Operations navrhuje, vytváří, provozuje a zlepšuje zabezpečení cloudové infrastruktury. Tento tým zajišťuje, že infrastruktura Azure poskytuje vysokou dostupnost a spolehlivost, vysokou efektivitu a inteligentní škálovatelnost. Tým poskytuje bezpečnější, privátnější a důvěryhodnější cloud.

Nepřerušitelné zdroje napájení a obrovské banky baterií zajišťují, že elektřina zůstane nepřetržitá, pokud dojde ke krátkodobému výpadku napájení. Nouzové generátory poskytují záložní napájení pro delší výpadky a plánovanou údržbu. Pokud dojde k přírodní katastrofě, může datové centrum využívat zásoby paliva na místě.

Vysokorychlostní a robustní optické sítě propojují datová centra s dalšími hlavními rozbočovači a uživateli internetu. Výpočetní uzly hostitelské úlohy blíže k uživatelům snížit latenci, poskytují geografickou redundanci a zvýšit celkovou odolnost služby. Tým inženýrů pracuje nepřetržitě, aby zajistil, že služby jsou trvale dostupné.

Společnost Microsoft zajišťuje vysokou dostupnost prostřednictvím pokročilého monitorování a reakce na incidenty, servisní podpory a možnosti převzetí služeb při selhání zálohování. Geograficky distribuovaná operační střediska Microsoftfungují 24/7/365. Síť Azure je jedna z největších na světě. Síť pro distribuci optických vláken a obsahu propojuje datová centra a hraniční uzly, aby byla zajištěna vysoká výkonnost a spolehlivost.

## <a name="disaster-recovery"></a>Zotavení po havárii
Azure udržuje vaše data trvanlivá ve dvou umístěních. Můžete zvolit umístění záložního webu. V obou umístěních Azure neustále udržuje tři repliky v pořádku vašich dat.

## <a name="database-availability"></a>Dostupnost databáze
Azure zajišťuje, že databáze je přístupná přes internet ovou bránu s trvalou dostupností databáze. Monitorování vyhodnocuje stav a stav aktivních databází v pětiminutových časových intervalech.

## <a name="storage-availability"></a>Dostupnost úložiště
Azure poskytuje úložiště prostřednictvím vysoce škálovatelné a trvanlivé služby úložiště, která poskytuje koncové body připojení. To znamená, že aplikace může přistupovat ke službě úložiště přímo. Služba úložiště zpracovává příchozí požadavky na úložiště efektivně s transakční integritou.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá, aby zabezpečil infrastrukturu Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
