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
ms.openlocfilehash: c8af1c32607e5fc7bf548b40069e18923c666303
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089647"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Připojení Infoblox NIOS k Azure Sentinel

> [!IMPORTANT]
> Datový konektor Infoblox NIOS v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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