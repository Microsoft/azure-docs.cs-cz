---
title: Připojit výstrahy zabezpečení Orca do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data výstrah zabezpečení Orca k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 735f4b447d7258a9b444f3b75a6537dec20b1307
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076269"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Připojte výstrahy zabezpečení Orca k Azure Sentinel 

> [!IMPORTANT]
> Konektor zabezpečení pro výstrahy zabezpečení nástroje Orca v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí nástroje Orca Security Alerts Connector můžete snadno přenést výstrahy řešení zabezpečení [Orca](https://orca.security/) do Azure Sentinel, abyste je mohli zobrazit v sešitech, používat je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření. Integrace mezi nástroji Orca Security Alerts a Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Konfigurace a připojení výstrah zabezpečení Orca

Výstrahy zabezpečení Orca můžou integrovat a exportovat protokoly přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** , vyberte možnost **Orca Security Alerts** a pak **otevřete stránku konektor**.

2. Chcete https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration -li dokončit integraci z platformy Orca, přečtěte si téma.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části **CustomLogs** v tabulce **OrcaAlerts_CL** .
Chcete-li použít příslušné schéma v Log Analytics pro výstrahy Orca, vyhledejte `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Ověřit připojení
Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak propojit výstrahy zabezpečení Orca s Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

