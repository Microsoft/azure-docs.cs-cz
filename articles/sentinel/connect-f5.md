---
title: Připojení dat F5 k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data F5 k Azure Sentinelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588259"
---
# <a name="connect-f5-to-azure-sentinel"></a>Připojení F5 k Azure Sentinelu

Tento článek vysvětluje, jak připojit zařízení F5 k Azure Sentinelu. Datový konektor F5 umožňuje snadno připojit protokoly F5 k Azure Sentinelu, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Použití F5 v Azure Sentinelu vám poskytne další přehled o využití internetu vaší organizace a zlepší možnosti operací zabezpečení. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Konfigurace f5 pro odesílání zpráv CEF

1. Přejděte na [web F5 Konfigurace protokolování událostí zabezpečení aplikací](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)a podle pokynů nastavte vzdálené protokolování podle následujících pokynů:
   - Nastavte **typ vzdáleného úložiště** na **Nástroj pro cef**.
   - Nastavte **protokol** na **tcp**.
   - Nastavte **ip adresu** na ip adresu serveru Syslog.
   - Nastavte **číslo portu** na **514**nebo port, který jste nastavili agenta.
   - Maximální velikost **řetězce dotazu** můžete nastavit na velikost nastavenou v agentovi.

1. Chcete-li použít příslušné schéma v log analytics pro události `CommonSecurityLog`CeF, vyhledejte .

1. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit F5 k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

