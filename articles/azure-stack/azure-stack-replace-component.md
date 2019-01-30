---
title: Vyměňovat hardwarové součásti uzlu jednotek škálování v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak vyměňovat hardwarové součásti v systému Azure Stack integrované.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 7133c9de08a3a4f46bf8f2cc5f2de863f8efe57a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248474"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Vyměňovat hardwarové součásti uzlu jednotek škálování v Azure stacku

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obecný postup k nahrazení hardwarové součásti, které jsou mimo za provozu. Skutečné nahrazení, který se postup lišit podle dodavatele hardwaru, výrobce OEM (OEM). Konkrétní podrobný postup pro váš integrovaný systém Azure Stack najdete v dokumentaci k jednotce nahraditelné v terénu od vašeho dodavatele.

Bez za provozu součásti zahrnují následující:

- PROCESOR *
- Paměť *
- Řadič pro správu základní desky/základní desky (BMC) / video karty
- Disk řadiče/hostitelský adaptér (HBA) / propojovací rozhraní systému
- Síťový adaptér (NIC)
- Disk operačního systému *
- Datové jednotky (jednotek, které nepodporují horké odkládacího souboru, například PCI-e – doplněk karty) *

* Těchto komponent může podporovat horké prohození, ale může lišit v závislosti na implementaci dodavatelem. Naleznete v dokumentaci od výrobce OEM FRU podrobné pokyny.

Následující vývojový diagram ukazuje obecné FRU proces, který nahradí bez za provozu hardwarová komponenta.

![Vývojový diagram znázorňující tok nahrazení komponenty](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

**, Jestli OEM dodavatele hardwaru provede nahrazení komponenty a aktualizace firmwaru může lišit podle vašich smlouvu o podpoře.

## <a name="review-alert-information"></a>Projděte si informace o výstrahách

Stav služby Azure Stack a systém monitorování sledují stav síťových adaptérů a datové jednotky řízeny prostory úložiště – přímé. Sledovat další hardwarové komponenty. Pro všechny další hardwarové komponenty se generují výstrahy do specifického pro dodavatele hardwaru řešení monitorování, které běží na hostiteli životního cyklu hardwaru.  

## <a name="component-replacement-process"></a>Proces nahrazení komponent

Následující kroky obsahují podrobný přehled proces nahrazení komponenty. Nepostupujte podle těchto kroků bez ohledu na dokumentaci k FRU poskytnutou výrobcem OEM.

1. Použijte akci vypnutí řádně vypnout uzel jednotek škálování. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

2. V nepravděpodobném případě, která akci vypnutí nezdaří, použijte [vyprázdnit](azure-stack-node-actions.md#drain) akce Vložit škálovací jednotku uzel do režimu údržby. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

   > [!NOTE]  
   > V každém případě můžete jenom jeden uzel zakázána a vypnutý ve stejnou dobu, aniž by vás to S2D (prostory úložiště – přímé).

3. Po uzlu škálovací jednotku je v režimu údržby, použijte [vypnutí](azure-stack-node-actions.md#scale-unit-node-actions) akce. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

   > [!NOTE]  
   > V nepravděpodobném případě vypnout akce nefunguje místo toho použijte webové rozhraní řadič pro správu základní desky.

4. Nahraďte poškozený hardwarová komponenta. Zda OEM dodavatele hardwaru provede nahrazení komponenty může lišit v závislosti na vaší smlouvu o podpoře.  
5. Aktualizace firmwaru. Postupujte podle váš proces aktualizace firmwaru specifického pro dodavatele pomocí životního cyklu hostitelů hardware zajistěte, aby že nahradil hardwarová komponenta byla schválené firmware úroveň použít. Zda OEM dodavatele hardwaru provádí tento krok může lišit v závislosti na vaší smlouvu o podpoře.  
6. Použití [opravit](azure-stack-node-actions.md#scale-unit-node-actions) akce vrací do stavu uzlu jednotky škálování jednotek škálování.
7. Použít privilegovaný koncový bod pro [zkontrolovat stav oprava virtuálního disku](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Úloha opravy celé úložiště pomocí nové datové jednotky, může trvat několik hodin v závislosti na zatížení systému a využité místo.
8. Po dokončení akce opravy, ověřte, že byly automaticky zavřeny všechny aktivní výstrahy.

## <a name="next-steps"></a>Další postup

- Informace o výměně za chodu vyměnitelné fyzického disku, najdete v části [Výměna disku](azure-stack-replace-disk.md).
- Informace o nahrazení fyzického uzlu, naleznete v tématu [nahraďte uzel jednotek škálování](azure-stack-replace-node.md).
