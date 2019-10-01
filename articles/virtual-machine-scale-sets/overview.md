---
title: Přehled služby Azure Virtual Machine Scale Sets | Microsoft Docs
description: Přečtěte si o službě Azure Virtual Machine Scale Sets a o tom, jak automaticky škálovat aplikace
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: manayar
ms.openlocfilehash: 73580814dcfe8f967684aca4ce433a40e7bbedc0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679385"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Co jsou sady škálování virtuálních počítačů?
Azure Virtual Machine Scale Sets umožňují vytvářet a spravovat skupiny identických virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvětšit nebo zmenšit v reakci na poptávku nebo podle definovaného plánu. Sady škálování poskytují vysokou dostupnost vašim aplikacím a umožňují centrálně spravovat, konfigurovat a aktualizovat velký počet virtuálních počítačů. Pomocí služby Virtual Machine Scale Sets můžete vytvářet rozsáhlé služby pro oblasti, jako jsou výpočetní prostředky, velké objemy dat a úlohy kontejnerů.


## <a name="why-use-virtual-machine-scale-sets"></a>Proč používat Virtual Machine Scale Sets?
Pro zajištění redundance a zlepšení výkonu jsou aplikace obvykle distribuovány mezi více instancí. Zákazníci můžou k vaší aplikaci přistupovat prostřednictvím nástroje pro vyrovnávání zatížení, který distribuuje požadavky na jednu z instancí aplikace. Pokud potřebujete provést údržbu nebo aktualizaci instance aplikace, musí být vaši zákazníci distribuováni do jiné dostupné instance aplikace. Aby se zajistila další poptávka se zákazníky, možná budete muset zvýšit počet instancí aplikace, které spouštějí vaši aplikaci.

Azure Virtual Machine Scale Sets poskytují možnosti správy pro aplikace, které běží na mnoha virtuálních počítačích, [Automatické škálování prostředků](virtual-machine-scale-sets-autoscale-overview.md)a vyrovnávání zatížení provozu. Sady škálování poskytují následující klíčové výhody:

- **Snadné vytváření a Správa více virtuálních počítačů**
    - Pokud máte mnoho virtuálních počítačů, na kterých běží vaše aplikace, je důležité udržovat v celém prostředí konzistentní konfiguraci. Pro zajištění spolehlivého výkonu vaší aplikace by se měla shodovat velikost virtuálního počítače, konfigurace disku a instalace aplikací napříč všemi virtuálními počítači.
    - Se sadami škálování jsou všechny instance virtuálních počítačů vytvořené ze stejné základní image operačního systému a konfigurace. Tento přístup umožňuje snadno spravovat stovky virtuálních počítačů bez dalších úloh konfigurace nebo správy sítě.
    - Sady škálování podporují použití [Nástroje pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md) pro základní distribuci provozu vrstvy 4 a [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) pro pokročilejší distribuci provozu vrstvy 7 a ukončení protokolu SSL.

- **Poskytuje vysokou dostupnost a odolnost aplikací.**
    - Sady škálování se používají ke spouštění více instancí aplikace. Pokud má jedna z těchto instancí virtuálních počítačů nějaký problém, zákazníci budou mít přístup k vaší aplikaci přes jednu z dalších instancí virtuálních počítačů s minimálním přerušením.
    - Pro zajištění vyšší dostupnosti můžete použít [zóny dostupnosti](../availability-zones/az-overview.md) k automatické distribuci instancí virtuálních počítačů do sady škálování v rámci jednoho datového centra nebo napříč různými datacentry.

