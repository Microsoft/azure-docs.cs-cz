---
title: Připojit data F5 ASM ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor F5 ASM pro stažení protokolů ASM z F5 do Azure Sentinel. Zobrazit F5 data ASM v sešitech, vytvořit výstrahy a vylepšit šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655693"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Připojit F5 ASM k Azure Sentinel

V tomto článku se dozvíte, jak pomocí konektoru nástroje F5 ASM snadno načíst do služby Azure Sentinel protokoly z F5. To vám umožní zobrazit data F5 ASM v sešitech, použít je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření. Data v nástroji F5 ASM v Azure Sentinel vám poskytnou více informací o zabezpečení webových aplikací vaší organizace a vylepší možnosti vašich operací zabezpečení. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Konfigurace kódu F5 pro posílání zpráv CEF

1. Podle pokynů v tématu [F5 konfigurace protokolování událostí zabezpečení aplikací](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) nastavte vzdálené protokolování, a to podle následujících pokynů:
   - Nastavte **typ vzdáleného úložiště** na **CEF**.
   - Nastavte **protokol** na **TCP**.
   - Nastavte **IP adresu** na IP adresu serveru syslog.
   - Nastavte **číslo portu** na **514** nebo port, který nastavíte pro použití v agentovi.
   - Můžete nastavit **maximální velikost řetězce dotazu** na velikost, kterou jste nastavili v agentovi.

1. Pokud chcete použít příslušné schéma v Log Analytics pro události CEF, vyhledejte `CommonSecurityLog` .

1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit F5 ASM ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.