---
title: Nahraďte hardwarová součást v uzlu jednotky škálování Azure zásobníku | Microsoft Docs
description: Informace o výměně hardwarové součásti v systému Azure zásobníku integrované.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/29/2018
ms.author: mabrigg
ms.openlocfilehash: 7018f0122ab1ef11d64cce8a9adf58419d0e9ba7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919708"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Nahraďte hardwarová součást v uzlu jednotky škálování Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje obecný postup k nahrazení hardwarové součásti, které jsou bez za provozu. Skutečné nahrazení, který postup lišit podle výrobce (OEM) dodavatele hardwaru. Dokumentaci od dodavatele pole replaceable jednotka (FRU) podrobné kroky, které jsou specifické pro systém Azure zásobníku integrované.

Bez za provozu součásti zahrnují následující:

- CPU*
- Paměť *
- Řadič pro správu základní desky nebo základní desky (BMC) / video karty
- Disk řadiče nebo hostitelských adaptérů (HBA) nebo propojovací rozhraní systému
- Síťový adaptér (NIC)
- Operační systém disku *
- Datové jednotky (jednotky, které nepodporují aktivní odkládacího souboru, třeba PCI-e doplňku karty) *

* Tyto součásti můžou podporovat aktivní odkládacího souboru, ale mohou lišit v závislosti na implementaci dodavatele. Najdete v dokumentaci FRU vašeho OEM dodavatele podrobné pokyny.

Následující vývojový diagram znázorňuje proces obecné FRU nahradit bez za provozu hardwarová komponenta.

![Vývojový diagram zobrazující komponenty nahrazení toku](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Tato akce nemusí být vyžadovány na základě podmínky pro fyzický hardware.

** Jestli dodavatele hardwaru, od výrobců OEM provede nahrazení součásti a aktualizace firmwaru může lišit podle vaší smlouvě o podpoře.

## <a name="review-alert-information"></a>Přečtěte si informace o výstrahách

Stav zásobník Azure a systém monitorování sledovat stav síťových adaptérů a datových jednotek řízené prostory úložiště – přímé. Ho nesleduje jiných hardwarových součástí. Pro všechny ostatní hardwarové součásti jsou vyvolány výstrahy specifické pro dodavatele hardwaru řešení monitorování, které běží na hostiteli životního cyklu hardwaru.  

## <a name="component-replacement-process"></a>Proces nahrazení komponent

Následující kroky poskytují souhrnné informace o nahrazení proces komponent. Nepostupujte podle těchto kroků bez odkazující na dokumentaci FRU poskytnutou výrobcem OEM.

1. Použití [vyprazdňování](azure-stack-node-actions.md#scale-unit-node-actions) akce uvést do režimu údržby uzlu jednotky škálování. Tuto akci nelze vyžadovat na základě podmínky pro fyzický hardware.

   > [!NOTE]
   > V každém případě jenom jeden uzel vyprázdnit a vypnout ve stejnou dobu, aniž by vás S2D (prostory úložiště – přímé).

2. Po uzlu jednotka škálování je v režimu údržby, použijte [vypnutí](azure-stack-node-actions.md#scale-unit-node-actions) akce. Tuto akci nelze vyžadovat na základě podmínky pro fyzický hardware.

   > [!NOTE]
   > V případě nepravděpodobné, že vypnutí akce nepomůže použijte místo toho webové rozhraní základní desky management controller, (BMC).

3. Nahraďte poškozené hardwarová součást. Jestli dodavatele hardwaru, od výrobců OEM provádí nahrazení součást může lišit v závislosti na vaší smlouvě o podpoře.  
4. Aktualizace firmwaru. Postupujte podle svůj proces aktualizace firmwaru specifické pro dodavatele pomocí životního cyklu hostitelského hardwaru a ujistěte se, že nahrazené hardwarová součást má schválené firmware úroveň použít. Jestli dodavatele hardwaru, od výrobců OEM provádí tento krok může lišit v závislosti na vaší smlouvě o podpoře.  
5. Použití [oprava](azure-stack-node-actions.md#scale-unit-node-actions) akce tím uzlu jednotky škálování zpět do jednotky škálování.
6. Použít privilegovaný koncový bod pro [zkontrolujte stav virtuálního disku opravy](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Úlohu oprava úplné úložiště s nové datové jednotky, může trvat několik hodin v závislosti na zatížení systému a využívat místo.
7. Po dokončení akce opravy ověřte, že byly automaticky zavřeny všechny aktivní výstrahy.

## <a name="next-steps"></a>Další postup

- Informace o nahrazení za provozu fyzický disk najdete v tématu [Výměna disku](azure-stack-replace-disk.md).
- Informace o nahrazení do fyzického uzlu najdete v tématu [nahradit uzlu jednotky škálování](azure-stack-replace-node.md).
