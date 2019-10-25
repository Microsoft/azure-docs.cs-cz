---
title: Co je Azure Monitor pro virtuální počítače (Preview)? | Microsoft Docs
description: Azure Monitor pro virtuální počítače je funkce Azure Monitor, která kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure a také automaticky zjišťuje součásti aplikací a závislosti s dalšími prostředky a mapuje komunikaci. mezi nimi. Tento článek poskytuje přehled.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: c6135f3ab90a2002c3cf0c8d26211d66d0c637e8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802401"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co je Azure Monitor pro virtuální počítače (Preview)?

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure a škálované sady virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. 

Zahrnuje podporu pro monitorování výkonu a závislostí aplikací pro virtuální počítače, které jsou hostované místně nebo v jiném poskytovateli cloudu. Tři klíčové funkce poskytují podrobný přehled:

- **Logické součásti virtuálních počítačů Azure a sady škálování virtuálních počítačů se systémem Windows a Linux**: jsou měřeny oproti předem nakonfigurovaným kritériím stavu a při splnění vyhodnocené podmínky vás upozorní.  

- **Předdefinované grafy výkonu trendů**: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

- **Mapa závislostí**: zobrazuje propojené komponenty s virtuálním počítačem z různých skupin prostředků a předplatných.  

Funkce jsou uspořádány do tří perspektiv:

- Stav akce
- Výkon
- Mapa

>[!NOTE]
>Nedávno jsme [oznámili](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) , že jsme provedli změny ve funkci stavu na základě zpětné vazby, kterou jsme získali od našich zákazníků ve veřejné verzi Preview. S ohledem na počet změn, které provedeme, budeme přestat nabízet funkci stavu pro nové zákazníky. Stávající zákazníci můžou dál používat funkci stavu. Další podrobnosti najdete v [nejčastějších dotazech k obecné dostupnosti](vminsights-ga-release-faq.md).  

Integrace s protokoly Azure Monitor zajišťuje výkonnou agregaci a filtrování a dokáže analyzovat trendy dat v čase. Takové komplexní monitorování úloh se nedá dosáhnout pomocí Azure Monitor nebo Service Map samostatně.  

Tato data můžete zobrazit v jednom virtuálním počítači přímo z virtuálního počítače nebo můžete použít Azure Monitor k poskytování agregovaného zobrazení virtuálních počítačů. Toto zobrazení je založené na perspektivě jednotlivých funkcí:

- **Stav**: virtuální počítače souvisejí se skupinou prostředků.
- **Mapování** a **výkon**: virtuální počítače jsou nakonfigurovány tak, aby hlásily do konkrétního pracovního prostoru Log Analytics.

![Perspektiva analýzy virtuálních počítačů v Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor pro virtuální počítače může poskytovat předvídatelný výkon a dostupnost důležitých aplikací. Identifikuje kritické události operačního systému, problémy s výkonem a problémy se sítí. Azure Monitor pro virtuální počítače vám může také porozumět tomu, jestli problém souvisí s ostatními závislostmi.  

## <a name="data-usage"></a>Využití dat

Když nasadíte Azure Monitor pro virtuální počítače, data shromážděná vašimi virtuálními počítači se ingestují a ukládají v Azure Monitor. Metriky kritérií stavu jsou uložené v Azure Monitor v databázi časových řad. shromážděná data o výkonu a závislostech se ukládají v pracovním prostoru Log Analytics. Na základě cen, které jsou publikovány na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), se Azure monitor pro virtuální počítače účtuje takto:

- Data, která se ingestují a ukládají.
- Počet monitorovaných časových řad metriky stavu.
- Pravidla výstrah, která jsou vytvořena.
- Oznámení, která jsou odeslána. 

Velikost protokolu se liší podle délky řetězců čítačů výkonu a může se zvýšit počtem logických disků a síťových adaptérů, které jsou přiděleny virtuálnímu počítači. Pokud už máte pracovní prostor a shromažďujete tyto čítače, neuplatní se žádné duplicitní poplatky. Pokud už Service Map používáte, bude se jednat o další data o připojení, která se odesílají do Azure Monitor.

## <a name="next-steps"></a>Další kroky

Pokud chcete pochopit požadavky a metody, které vám pomůžou monitorovat virtuální počítače, přečtěte si téma [nasazení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).
