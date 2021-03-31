---
title: Připojení dat o virtuálních IP adresách společnosti Symantec do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data o virtuálních IP adresách z Symantecu do Azure Sentinel.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090407"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Připojení virtuální IP adresy Symantec k Azure Sentinel

> [!IMPORTANT]
> Datový konektor Symantec VIP v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit zařízení se systémem [Symantec VIP](https://vip.symantec.com/) k Azure Sentinel. Datový konektor Symantec VIP vám umožní snadno připojit protokoly VIP VIP k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi virtuálními IP adresami Symantec a službou Azure Sentinel využívá protokol syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Předejte protokoly VIP společnosti Symantec do agenta syslog.  

Nakonfigurujte virtuální IP adresu Symantec pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Symantec VIP** Connector.

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **VIP** s certifikátem Symantec.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části syslog.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak propojit protokoly VIP společnosti Symantec do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.