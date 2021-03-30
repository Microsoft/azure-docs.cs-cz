---
title: Připojit data kontrolního bodu ke službě Azure Sentinel | Microsoft Docs
description: Nakonfigurujte zařízení kontrolního bodu tak, aby předalo zprávy syslog ve formátu CEF do pracovního prostoru Azure Sentinel prostřednictvím agenta syslog.
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
ms.openlocfilehash: 32cbdabef4d89c7fabb47a52ebf0589690b7ab33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566058"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Připojení kontrolního bodu k Azure Sentinel



Tento článek vysvětluje, jak propojit vaše zařízení se kontrolním bodem do Azure Sentinel. Datový konektor pro kontrolní body vám umožní snadno připojit protokoly kontrolních bodů ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití kontrolního bodu na Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Přesměrovat protokoly kontrolního bodu do agenta syslog

Nakonfigurujte zařízení kontrolního bodu tak, aby předalo zprávy syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Přejděte na [Export protokolu Check Point](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Přejděte dolů k **základnímu nasazení** a postupujte podle pokynů pro nastavení připojení, a to podle následujících pokynů:
   - Nastavte **port SYSLOG** na **514** nebo port, který jste nastavili v agentovi.
     - V rozhraní příkazového řádku nahraďte **název a** **IP adresu cílového serveru** pomocí názvu agenta syslog a IP adresy.
     - Nastavte formát na **CEF**.
1. Pokud používáte verzi R 77.30 nebo R 80.10, posuňte se k **instalacím** a postupujte podle pokynů pro instalaci exportéra protokolu pro vaši verzi.
1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).
 

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení s kontrolním bodem ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- [Ověřte připojení](connect-cef-verify.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


