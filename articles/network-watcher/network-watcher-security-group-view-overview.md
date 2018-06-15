---
title: Úvod do zobrazení skupiny zabezpečení v sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka obsahuje přehled funkce zobrazení zabezpečení sledovací proces sítě
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.openlocfilehash: f4175875b68c52e68588b8d0debd003ab73427ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864207"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Úvod do zobrazení skupiny zabezpečení sítě v sledovací proces sítě Azure

Skupiny zabezpečení sítě na úrovni podsítě nebo na úrovni seskupování souvisejí. Když přidružené na úrovni podsítě, bude se vztahovat na všechny instance virtuálního počítače v podsíti. Skupina zabezpečení sítě zobrazení vrátí nakonfigurované skupiny Nsg a pravidel, které jsou přidružené úrovni síťových Adaptérů a podsítě pro virtuální počítač poskytuje přehled o konfiguraci. Kromě toho pravidla efektivní zabezpečení jsou vráceny pro jednotlivé síťové adaptéry ve virtuálním počítači. Zobrazení pomocí skupiny zabezpečení sítě, můžete vyhodnotit virtuální počítač chyb zabezpečení sítě, jako je například otevřené porty. Můžete také ověřit, pokud vaše skupina zabezpečení sítě funguje podle očekávání, na základě [srovnání nakonfigurované a pravidla efektivní zabezpečení](network-watcher-nsg-auditing-powershell.md).

Více rozšířených případ použití se dodržování předpisů pro zabezpečení a auditování. Můžete definovat doporučený sadu pravidel zabezpečení jako model pro řízení zabezpečení ve vaší organizaci. Audit pravidelné dodržování předpisů, můžou se implementovat v programový způsob tak, že porovnáte doporučený pravidla s efektivní pravidla pro jednotlivé virtuální počítače ve vaší síti.

Na portálu, které se dál dělí platné, podsítě, síťové a výchozí pravidla. To poskytuje jednoduché zobrazení do pravidla používaná k virtuálnímu počítači. Všechna pravidla zabezpečení bez ohledu na to, na kartě snadno stáhnout do souboru CSV je k dispozici tlačítko Stáhnout.

![zobrazení skupiny zabezpečení][1]

Pravidla lze vybrat a otevře nové okno se na skupinu zabezpečení sítě a zdrojové a cílové předpony. V tomto okně můžete přejít přímo na prostředek skupinu zabezpečení sítě.

![Přejít k podrobnostem][2]

### <a name="next-steps"></a>Další kroky

Zjistěte, jak auditovat nastavení skupinu zabezpečení sítě, navštivte stránky [nastavení auditu skupinu zabezpečení sítě pomocí prostředí PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









