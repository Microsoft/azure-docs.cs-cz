---
title: Povolit Fusion v Azure Sentinel Preview | Microsoft Docs
description: Naučte se, jak povolit Fusion v Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780440"
---
# <a name="enable-fusion"></a>Povolení fúze

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning ve službě Azure Sentinel je integrovaná přímo na začátku. Thoughtfully jsme navrhli systém s inovacemi ML, jejichž cílem je umožnit analytikům zabezpečení, specialistům na zabezpečení dat a inženýrům produktivní produktivitu. Jednou z takových inovací je Azure Sentinel Fusion vytvořená hlavně k omezení únavy výstrah.

Fúze využívá algoritmy strojového učení založené na grafu ke vzájemnému sladění milionů neobvyklé aktivit s nižší věrností z různých produktů, jako jsou Azure AD Identity Protection, a Microsoft Cloud App Security, aby je bylo možné zkombinovat s spravovatelným počtem zajímavé incidenty zabezpečení.

## <a name="enable-fusion"></a>Povolení fúze

1. V Azure Portal vyberte ikonu pro otevření Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  V okně **Vítá vás Cloud Shell** okna, které se otevře, vyberte PowerShell.

3.  Vyberte předplatné, na které jste nasadili službu Azure Sentinel, a **vytvořte úložiště**.

4. Po ověření a vytvoření jednotky Azure na příkazovém řádku spusťte následující příkazy:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Zakázat Fusion

Postupujte podle výše uvedeného postupu a spusťte následující příkaz:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Zobrazit stav fúze

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak povolit Fusion v Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

