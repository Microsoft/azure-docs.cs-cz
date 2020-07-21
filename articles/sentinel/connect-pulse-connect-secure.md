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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531179"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Připojení Pulse Connect Secure k Azure Sentinel

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