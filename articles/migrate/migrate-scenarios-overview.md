---
title: Migrace místního datacentra do Azure | Microsoft Docs
description: Přečtěte si dokument white paper věnovaný migraci místních datových center do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migrace místních úloh do Azure


Microsoft Azure poskytuje přístup ke komplexní sadě cloudových služeb, pomocí kterých můžete jako vývojáři a IT specialisté sestavovat, nasazovat a spravovat aplikace s využitím celé řady nástrojů a rozhraní a globální sítě datacenter. Když vaše firma čelí výzvám souvisejícím s digitalizací, cloud Azure vám pomůže zjistit, jak optimalizovat prostředky a operace, zapojit zákazníky i zaměstnance a transformovat vaše produkty.

I přes všechny výhody, které cloud poskytuje z hlediska rychlosti a flexibility, minimalizace nákladů, vysokého výkonu a spolehlivost, si však v Azure uvědomujeme, že řada organizací bude potřebovat ještě nějakou dobu provozovat místní datacentra. V reakci na překážky přechodu do cloudu Azure poskytuje strategii hybridního cloudu, která propojí vaše místní datacentra s veřejným cloudem Azure. Pomocí cloudových prostředků Azure, jako je například služba Backup, můžete zajistit ochranu místních prostředků nebo pomocí Azure Analytics získat přehled o místních úlohách. 

V rámci strategie hybridního cloudu poskytuje Azure rozšiřující se řešení pro migraci místních aplikací a úloh do cloudu. Pomocí jednoduchých postupů můžete komplexně vyhodnotit své místní prostředky, abyste zjistili, jak si povedou v cloudu Azure. Když budete mít k dispozici podrobné posouzení, můžete bez obav migrovat prostředky do Azure. Po zprovoznění prostředků v Azure je můžete optimalizovat, abyste zachovali a vylepšili úroveň přístupu, flexibility, zabezpečení a spolehlivosti.

V této sérii článků o migraci se dozvíte, jak naplánovat a sestavit strategii migrace pro vaši společnost. Články popisují několik scénářů, které postupně rozšiřujeme a jejichž složitost se postupně zvyšuje. Tyto scénáře jsou uvedené v následující tabulce. Pro každý scénář poskytujeme přehled a architekturu migrace a předvádíme kroky, které proces migrace zahrnuje. 

**Scénář** | **Řešení** | **Služby** | **Článek** 
--- | --- | --- | ---
[Scénář 1: Zjišťování a posouzení](migrate-scenarios-assessment.md) | Zjišťovat a vyhodnocení místní aplikace, data a infrastruktury pro migraci na Azure | Data Migration Assistant, služba Azure Migrate  | Nově dostupné
**[Scénář 2: Metody opětovného hostování aplikace](migrate-scenarios-lift-and-shift.md)** | Navýšení a shift aplikace do Azure. | Azure Site Recovery, Azure Database Migration Service, Azure SQL Database Managed Instance | Nově dostupné
**Scénář 3: Refaktorovat aplikace** | Refaktorovat aplikace během migrace do Azure. | Plánuje se | Plánováno
**Scénář 4: Rearchitect aplikace** | Rearchitect aplikace během migrace do Azure. | Plánuje se | Plánováno
**Scénář 5: Opětovné sestavení aplikace** |Opětovné sestavení aplikace během migrace do Azure | Plánuje se | Plánováno




