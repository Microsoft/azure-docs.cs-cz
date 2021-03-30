---
title: Dostupnost infrastruktury Azure – zabezpečení Azure
description: Tento článek obsahuje informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure a poskytování maximální dostupnosti zákaznických dat.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727217"
---
# <a name="azure-infrastructure-availability"></a>Dostupnost infrastruktury Azure
Tento článek obsahuje informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure a poskytování maximální dostupnosti zákaznických dat. Azure poskytuje robustní dostupnost na základě rozsáhlé redundance dosažené technologií virtualizace.

## <a name="temporary-outages-and-natural-disaster"></a>Dočasné výpadky a přírodní havárie
Microsoft Cloud infrastruktury a provozního týmu navrhuje, sestavuje, pracuje a vylepšuje zabezpečení cloudové infrastruktury. Tento tým zajišťuje, aby infrastruktura Azure zajišťovala vysokou dostupnost a spolehlivost, vysokou efektivitu a chytrou škálovatelnost. Tým poskytuje bezpečnější, privátní a důvěryhodný cloud.

Nepřerušitelný zdroj napájení a nepřeberné banky baterie zajistí, že elektřina zůstane nepřetržitá, pokud dojde k krátkodobému výpadku napájení. Nouzové generátory poskytují výkon zálohování pro rozšířené výpadky a plánovanou údržbu. Pokud dojde k přirozené havárii, může datové centrum využívat rezervy pohonných hmot na pracovišti.

Vysokorychlostní a robustní síťové optické sítě spojují datacentra s dalšími hlavními rozbočovači a uživateli internetu. Výpočetní uzly hostují úlohy blíže uživatelům, aby snížily latenci, poskytovaly geografickou redundanci a zvýšily celkovou odolnost služby. Tým techniků pracuje s časem, aby bylo zajištěno trvalé dostupnosti služeb.

Microsoft zajišťuje vysokou dostupnost prostřednictvím pokročilého monitorování a reakce na incidenty, podporu služeb a možnosti převzetí služeb při selhání zálohování. Geograficky distribuované provozní centra Microsoftu pracují 24/7/365. Síť Azure je jedním z největších světových. Optická síť a distribuce obsahu propojuje datová centra a hraniční uzly, aby se zajistil vysoký výkon a spolehlivost.

## <a name="disaster-recovery"></a>Zotavení po havárii
Azure udržuje data trvalá na dvou místech. Můžete zvolit umístění záložní lokality. V obou umístěních Azure neustále udržuje tři dobré repliky vašich dat.

## <a name="database-availability"></a>Dostupnost databáze
Azure zajišťuje, že je databáze přístupná přes internetovou bránu s využitím trvalé dostupnosti databáze. Monitorování vyhodnocuje stav a stav aktivních databází v intervalech po dobu pěti minut.

## <a name="storage-availability"></a>Dostupnost úložiště
Azure poskytuje úložiště prostřednictvím vysoce škálovatelné a odolné služby úložiště, která poskytuje koncové body připojení. To znamená, že aplikace může přistupovat přímo ke službě úložiště. Služba úložiště zpracovává požadavky na příchozí úložiště efektivně a s transakční integritou.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
