---
title: Povolit fusion ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak povolit fusion v ověřovacích Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55d569d4a993a725137d7bfab37c113147fe81ef
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242458"
---
# <a name="enable-fusion"></a>Povolení fúze

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning v Azure Sentinelu jsou integrované přímo od začátku. Vytvoříte promyšlené jsme vytvořili v systému s ML inovace zaměřené na produktivitu analytikům zabezpečení, zabezpečení dat vědcům a inženýrům. Jeden takový inovace je že Fusion Sentinelu Azure vytvořené především ke snížení reorganizovaly výstrah.

Fusion používá ke koordinaci mezi miliony nižší přesnosti neobvyklé aktivity z různých produktů, jako je Azure AD Identity Protection a Microsoft Cloud App Security, je zkombinovat do několika spravovatelných grafu s využitím algoritmů strojového učení zajímavé případů zabezpečení.

## <a name="enable-fusion"></a>Povolení fúze

1. Na webu Azure Portal vyberte ikonu a otevřete Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  V **Vítejte v Cloud Shellu** systému windows, které se otevře níže, vyberte prostředí PowerShell.

3.  Vyberte předplatné, na které jste nasadili Azure Sentinelu, a **vytvořit úložiště**.

4. Poté, co jste ověřeni a váš disk Azure je postavená na příkazovém řádku spusťte následující příkazy:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Zakázat fusion

Postupujte stejným způsobem, jak je uvedeno výše a spusťte následující příkaz:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Zobrazení stavu fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak povolit Fusion v ověřovacích Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

