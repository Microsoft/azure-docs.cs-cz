---
title: Připojení dat Symantec ProxySG ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Symantec ProxySG ke službě Azure Sentinel.
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
ms.openlocfilehash: 902475ae3e60761fb30620c5ba2fa8cbd905c916
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099179"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Připojení Symantec ProxySG k Azure Sentinel

> [!IMPORTANT]
> Datový konektor Symantec ProxySG ve službě Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit zařízení [Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) ke službě Azure Sentinel. Data Connector Symantec ProxySG vám umožní snadno připojit protokoly Symantec ProxySG ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi Symantec ProxySG a Azure Sentinel využívá syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Předejte protokoly Symantec ProxySG do agenta syslog.  

Nakonfigurujte Symantec ProxySG pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Symantec ProxySG** Connector.

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **Symantec ProxySG** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části syslog.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Symantec ProxySG ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.