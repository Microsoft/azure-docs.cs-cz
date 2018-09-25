---
title: Co je Azure Monitor pro virtuální počítače? | Dokumenty Microsoft
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
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 26fcc3eb78af53360cca57382b4c06b017f36c0e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063264"
---
# <a name="what-is-azure-monitor-for-vms"></a>Co je Azure Monitor pro virtuální počítače?

Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) ve velkém měřítku prostřednictvím analýzy výkonu a stavu Windows a virtuální počítače s Linuxem, včetně jejich různé procesy a propojených záviset na jiných prostředcích a externí proces. Toto řešení zahrnuje podporu monitorování výkonu a závislostí aplikací pro virtuální počítače hostované v místním nebo jiného zprostředkovatele cloudu.  Zahrnuje tři hlavní funkce poskytovat tento podrobný přehled Díky uloženým:

* Logické součásti virtuálních počítačů Azure s operačním systémem Windows a Linux se měří podle sadu kritérií předem nakonfigurované stavu a výstrahy, když je splněna podmínka Vyhodnocená.  
* Základní metriky výkonu z procesoru, paměti, disku a síťový adaptér operačního systému hosta virtuálního počítače se shromažďují a uvedené v předem definované populárních grafy výkonu.
* Mapa závislostí zobrazení zjištěných vzájemně propojených součástí tohoto virtuálního počítače z více skupin prostředků a předplatných.  

Tyto funkce jsou uspořádané do tří perspektiv:

* Stav
* Výkon
* Mapa

>[!NOTE]
>Stav funkce je v současné době nabízí jenom pro virtuální počítače Azure.
>

Integraci s Log Analytics poskytuje výkonné agregace, filtrování a schopnost provádět analýzy trendů z dat v čase. Komplexní monitorování vaší úlohy není možné dosáhnout samostatně pomocí Azure monitoru, řešení Service Map nebo Log Analytics.  

Tato data můžete zobrazit v rámci jednoho virtuálního počítače z virtuálního počítače přímo nebo prostřednictvím služby Azure Monitor nabízí souhrnný náhled na vašich virtuálních počítačů založených na následující perspektivy pro jednotlivé funkce:

* Stav – související virtuální počítače do skupiny prostředků
* Mapy a výkonu – virtuální počítače nakonfigurované pro sestavy pro konkrétní pracovní prostor Log Analytics

![Insights perspektivy virtuálního počítače z portálu](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps můžete efektivně poskytovat předvídatelný výkon a dostupnost důležitých aplikací díky identifikaci událostí kritické operačního systému a problémových míst výkonu, problémy se sítí a vysvětlení, pokud se problém souvisí s další závislosti.  

## <a name="data-usage"></a>Využití dat 

Jakmile je připojení Azure Monitor pro virtuální počítače, data shromážděná z virtuálních počítačů ingestuje a uložené ve službě Azure Monitor.  Azure Monitor pro virtuální počítače se účtuje data ingestují a zachován, různých kritérií stavu metriky časových řad monitorovaných, upozornění pravidla vytvořit, odesílat oznámení, ceny za publikování na Azure Monitor [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/)

Velikost protokolu se liší v závislosti na řetězec délky čítačů a zvýšit počet logických disků a síťových adaptérů.  Pokud už máte pracovní prostor a shromažďování těchto čítačů, nebudou existovat žádné duplicitní poplatky.  Pokud už používáte řešení Service Map, je jediná změna, kterou uvidíte další připojení dat odesílaných do Azure monitoru.

## <a name="next-steps"></a>Další postup
Kontrola [připojení Azure Monitor pro virtuální počítače](monitoring-vminsights-onboard.md) vám pomohou pochopit požadavky a metody, pokud chcete povolit monitorování virtuálních počítačů.