- **Umožňuje aplikaci automaticky škálovat změny požadavků na prostředky.**
    - Zákaznická poptávka za vaše aplikace se může v průběhu dne nebo týdne změnit. Aby bylo možné odpovídat zákaznickým požadavkům, můžou sady škálování automaticky zvýšit počet instancí virtuálních počítačů, když se zvyšuje poptávka aplikací, a pak snížit počet instancí virtuálních počítačů, které se sníží.
    - Automatické škálování také minimalizuje počet zbytečných instancí virtuálních počítačů, na kterých běží vaše aplikace, když je poptávka nízká, zatímco zákazníci dostanou přijatelnou úroveň výkonu při zvětšování poptávky a automaticky se přidávají další instance virtuálních počítačů. Tato možnost pomáhá snižovat náklady a efektivně vytvářet prostředky Azure podle potřeby.

- **Funguje ve velkém měřítku**
    - Sady škálování podporují až 1 000 instancí virtuálních počítačů. Pokud vytváříte a nahráváte vlastní image virtuálních počítačů, tento limit je 600 instancí virtuálních počítačů.
    - K dosažení nejlepšího výkonu u produkčních úloh použijte [Azure Managed disks](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Rozdíly mezi virtuálními počítači a sadami škálování
Sady škálování jsou sestavené z virtuálních počítačů. Pomocí sad škálování jsou k dispozici vrstvy správy a automatizace pro spouštění a škálování aplikací. Místo toho můžete ručně vytvořit a spravovat jednotlivé virtuální počítače nebo integrovat stávající nástroje a vytvořit podobnou úroveň automatizace. Následující tabulka popisuje výhody sad škálování v porovnání s ruční správou více instancí virtuálních počítačů.

| Scénář                           | Ruční skupina virtuálních počítačů                                                                    | Sada škálování virtuálních počítačů |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Přidání dalších instancí virtuálních počítačů        | Ruční proces vytvoření, konfigurace a zajištění dodržování předpisů                             | Automaticky vytvořit z centrální konfigurace |
| Vyrovnávání zatížení a distribuce | Ruční proces vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure nebo Application Gateway      | Může automaticky vytvářet a integrovat se službou Azure Load Balancer nebo Application Gateway. |
| Vysoká dostupnost a redundance   | Ruční vytvoření skupiny dostupnosti nebo distribuce a sledování virtuálních počítačů napříč Zóny dostupnosti | Automatická distribuce instancí virtuálních počítačů napříč Zóny dostupnosti nebo skupinami dostupnosti |
| Škálování virtuálních počítačů                     | Ruční monitorování a Azure Automation                                                 | Automatické škálování na základě metrik hostitelů, metrik v hostu, Application Insights nebo plánu |

Pro škálování sad se neúčtují žádné další náklady. Platíte jenom za základní výpočetní prostředky, jako jsou instance virtuálních počítačů, nástroj pro vyrovnávání zatížení nebo spravované diskové úložiště. Funkce správy a automatizace, jako je například automatické škálování a redundance, neúčtují za použití virtuálních počítačů žádné další poplatky.

## <a name="how-to-monitor-your-scale-sets"></a>Jak monitorovat sady škálování

Použijte [Azure monitor pro virtuální počítače](../azure-monitor/insights/vminsights-overview.md), který má jednoduchý proces připojování a bude automatizovat shromažďování důležitých ČÍTAČŮ výkonu procesoru, paměti, disku a sítě z virtuálních počítačů ve vaší sadě škálování. Obsahuje také další možnosti monitorování a předdefinované vizualizace, které vám pomůžou se zaměřit na dostupnost a výkon vašich sad škálování.

Pokud chcete shromažďovat podrobné informace o vaší aplikaci, včetně zobrazení stránek, žádostí o aplikace a výjimek, povolte monitorování pro [aplikaci Virtual Machine Scale set](../azure-monitor/app/azure-vm-vmss-apps.md) s Application Insights. Dále ověřte dostupnost aplikace konfigurací [testu dostupnosti](../azure-monitor/app/monitor-web-app-availability.md) pro simulaci provozu uživatelů.

## <a name="next-steps"></a>Další kroky
Začněte tím, že na Azure Portal vytvoříte svou první sadu škálování virtuálního počítače.

> [!div class="nextstepaction"]
> [Vytvoření sady škálování v Azure Portal](quick-create-portal.md)
