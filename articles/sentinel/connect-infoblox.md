---
title: Připojit data operačního systému Infoblox Network identity (NIOS) do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data operačního systému Infoblox Network identity (NIOS) do Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531263"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Připojení Infoblox NIOS k Azure Sentinel

Tento článek vysvětluje, jak připojit [zařízení s operačním systémem Infoblox Network identity (NIOS)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) ke službě Azure Sentinel. Datový konektor Infoblox NIOS umožňuje snadno připojit protokoly Infoblox k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi Infoblox NIOS a službou Azure Sentinel využívá protokol syslog.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Přeposílání protokolů Infoblox do agenta syslog  

Nakonfigurujte Infoblox pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte konektor **Infoblox NIOS** .

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **INFOBLOX NIOS** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části syslog.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Infoblox NIOS ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.