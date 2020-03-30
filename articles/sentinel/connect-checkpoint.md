---
title: Připojení dat kontrolního bodu k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data kontrolního bodu k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588412"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Connect Check Point k Azure Sentinelu



Tento článek vysvětluje, jak připojit zařízení Kontrolní bod k Azure Sentinelu. Datový konektor Kontrolní bod umožňuje snadno připojit protokoly kontrolního bodu s Azure Sentinelem, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Použití kontrolního bodu v Azure Sentinelu vám poskytne další přehled o využití internetu vaší organizace a zlepší možnosti operací zabezpečení. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Protokoly dopředný bod kontroly agentovi Syslog

Nakonfigurujte zařízení Check Point tak, aby přesměrovávalo zprávy Syslogu ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta Syslog.

1. Přejděte na [export protokolu kontrolních bodů](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Přejděte dolů na **základní nasazení** a podle pokynů nastavte připojení podle následujících pokynů:
   - Nastavte **port Syslog** na **514** nebo port, který jste nastavili u agenta.
     - Nahraďte adresu IP **názvu** a **cílového serveru** v zapisování/k. zařazování a protokolem Syslog názvem a adresou IP.
     - Nastavte formát na **CEF**.
1. Pokud používáte verzi R77.30 nebo R80.10, přejděte nahoru na **instalace** a podle pokynů nainstalujte log exportér pro vaši verzi.
1. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).
 

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení Kontrolní bod k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- [Ověřte připojení](connect-cef-verify.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


