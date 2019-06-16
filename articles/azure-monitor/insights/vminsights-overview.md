---
title: Co je Azure Monitor pro virtuální počítače (preview)? | Dokumenty Microsoft
description: Azure Monitor pro virtuální počítače je funkce služby Azure Monitor, který kombinuje stavu a výkonu monitorování operačního systému virtuálního počítače Azure, stejně jako automatické zjišťování mezi součástmi aplikace a závislosti s jiným prostředkům a mapuje komunikace mezi nimi. Tento článek obsahuje přehled.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 7d86b3fe9aeddd603d0c40b1c760cabdee42e396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522111"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co je Azure Monitor pro virtuální počítače (preview)?

Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. 

Azure Monitor pro virtuální počítače jako řešení, zahrnuje podporu pro monitorování výkonu a používání závislosti virtuálních počítačů, které jsou hostované v místním prostředí nebo jiného zprostředkovatele cloudu. Tři klíčové funkce poskytovat podrobnější informace:

* **Logické součásti virtuálních počítačů Azure, který spustí Windows a Linuxem**: Se měří podle kritérií stavu předem nakonfigurované a že vás upozornit, když je splněna podmínka Vyhodnocená.  

* **Předdefinované populárních grafy výkonu**: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

* **Mapa závislostí**: Zobrazí vzájemně propojených součástí virtuálního počítače z různých skupin prostředků a předplatných.  

Funkce jsou uspořádané do tří perspektiv:

* Stav
* Výkon
* Mapa

>[!NOTE]
>Funkce stavu je v současné době nabízí jenom pro virtuální počítače Azure. Výkon a funkce mapování podporují virtuální počítače Azure, škálovací sady virtuálních počítačů Azure a virtuální počítače, které jsou hostované ve vašem prostředí nebo jiný poskytovatel cloudu.

Integrace s protokoly Azure monitoru poskytuje výkonné agregace a filtrování, a to analyzovat trendy v datech v čase. Tyto úlohy komplexní monitorování není možné dosáhnout s Azure Monitor nebo Service Map samostatně.  

Tato data můžete zobrazit v jeden virtuální počítač z virtuálního počítače přímo, nebo Azure Monitor můžete použít k zajištění souhrnný náhled na vašich virtuálních počítačů. Toto zobrazení vychází z perspektivy jednotlivých funkcí:

* **Stav**: Virtuální počítače se vztahují na skupinu prostředků.
* **Mapa** a **výkonu**: Virtuální počítače jsou nakonfigurované k ukládání dat do konkrétního pracovního prostoru Log Analytics.

![Insights perspektivy virtuálního počítače na webu Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Pro virtuální počítače Azure Monitor dokáže zajistit předvídatelný výkon a dostupnost důležitých aplikací. Identifikuje událostí kritické operačního systému, kritické body výkonu a problémy se sítí. Azure Monitor pro virtuální počítače můžete taky vám pomůžou pochopit, jestli problém souvisí s další závislosti.  

## <a name="data-usage"></a>Využití dat 

Při nasazení Azure Monitor pro virtuální počítače, data, která se shromažďují ve vašich virtuálních počítačů je ingestuje a uložené ve službě Azure Monitor. Kritéria metrik stavu se ukládají ve službě Azure Monitor, databáze časových řad, shromažďovat data o výkonu a závislostí se ukládají v pracovním prostoru Log Analytics. Na ceny, který je publikovaný na základě [stránce s cenami Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), účtuje se pro monitorování Azure pro virtuální počítače:

* Data, která má ingestují a ukládají.
* Počet metrik stavu kritéria, časových řad, která jsou monitorovaná.
* Pravidla výstrah, které jsou vytvořeny.
* Oznámení, které se odesílají. 

Velikost protokolu se liší podle délky řetězce čítačů výkonu a můžete zvýšit počet logických disků a síťových adaptérů přidělí virtuálnímu počítači. Pokud už máte pracovní prostor a tyto čítače shromažďují, jsou použity žádné duplicitní poplatky. Pokud už používáte řešení Service Map, je jediná změna, který se zobrazí data další připojení, která je odeslána do Azure monitoru.

## <a name="next-steps"></a>Další postup
Informace o tom požadavky a metody, které vám pomohou monitorovat virtuální počítače, projděte si [nasazení Azure Monitor pro virtuální počítače](vminsights-enable-overview.md).
