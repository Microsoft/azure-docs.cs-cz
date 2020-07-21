---
title: Připojit výstrahy Orca k Azure Sentinel | Microsoft Docs
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
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531299"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Připojte svoje výstrahy Orca k Azure Sentinel 

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

