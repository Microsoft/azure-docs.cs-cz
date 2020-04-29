---
title: Co je Azure Monitor pro virtuální počítače?
description: Přehled Azure Monitor pro virtuální počítače, který monitoruje stav a výkon virtuálních počítačů Azure Kromě automatického zjišťování a mapování součástí aplikace a jejich závislostí.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480483"
---
# <a name="what-is-azure-monitor-for-vms"></a>Co je Azure Monitor pro virtuální počítače?

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure a škálované sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. Zahrnuje podporu pro monitorování výkonu a závislostí aplikací pro virtuální počítače, které jsou hostované místně nebo v jiném poskytovateli cloudu. Následující klíčové funkce poskytují podrobný přehled:

- **Předdefinované grafy výkonu trendů**: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

- **Mapa závislostí**: zobrazuje propojené komponenty s virtuálním počítačem z různých skupin prostředků a předplatných.  

>[!NOTE]
>Nedávno jsme [oznámili](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) , že jsme provedli změny v závislosti na zpětné vazbě, kterou jsme získali od našich zákazníků ve veřejné verzi Preview. S ohledem na počet změn, které provedeme, budeme přestat nabízet funkci stavu pro nové zákazníky. Stávající zákazníci můžou dál používat funkci stavu. Další podrobnosti najdete v [nejčastějších dotazech k obecné dostupnosti](vminsights-ga-release-faq.md).  

Integrace s protokoly Azure Monitor poskytuje výkonné agregace a filtrování, což umožňuje Azure Monitor pro virtuální počítače analyzovat trendy dat v čase. Tato data můžete zobrazit v jednom virtuálním počítači přímo z virtuálního počítače nebo můžete použít Azure Monitor k poskytnutí agregovaného zobrazení virtuálních počítačů, kde zobrazení podporuje režimy Azure – kontext nebo pracovní prostor – kontext. Další informace najdete v tématu [Přehled režimů přístupu](../platform/design-logs-deployment.md#access-mode).

![Perspektiva analýzy virtuálních počítačů v Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor pro virtuální počítače může poskytovat předvídatelný výkon a dostupnost důležitých aplikací. Identifikuje problémová místa výkonu a problémy se sítí a může vám také porozumět tomu, jestli problém souvisí s ostatními závislostmi.  

## <a name="data-usage"></a>Využití dat

Když nasadíte Azure Monitor pro virtuální počítače, data shromážděná vašimi virtuálními počítači se ingestují a ukládají v Azure Monitor. Shromážděná data o výkonu a závislostech se ukládají do Log Analyticsho pracovního prostoru. Na základě cen, které jsou publikovány na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), se Azure monitor pro virtuální počítače účtuje takto:

- Data, která se ingestují a ukládají.
- Pravidla výstrah, která jsou vytvořena.
- Oznámení, která jsou odeslána. 

Velikost protokolu se liší podle délky řetězců čítačů výkonu a může se zvýšit počtem logických disků a síťových adaptérů, které jsou přiděleny virtuálnímu počítači. Pokud už máte pracovní prostor a shromažďujete tyto čítače, neuplatní se žádné duplicitní poplatky. Pokud už Service Map používáte, bude se jednat o další data o připojení, která se odesílají do Azure Monitor.

## <a name="next-steps"></a>Další kroky

Pokud chcete pochopit požadavky a metody, které vám pomůžou monitorovat virtuální počítače, přečtěte si téma [nasazení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).
