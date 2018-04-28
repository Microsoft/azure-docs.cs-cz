---
title: Migrace místního datacentra do Azure | Microsoft Docs
description: Přečtěte si dokument white paper věnovaný migraci místních datových center do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: be322596da0c3e5ba18aa64285c437cdb823fc4b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migrace místních úloh do Azure


Microsoft Azure poskytuje přístup ke komplexní sadě cloudových služeb, pomocí kterých můžete jako vývojáři a IT specialisté sestavovat, nasazovat a spravovat aplikace s využitím celé řady nástrojů a rozhraní a globální sítě datacenter. Když vaše firma čelí výzvám souvisejícím s digitalizací, cloud Azure vám pomůže zjistit, jak optimalizovat prostředky a operace, zapojit zákazníky i zaměstnance a transformovat vaše produkty.

I přes všechny výhody, které cloud poskytuje z hlediska rychlosti a flexibility, minimalizace nákladů, vysokého výkonu a spolehlivost, si však v Azure uvědomujeme, že řada organizací bude potřebovat ještě nějakou dobu provozovat místní datacentra. V reakci na překážky přechodu do cloudu Azure poskytuje strategii hybridního cloudu, která propojí vaše místní datacentra s veřejným cloudem Azure. Pomocí cloudových prostředků Azure, jako je například služba Backup, můžete zajistit ochranu místních prostředků nebo pomocí Azure Analytics získat přehled o místních úlohách. 

V rámci strategie hybridního cloudu poskytuje Azure rozšiřující se řešení pro migraci místních aplikací a úloh do cloudu. Pomocí jednoduchých postupů můžete komplexně vyhodnotit své místní prostředky, abyste zjistili, jak si povedou v cloudu Azure. Když budete mít k dispozici podrobné posouzení, můžete bez obav migrovat prostředky do Azure. Po zprovoznění prostředků v Azure je můžete optimalizovat, abyste zachovali a vylepšili úroveň přístupu, flexibility, zabezpečení a spolehlivosti.

V této sérii článků o migraci se dozvíte, jak naplánovat a sestavit strategii migrace pro vaši společnost. Články popisují několik scénářů, které postupně rozšiřujeme a jejichž složitost se postupně zvyšuje. Tyto scénáře jsou uvedené v následující tabulce. Pro každý scénář poskytujeme přehled a architekturu migrace a předvádíme kroky, které proces migrace zahrnuje. 

**Scénář** | **Řešení** | **Služby** | **Článek** 
--- | --- | --- | ---
[Scénář 1: Zjišťování a posouzení](migrate-scenarios-assessment.md) | Zjištění místních aplikací a dat a posouzení vhodnosti jejich migrace do Azure | Data Migration Assistant, služba Azure Migrate  | Nově dostupné
**Scénář 2: Migrace metodou „lift and shift“** | Přesun hostování interních aplikací do Azure. Optimalizace v Azure po migraci. | Azure Site Recovery, Azure Database Migration Service, Azure SQL Database Managed Instance | Nově dostupné
**Scénář 3: Refaktorování a migrace** | Modernizace a refaktorování místních zákaznických aplikací během migrace do Azure. | Plánuje se | Plánováno
**Scénář 4: Úprava architektury a migrace** | Úprava architektury a migrace zákaznických transakčních webů během migrace do Azure | Plánuje se | Plánováno
**Scénář 5: Opětovné sestavení** |Opětovné sestavení a migrace zákaznických aplikací a dat do Azure | Plánuje se | Plánováno




