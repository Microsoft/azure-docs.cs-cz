---
title: Nahraďte uzlu jednotky škálování v systému Azure zásobníku integrované | Microsoft Docs
description: Zjistěte, jak nahradit uzlem škálování fyzické jednotky v systému Azure zásobníku integrované.
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
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 468af385833395963ef8acad905b99a9b7e6b8fa
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2018
ms.locfileid: "27598769"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Nahraďte uzlu jednotky škálování v systému Azure zásobníku integrované

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje obecný postup k nahrazení fyzického počítače (také označuje jako *uzlu jednotky škálování*) v zásobníku Azure integrované systému. Skutečné škálování jednotky uzlu nahrazení, který pokyny se budou lišit podle dodavatele hardwaru, výrobce (OEM). Dokumentaci od dodavatele pole replaceable jednotka (FRU) podrobné kroky, které jsou specifické pro systém.

Následující vývojový diagram znázorňuje obecný postup FRU nahradit do uzlu škálování celé jednotky.

![Vývojový diagram procesu uzlu nahradit](media/azure-stack-replace-node/replacenodeflow.png)

* Tato akce nemusí být vyžadovány na základě podmínky pro fyzický hardware.

## <a name="review-alert-information"></a>Přečtěte si informace o výstrahách

Pokud uzel jednotka škálování je vypnutý, dostanete následující kritické výstrahy:

- Uzel není připojeno k síťový adaptér
- Uzel nedostupný pro umístění virtuálního počítače
- Uzel jednotka škálování je offline

![Seznam výstrah pro jednotku škálování směrem dolů](media/azure-stack-replace-node/nodedownalerts.png)

Pokud otevřete **uzel jednotka škálování je offline** výstrah, popis výstrahy obsahuje uzlu jednotek škálování, který je nedostupná. Může se také zobrazit další výstrahy v řešení pro monitorování výrobce OEM, která běží na hostiteli životního cyklu hardwaru.

![Podrobnosti výstrahy offline uzlu](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Proces nahrazení uzlu jednotky škálování

Následující kroky jsou uvedeny jako přehled procesu nahrazení uzlu jednotky škálování. Naleznete v dokumentaci dodavatele hardwaru OEM FRU k podrobné kroky, které jsou specifické pro systém. Nepostupujte podle těchto kroků bez odkazující na dokumentaci poskytnutou výrobcem OEM.

1. Použití [vyprazdňování](azure-stack-node-actions.md#scale-unit-node-actions) akce uvést do režimu údržby uzlu jednotky škálování. Tuto akci nelze vyžadovat na základě podmínky pro fyzický hardware.

   > [!NOTE]
   > V každém případě jenom jeden uzel vyprázdnit a vypnout ve stejnou dobu, aniž by vás S2D (prostory úložiště – přímé).

2. Pokud uzel je pořád zapnutý, použijte [vypnutí](azure-stack-node-actions.md#scale-unit-node-actions) akce. Tuto akci nelze vyžadovat na základě podmínky pro fyzický hardware.
 
   > [!NOTE]
   > V případě nepravděpodobné, že vypnutí akce nepomůže použijte místo toho webové rozhraní základní desky management controller, (BMC).

1. Nahraďte fyzického počítače. Obvykle k tomu je potřeba dodavatele hardwaru, od výrobců OEM.
2. Použití [Repair](azure-stack-node-actions.md#scale-unit-node-actions) akci chcete přidat nový fyzický počítač do jednotky škálování.
3. Použít privilegovaný koncový bod pro [zkontrolujte stav virtuálního disku opravy](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Úlohu oprava úplné úložiště s nové datové jednotky, může trvat několik hodin v závislosti na zatížení systému a využívat místo.
4. Po dokončení akce opravy ověřte, že byly automaticky zavřeny všechny aktivní výstrahy.

## <a name="next-steps"></a>Další postup

- Informace o nahrazení za provozu fyzický disk najdete v tématu [Výměna disku](azure-stack-replace-disk.md). 
- Informace o nahrazení bez za provozu hardwarová komponenta najdete v tématu [výměně hardwarové součásti](azure-stack-replace-component.md).
