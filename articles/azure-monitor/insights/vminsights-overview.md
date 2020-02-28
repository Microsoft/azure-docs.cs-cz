---
title: Co je Azure Monitor pro virtuální počítače (preview)? | Dokumenty Microsoft
description: Přehled Azure Monitor pro virtuální počítače, který monitoruje stav a výkon virtuálních počítačů Azure Kromě automatického zjišťování a mapování součástí aplikace a jejich závislostí.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670659"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co je Azure Monitor pro virtuální počítače (preview)?

Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. 

Zahrnuje podporu pro monitorování výkonu a závislostí aplikací pro virtuální počítače, které jsou hostované místně nebo v jiném poskytovateli cloudu. Následující klíčové funkce poskytují podrobný přehled:

- **Předdefinované grafy výkonu trendů**: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

- **Mapa závislostí**: zobrazuje propojené komponenty s virtuálním počítačem z různých skupin prostředků a předplatných.  

>[!NOTE]
>Nedávno jsme [oznámili](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) , že jsme provedli změny v závislosti na zpětné vazbě, kterou jsme získali od našich zákazníků ve veřejné verzi Preview. S ohledem na počet změn, které provedeme, budeme přestat nabízet funkci stavu pro nové zákazníky. Stávající zákazníci můžou dál používat funkci stavu. Další podrobnosti najdete v [nejčastějších dotazech k obecné dostupnosti](vminsights-ga-release-faq.md).  

Integrace s protokoly Azure Monitor zajišťuje výkonnou agregaci a filtrování a dokáže analyzovat trendy dat v čase. Takové komplexní monitorování úloh se nedá dosáhnout pomocí Azure Monitor nebo Service Map samostatně.  

Tato data můžete zobrazit v jednom virtuálním počítači přímo z virtuálního počítače nebo můžete použít Azure Monitor k poskytnutí agregovaného zobrazení virtuálních počítačů, kde zobrazení podporuje režimy Azure – kontext nebo pracovní prostor – kontext. Další informace najdete v tématu [Přehled režimů přístupu](../platform/design-logs-deployment.md#access-mode).

![Insights perspektivy virtuálního počítače na webu Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor pro virtuální počítače může poskytovat předvídatelný výkon a dostupnost důležitých aplikací. Identifikuje kritické body výkonu a problémy se sítí. Azure Monitor pro virtuální počítače vám může také porozumět tomu, jestli problém souvisí s ostatními závislostmi.  

## <a name="data-usage"></a>Využití dat

Při nasazení Azure Monitor pro virtuální počítače, data, která se shromažďují ve vašich virtuálních počítačů je ingestuje a uložené ve službě Azure Monitor. Shromážděná data o výkonu a závislostech se ukládají do Log Analyticsho pracovního prostoru. Na základě cen, které jsou publikovány na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), se Azure monitor pro virtuální počítače účtuje takto:

- Data, která má ingestují a ukládají.
- Pravidla výstrah, které jsou vytvořeny.
- Oznámení, které se odesílají. 

Velikost protokolu se liší podle délky řetězců čítačů výkonu a může se zvýšit počtem logických disků a síťových adaptérů, které jsou přiděleny virtuálnímu počítači. Pokud už máte pracovní prostor a tyto čítače shromažďují, jsou použity žádné duplicitní poplatky. Pokud už používáte řešení Service Map, je jediná změna, který se zobrazí data další připojení, která je odeslána do Azure monitoru.

## <a name="next-steps"></a>Další kroky

Pokud chcete pochopit požadavky a metody, které vám pomůžou monitorovat virtuální počítače, přečtěte si téma [nasazení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).
