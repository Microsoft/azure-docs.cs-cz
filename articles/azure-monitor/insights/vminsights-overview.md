---
title: Co je Azure Monitor pro virtuální počítače?
description: Přehled Azure Monitor pro virtuální počítače, který monitoruje stav a výkon virtuálních počítačích Azure kromě automatického zjišťování a mapování aplikačních komponent a jejich závislostí.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480483"
---
# <a name="what-is-azure-monitor-for-vms"></a>Co je Azure Monitor pro virtuální počítače?

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure (VM) a škálovací sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. Zahrnuje podporu pro monitorování výkonu a závislostí aplikací pro virtuální servery, které jsou hostované místně nebo v jiném poskytovateli cloudu. Následující klíčové funkce poskytují podrobný přehled:

- **Předdefinované grafy výkonu trendů:** Zobrazení základních metrik výkonu z operačního systému hostovaného virtuálního uživatele.

- **Mapa závislostí**: Zobrazí propojené součásti s virtuálním virtuálním uživatelem z různých skupin prostředků a předplatných.  

>[!NOTE]
>Nedávno jsme [oznámili změny,](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) které provádíme ve funkci Zdraví na základě zpětné vazby, kterou jsme obdrželi od našich zákazníků s veřejnou předpremiérou. Vzhledem k počtu změn, které provedeme, přestaneme nabízet funkci Zdraví pro nové zákazníky. Stávající zákazníci mohou nadále používat funkci stavu. Další podrobnosti naleznete v [nejčastějších dotazech](vminsights-ga-release-faq.md)k obecné dostupnosti .  

Integrace s protokoly monitorování Azure poskytuje výkonné agregace a filtrování, což umožňuje Azure Monitor pro virtuální počítače analyzovat trendy dat v čase. Tato data můžete zobrazit v jednom virtuálním počítači přímo z virtuálního počítače nebo můžete použít Azure Monitor k poskytování agregované zobrazení virtuálních počítačů, kde zobrazení podporuje režimy kontextu prostředků Azure nebo kontextu pracovního prostoru. Další informace naleznete v tématu [přehled režimů přístupu](../platform/design-logs-deployment.md#access-mode).

![Perspektiva přehledů virtuálních strojů na webu Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor pro virtuální počítače může poskytovat předvídatelný výkon a dostupnost důležitých aplikací. Identifikuje problémy s výkonem a problémy se sítí a může také pomoci pochopit, zda problém souvisí s jinými závislostmi.  

## <a name="data-usage"></a>Využití dat

Když nasadíte Azure Monitor pro virtuální počítače, data, která jsou shromažďovány virtuálními počítači, se ingutují a ukládají ve Službě Azure Monitor. Shromážděná data o výkonu a závislostech jsou uložena v pracovním prostoru Log Analytics. Na základě cen, které se publikují na [stránce s cenami Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/), se Azure Monitor pro virtuální počítače účtuje za:

- Data, která jsou ingestována a uložena.
- Pravidla výstrah, které jsou vytvořeny.
- Oznámení, které jsou odeslány. 

Velikost protokolu se liší podle délky řetězce čítače výkonu a může zvýšit s počtem logických disků a síťových adaptérů přidělených virtuálnímu počítači. Pokud již máte pracovní prostor a shromažďujete tyto čítače, nebudou použity žádné duplicitní poplatky. Pokud už používáte mapu služeb, jediná změna, kterou uvidíte, jsou další data o připojení, která se odešlou do Azure Monitoru.

## <a name="next-steps"></a>Další kroky

Chcete-li porozumět požadavkům a metodám, které vám pomohou monitorovat vaše virtuální počítače, přečtěte [si seznam nasazení Azure Monitoru pro virtuální počítače](vminsights-enable-overview.md).
