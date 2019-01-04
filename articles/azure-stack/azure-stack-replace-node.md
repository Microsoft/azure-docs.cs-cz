---
title: Nahraďte uzlu jednotek škálování v systémech pro Azure Stack integrované | Dokumentace Microsoftu
description: Zjistěte, jak k nahrazení uzlu fyzické škálovací jednotku v systémech pro Azure Stack integrované.
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
ms.openlocfilehash: 9d53aa879c39eb68597a402133a7ff16737f4f65
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716306"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Nahraďte uzlu jednotek škálování v systémech pro Azure Stack integrované

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obecný postup k nahrazení fyzického počítače (také označované jako škálovací jednotky uzel) v systémech pro Azure Stack integrované. Skutečné škálovací jednotku uzel nahrazení, kroky budou lišit podle dodavatele hardwaru, výrobce OEM (OEM). Dokumentaci od dodavatele pole vyměnitelná jednotka (FRU) podrobné pokyny, které jsou specifické pro váš systém.

Následující vývojový diagram ukazuje obecné FRU procesu k nahrazení uzlu celou škálovací jednotku.

![Vývojový diagram pro proces nahrazení uzlu](media/azure-stack-replace-node/replacenodeflow.png)

* Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

> [!Note]  
> Pokud se nezdaří operace vypnutí, se doporučuje použití operace vyprazdňování způsoben stop. Další podrobnosti najdete v uzlu dostupné operace  

## <a name="review-alert-information"></a>Projděte si informace o výstrahách

Pokud uzel jednotka škálování je vypnutý, dostanete následujícím kritické výstrahy:

- Uzel není připojen do síťového adaptéru
- Uzel nedostupný pro umístění virtuálních počítačů
- Uzel jednotka škálování je offline

![Seznam výstrah pro jednotku škálování dolů](media/azure-stack-replace-node/nodedownalerts.png)

Pokud otevřete **Škálovací jednotku uzel je offline** výstrahy, popis výstrahy obsahuje uzel jednotek škálování, který je nedostupný. V řešení pro monitorování specifické pro výrobce OEM, na kterém běží na hostiteli životního cyklu hardwaru také může zobrazit další výstrahy.

![Podrobnosti o uzlu offline upozornění](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Proces nahrazení uzlu jednotky škálování

Následující kroky jsou k dispozici jako základní přehled o proces nahrazení uzlu jednotek škálování. Najdete v dokumentaci dodavatele hardwaru pro výrobce OEM FRU podrobné pokyny, které jsou specifické pro váš systém. Nepostupujte podle těchto kroků bez ohledu na dokumentaci k poskytnutou výrobcem OEM.

1. Použití **vypnutí** akce řádně vypnout uzel jednotek škálování. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru. 

2. Nepravděpodobné, nastavte malá a velká nezdaří akce vypnutí, použít [vyprázdnit](azure-stack-node-actions.md#drain) akce Vložit škálovací jednotku uzel do režimu údržby. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

   > [!NOTE]  
   > V každém případě můžete jenom jeden uzel zakázána a vypnutý ve stejnou dobu, aniž by vás to S2D (prostory úložiště – přímé).

3. Po uzlu škálovací jednotku je v režimu údržby, použijte [Zastavit](azure-stack-node-actions.md#stop) akce. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

   > [!NOTE]  
   > V nepravděpodobném případě vypnout akce nefunguje místo toho použijte webové rozhraní řadič pro správu základní desky.

4. Nahraďte fyzického počítače. Obvykle je to váš dodavatel hardwaru výrobce OEM.
5. Použití [opravit](azure-stack-node-actions.md#repair) akce pro přidání nového fyzického počítače na jednotce škálování.
6. Použít privilegovaný koncový bod pro [zkontrolovat stav oprava virtuálního disku](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Úloha opravy celé úložiště pomocí nové datové jednotky, může trvat několik hodin v závislosti na zatížení systému a využité místo.
7. Po dokončení akce opravy, ověřte, že byly automaticky zavřeny všechny aktivní výstrahy.

## <a name="next-steps"></a>Další postup

- Informace o nahrazení fyzického disku, když je systém zapnutý, naleznete v tématu [Výměna disku](azure-stack-replace-disk.md). 
- Informace o nahrazení hardwarová komponenta, která je potřeba další systém vypnout napájení najdete v tématu [vyměňovat hardwarové součásti](azure-stack-replace-component.md).
