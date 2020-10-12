---
title: Připojení Pulse Connect Secure data do Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Pulse Connect zabezpečená data k Azure Sentinel.
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
ms.openlocfilehash: f7f6de717f0ed9a2e27e15199b6f0ea566e0fdcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021626"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Připojení Pulse Connect Secure k Azure Sentinel

> [!IMPORTANT]
> Konektor zabezpečení dat Pulse Connect v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit [zabezpečovací zařízení Pulse Connect](https://www.pulsesecure.net/products/pulse-connect-secure/) ke službě Azure Sentinel. Konektor pro zabezpečení dat Pulse Connect umožňuje snadno připojit zabezpečené protokoly Pulse Connect k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi pulsovým připojením Secure a službou Azure Sentinel využívá protokol syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Dopředný Pulse připojit zabezpečené protokoly k agentovi syslog  

Nakonfigurujte Pulse Connect Secure pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Pulse Connect Secure** Connector.

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **zabezpečení Pulse Connect** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části syslog.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit Pulse Connect Secure k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.