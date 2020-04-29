---
title: Připojit data F5 ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit data F5 ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588259"
---
# <a name="connect-f5-to-azure-sentinel"></a>Připojit F5 ke službě Azure Sentinel

Tento článek vysvětluje, jak připojit zařízení F5 ke službě Azure Sentinel. Konektor pro data F5 umožňuje snadno připojit protokoly F5 pomocí funkce Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití F5 v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Konfigurace F5 pro posílání zpráv CEF

1. Přejděte na [F5 konfigurace protokolování událostí zabezpečení aplikace](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)a podle pokynů nastavte vzdálené protokolování, a to podle následujících pokynů:
   - Nastavte **typ vzdáleného úložiště** na **CEF**.
   - Nastavte **protokol** na **TCP**.
   - Nastavte **IP adresu** na IP adresu serveru syslog.
   - Nastavte **číslo portu** na **514**nebo port, který nastavíte pro použití v agentovi.
   - Můžete nastavit **maximální velikost řetězce dotazu** na velikost, kterou jste nastavili v agentovi.

1. Pokud chcete použít příslušné schéma v Log Analytics pro události CEF, vyhledejte `CommonSecurityLog`.

1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit F5 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

