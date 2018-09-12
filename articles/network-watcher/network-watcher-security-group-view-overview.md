---
title: Úvod do zobrazení skupin zabezpečení ve službě Azure Network Watcher | Dokumentace Microsoftu
description: Tato stránka obsahuje přehled možností zobrazení zabezpečení Network Watcher
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
ms.openlocfilehash: 15f6bd0d7da63924e52db8ec7e2cbb0ee7483f82
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391534"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Úvod do zobrazení skupin zabezpečení sítě ve službě Azure Network Watcher

Skupiny zabezpečení sítě jsou spojeny na úrovni podsítě nebo na úrovni síťové karty. Pokud se související na úrovni podsítě, platí pro všechny instance virtuálních počítačů v podsíti. Zobrazení skupiny zabezpečení sítě vrátí nakonfigurované skupiny Nsg a pravidla, které jsou spojeny na úrovni síťové karty NIC a podsítě pro virtuální počítač poskytuje přehled o konfiguraci. Kromě toho jsou vráceny platná pravidla zabezpečení pro jednotlivé síťové adaptéry na virtuálním počítači. Zobrazení pomocí skupiny zabezpečení sítě můžete vyhodnotit virtuálního počítače pro zranitelností sítí, jako je otevření portů. Můžete také ověřit, zda vaše skupiny zabezpečení sítě funguje podle očekávání, na základě [srovnání nakonfigurované a pravidel zabezpečení schválené](network-watcher-nsg-auditing-powershell.md).

Více rozšířené případem použití je v dodržování předpisů zabezpečení a auditování. Doporučené postupy sadu pravidel zabezpečení jako model zabezpečení, zásad správného řízení můžete definovat ve vaší organizaci. Audit pravidelné dodržování předpisů můžete implementovat prostřednictvím kódu programu způsobem porovnáním doporučené pravidla s efektivní pravidla pro každý virtuální počítač ve vaší síti.

Na portálu, které jsou rozděleny pravidla platí, podsíti, síťové rozhraní a výchozí. To poskytuje jednoduchý přehled pravidla použitá k virtuálnímu počítači. Tlačítko pro stažení je k dispozici všechna pravidla zabezpečení bez ohledu na to, na kartě snadno stáhnout do souboru CSV.

![zobrazení skupin zabezpečení][1]

Pravidla je možné vybrat a otevře se nové okno zobrazí skupiny zabezpečení sítě a zdrojových a cílových předpon. Z tohoto okna můžete přejít přímo na prostředek skupiny zabezpečení sítě.

![Přejít k podrobnostem][2]

### <a name="next-steps"></a>Další postup

Zjistěte, jak auditovat nastavení skupiny zabezpečení sítě návštěvou [nastavení auditu skupina zabezpečení sítě pomocí Powershellu](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









