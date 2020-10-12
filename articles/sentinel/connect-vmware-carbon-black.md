---
title: Připojit standardní data koncového bodu VMware uhlí Black do Azure Sentinel | Microsoft Docs
description: Naučte se připojit standardní data koncového bodu VMware z černého cloudu do Azure Sentinel.
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
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097069"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Připojení koncového bodu standardu VMware z černého cloudu ke službě Azure Sentinel pomocí funkce Azure Functions

> [!IMPORTANT]
> Standardní datový konektor koncového bodu VMware pro uhlí v Azure je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Standardní konektor koncového bodu VMware pro Azure uhlí vám umožňuje snadno připojit všechna vaše protokolová řešení [VMware pro standardní koncové body standardního](https://www.carbonblack.com/products/endpoint-standard/) zabezpečení pomocí Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi VMware uhlí černou a službou Azure Sentinel využívá Azure Functions k vyžádání dat protokolu pomocí REST API.


> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Konfigurace a připojení ke koncovému bodu standardu VMware uhlí černého cloudu

Azure Functions můžou integrovat a přijímat události a protokoly přímo ze standardního koncového bodu VMware uhlí černého cloudu a předají je do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **VMware uhlí Black** Connector.
2. Vyberte **stránku otevřít konektor**.
3. Postupujte podle pokynů na **černé stránce VMware uhlí** .


## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v tabulkách **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** a * * * * CarbonBlackEvents_CL * * * *.

## <a name="validate-connectivity"></a>Ověřit připojení
Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak pomocí aplikací Azure Functions připojit koncový bod standardu VMware z černého cloudu ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

