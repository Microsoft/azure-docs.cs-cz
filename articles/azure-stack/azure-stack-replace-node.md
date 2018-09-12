---
title: Nahraďte uzlu jednotek škálování v systémech pro Azure Stack integrované | Dokumentace Microsoftu
description: Zjistěte, jak k nahrazení uzlu fyzické škálovací jednotku v systémech pro Azure Stack integrované.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 1b37b150dad4951a4ade81f226b515ce9cae9053
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377050"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Nahraďte uzlu jednotek škálování v systémech pro Azure Stack integrované

*Platí pro: integrované systémy Azure Stack*

Tento článek popisuje obecný postup k nahrazení fyzického počítače (také označuje jako *uzel jednotek škálování*) v Azure stacku integrovaný systém. Skutečné škálovací jednotku uzel nahrazení, kroky budou lišit podle dodavatele hardwaru, výrobce OEM (OEM). Dokumentaci od dodavatele pole vyměnitelná jednotka (FRU) podrobné pokyny, které jsou specifické pro váš systém.

Následující vývojový diagram ukazuje obecné FRU procesu k nahrazení uzlu celou škálovací jednotku.

![Vývojový diagram pro proces nahrazení uzlu](media/azure-stack-replace-node/replacenodeflow.png)

* Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

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

1. Použití [vyprázdnit](azure-stack-node-actions.md#scale-unit-node-actions) akce Vložit škálovací jednotku uzel do režimu údržby. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.

   > [!NOTE]
   > V každém případě pouze jeden uzel vyprázdnit a vypnutý ve stejnou dobu, aniž by vás to S2D (prostory úložiště – přímé).

2. Pokud uzel je pořád zapnutý, použijte [vypnutí](azure-stack-node-actions.md#scale-unit-node-actions) akce. Tato akce nemusí být vyžadovány na základě podmínky fyzického hardwaru.
 
   > [!NOTE]
   > V nepravděpodobném případě vypnout akce nefunguje místo toho použijte webové rozhraní řadič pro správu základní desky.

1. Nahraďte fyzického počítače. Obvykle je to váš dodavatel hardwaru výrobce OEM.
2. Použití [opravit](azure-stack-node-actions.md#scale-unit-node-actions) akce pro přidání nového fyzického počítače na jednotce škálování.
3. Použít privilegovaný koncový bod pro [zkontrolovat stav oprava virtuálního disku](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Úloha opravy celé úložiště pomocí nové datové jednotky, může trvat několik hodin v závislosti na zatížení systému a využité místo.
4. Po dokončení akce opravy, ověřte, že byly automaticky zavřeny všechny aktivní výstrahy.

## <a name="next-steps"></a>Další postup

- Informace o výměně za chodu vyměnitelné fyzického disku, najdete v části [Výměna disku](azure-stack-replace-disk.md). 
- Informace o nahrazení bez za provozu hardwarová komponenta najdete v tématu [vyměňovat hardwarové součásti](azure-stack-replace-component.md).
