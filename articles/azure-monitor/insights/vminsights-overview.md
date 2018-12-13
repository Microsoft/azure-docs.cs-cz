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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: c7d2004da52d83ceda62dc31583797d9a218ef48
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085447"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co je Azure Monitor pro virtuální počítače (preview)?

Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Služba analyzuje výkonu a stavu systému Windows a virtuální počítače s Linuxem, monitorování svých procesů a jejich závislosti na jiných zdrojů a externí procesů. 

Azure Monitor pro virtuální počítače jako řešení, zahrnuje podporu pro monitorování výkonu a používání závislosti virtuálních počítačů, které jsou hostované v místním prostředí nebo jiného zprostředkovatele cloudu. Tři klíčové funkce poskytovat podrobnější informace:

* **Logické součásti virtuálních počítačů Azure, který spustí Windows a Linuxem**: se měří podle kritérií stavu předem nakonfigurované a že vás upozornit, když je splněna podmínka Vyhodnocená.  

* **Předem definované, grafy výkonu vzroste zájem o**: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

* **Mapa závislostí**: zobrazí vzájemně propojených součástí virtuálního počítače z různých skupin prostředků a předplatných.  

Funkce jsou uspořádané do tří perspektiv:

* Stav
* Výkon
* Mapa

>[!NOTE]
>V současné době funkce stavu nabízí pouze pro Azure virtual machines a virtual machine scale sets. Výkon a mapování funkce podporují virtuální počítače Azure a virtuální počítače, které jsou hostované ve vašem prostředí nebo jiný poskytovatel cloudu.

Integraci s Log Analytics poskytuje výkonné agregace a filtrování, a to analyzovat trendy v datech v čase. Tyto úlohy komplexní monitorování není možné dosáhnout s Azure Monitor, řešení Service Map nebo Log Analytics samostatně.  

Tato data můžete zobrazit v jeden virtuální počítač z virtuálního počítače přímo, nebo Azure Monitor můžete použít k zajištění souhrnný náhled na vašich virtuálních počítačů. Toto zobrazení vychází z perspektivy jednotlivých funkcí:

* **Stav**: virtuální počítače se vztahují na skupinu prostředků.
* **Mapa** a **výkonu**: virtuální počítače jsou nakonfigurované k ukládání dat do konkrétního pracovního prostoru Log Analytics.

![Insights perspektivy virtuálního počítače na webu Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure DevOps dokáže zajistit předvídatelný výkon a dostupnost důležitých aplikací. Identifikuje událostí kritické operačního systému, kritické body výkonu a problémy se sítí. Azure DevOps můžete taky vám pomůžou pochopit, jestli problém souvisí s další závislosti.  

## <a name="data-usage"></a>Využití dat 

Při nasazení Azure Monitor pro virtuální počítače, data, která se shromažďují ve vašich virtuálních počítačů je ingestuje a uložené ve službě Azure Monitor. Na ceny, který je publikovaný na základě [stránce s cenami Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), účtuje se pro monitorování Azure pro virtuální počítače:
* Data, která má ingestují a ukládají.
* Počet metrik stavu kritéria, časových řad, která jsou monitorovaná.
* Pravidla výstrah, které jsou vytvořeny.
* Oznámení, které se odesílají. 

Velikost protokolu se liší podle délky řetězce čítačů a můžete zvýšit počet logických disků a síťových adaptérů. Pokud už máte pracovní prostor a tyto čítače shromažďují, jsou použity žádné duplicitní poplatky. Pokud už používáte řešení Service Map, je jediná změna, který se zobrazí data další připojení, která je odeslána do Azure monitoru.

## <a name="next-steps"></a>Další postup
Informace o tom požadavky a metody, které vám pomohou monitorovat virtuální počítače, projděte si [nasazení Azure Monitor pro virtuální počítače](vminsights-onboard.md).
