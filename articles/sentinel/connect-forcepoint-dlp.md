---
title: Připojení ochrany před únikem informací Forcepoint ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Forcepoint DLP ke službě Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 62ed3915dcaf596d144a2f59817626cdf8ec47e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092770"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Připojení ochrany před únikem informací z Forcepoint k Azure Sentinel

> [!IMPORTANT]
> Datový konektor ochrany před únikem informací Forcepoint v Azure Sentinel je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Konektor Forcepoint pro ochranu před únikem informací umožňuje automaticky exportovat data incidentu DLP do Azure Sentinel. Můžete ji použít k získání přehledu o aktivitách uživatelů a událostech ztráty dat. Umožňuje taky korelace s daty z úloh Azure a dalšími informačními kanály a vylepšuje možnosti monitorování pomocí sešitů v rámci Azure Sentinel.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Konfigurace a připojení ochrany před únikem informací Forcepoint

Nakonfigurujte ochranu před únikem informací Forcepoint pro přeposílání dat incidentů ve formátu JSON do pracovního prostoru Azure prostřednictvím REST API, jak je vysvětleno v [Průvodci integrací FORCEPOINT DLP](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Hledání dat

Po nastavení konektoru Forcepoint pro ochranu před únikem informací se data zobrazí v Log Analytics **ForcepointDLPEvents_CL** CustomLogs.


Pokud chcete použít příslušné schéma v Log Analytics pro Forcepoint DLP, vyhledejte **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Forcepoint DLP ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.