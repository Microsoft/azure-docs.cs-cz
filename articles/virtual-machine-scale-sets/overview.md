---
title: Přehled škálovacích sad virtuálních počítačů Azure | Dokumentace Microsoftu
description: Seznamte se se škálovacími sadami virtuálních počítačů Azure a způsobem automatického škálování aplikací.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: manayar
ms.openlocfilehash: 5cd9593bba1e97f52bfa37467920959b1ef0f698
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329601"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Co jsou škálovací sady virtuálních počítačů?
Škálovací sady virtuálních počítačů Azure umožňují vytvářet a spravovat skupiny identických virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat s ohledem na požadavky nebo definovaný plán. Škálovací sady zajišťují vysokou dostupnost aplikací a umožňují centrálně spravovat, konfigurovat a aktualizovat velký počet virtuálních počítačů. S využitím škálovacích sad virtuálních počítačů můžete sestavovat rozsáhlé služby pro oblasti, jako jsou výpočty, velké objemy dat a úlohy kontejneru.


## <a name="why-use-virtual-machine-scale-sets"></a>Proč používat škálovací sady virtuálních počítačů?
Za účelem zajištění redundance a vyššího výkonu se aplikace obvykle distribuují na více instancí. Zákazníci můžou k vaší aplikaci přistupovat přes nástroj pro vyrovnávání zatížení, který distribuuje požadavky na jednu z instancí aplikace. V případě, že potřebujete provést údržbu nebo aktualizaci instance aplikace, musí se vaši zákazníci distribuovat do jiné dostupné instance aplikace. Abyste udrželi krok s dalšími požadavky zákazníků, můžete zvýšit počet instancí aplikace, na kterých se vaše aplikace spouští.

Škálovací sady virtuálních počítačů Azure poskytují možnosti správy pro aplikace, které se spouštějí na více virtuálních počítačích, [automatické škálování prostředků](virtual-machine-scale-sets-autoscale-overview.md) a vyrovnávání zatížení provozu. Škálovací sady poskytují následující klíčové výhody:

- **Snadné vytváření a správa několika virtuálních počítačů**
    - Pokud máte mnoho virtuálních počítačů, na kterých se vaše aplikace spouští, je důležité v celém prostředí udržovat konzistentní konfiguraci. Pro zajištění spolehlivého výkonu aplikace by všechny virtuální počítače měly mít stejnou velikost, konfiguraci disků a instalace aplikace.
    - V případě škálovacích sad se všechny instance virtuálních počítačů vytvářejí s využitím stejné základní image operačního systému a konfigurace. Tento přístup umožňuje snadnou správu stovek virtuálních počítačů bez dalších úloh konfigurace nebo správy sítě.
    - Škálovací sady podporují použití [nástroje pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md) pro účely základní distribuce provozu úrovně 4 a službu [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) pro pokročilejší distribuci provozu úrovně 7 a ukončování protokolu SSL.

- **Zajištění vysoké dostupnosti a odolnosti aplikací**
    - Škálovací sady slouží ke spouštění více instancí vaší aplikace. Pokud u jedné z těchto instancí virtuálních počítačů dojde k problému, zákazníci můžou pokračovat v přístupu k aplikaci přes některou z dalších instancí virtuálních počítačů, a to s minimálním přerušením.
    - Pokud potřebujete větší dostupnost, můžete použít [zóny dostupnosti](../availability-zones/az-overview.md) k automatické distribuci instancí virtuálních počítačů ve škálovací sadě v rámci jednoho datacentra nebo do více datacenter.

- **Možnost automatického škálování aplikace s ohledem na měnící se požadavky na prostředky**
    - Požadavky zákazníků na vaši aplikaci se v průběhu dne nebo týdne můžou měnit. S ohledem na požadavky zákazníků můžou škálovací sady automaticky zvýšit počet instancí virtuálních počítačů při nárůstu poptávky a při snížení poptávky pak počet instancí virtuálních počítačů snížit.
    - Automatické škálování také minimalizuje počet nepotřebných instancí virtuálních počítačů s vaší aplikací při nízké poptávce, zatímco zákazníci mají stále k dispozici přijatelnou úroveň výkonu díky automatickému přidávání dalších instancí virtuálních počítačů, když se poptávka zvýší. Tato schopnost pomáhá snižovat náklady a efektivně vytvářet prostředky Azure podle potřeby.

- **Práce ve velkém měřítku**
    - Škálovací sady podporují až 1 000 instancí virtuálních počítačů. Pokud vytváříte a nahráváte vlastní image virtuálních počítačů, platí omezení na 300 instancí virtuálních počítačů.
    - Pro zajištění nejlepšího výkonu pro úlohy v produkčním prostředí, použijte [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Rozdíly mezi virtuálními počítači a škálovacími sadami
Škálovací sady se sestavují z virtuálních počítačů. Škálovací sady poskytují vrstvy správy a automatizace pro spouštění a škálování aplikací. Místo toho můžete ručně vytvářet a spravovat jednotlivé virtuální počítače nebo integrovat stávající nástroje a vytvořit tak podobnou úroveň automatizace. Následující tabulka popisuje výhody škálovacích sad v porovnání s ruční správou několika instancí virtuálních počítačů.

| Scénář                           | Ručně vytvořená skupina virtuálních počítačů                                                                    | Škálovací sada virtuálních počítačů |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Přidání dalších instancí virtuálních počítačů        | Ruční proces vytvoření, konfigurace a zajištění dodržování předpisů                             | Automatické vytvoření s využitím centrální konfigurace |
| Vyrovnávání a distribuce provozu | Ruční proces vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure nebo služby Application Gateway      | Možnost automatického vytvoření nástroje pro vyrovnávání zatížení Azure nebo služby Application Gateway a integrace s nimi |
| Vysoká dostupnost a redundance   | Ruční vytvoření skupiny dostupnosti nebo distribuce a sledování virtuálních počítačů napříč zónami dostupnosti | Automatická distribuce instancí virtuálních počítačů napříč zónami dostupnosti nebo skupinami dostupnosti |
| Škálování virtuálních počítačů                     | Ruční monitorování a služba Azure Automation                                                 | Automatické škálování na základě metrik hostitele, metrik hosta, Application Insights nebo plánu |

Za použití škálovacích sad se neúčtují žádné další poplatky. Platíte pouze za základní výpočetní prostředky, jako jsou instance virtuálních počítačů, nástroj pro vyrovnávání zatížení nebo spravované diskové úložiště. Za funkce správy a automatizace, jako jsou automatické škálování a redundance, se neúčtují žádné další poplatky nad rámec použití virtuálních počítačů.


## <a name="next-steps"></a>Další postup
Začněte vytvořením své první škálovací sady virtuálních počítačů na webu Azure Portal.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady na webu Azure Portal](quick-create-portal.md)
