---
title: Připojení dat Cisco k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Cisco k Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588395"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Připojit Cisco ASA k Azure Sentinel



Tento článek vysvětluje, jak připojit zařízení Cisco ASA ke službě Azure Sentinel. Datový konektor Cisco ASA vám umožní snadno připojit protokoly Cisco ASA k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Cisco ASA v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Předejte protokoly Cisco ASA do agenta syslog.

Cisco ASA nepodporuje CEF, takže protokoly se odesílají jako syslog a Agent Azure Sentinel ví, jak je analyzovat, jako by to byly protokoly CEF. Nakonfigurujte Cisco ASA pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog:

1. Přejděte na [Odeslat zprávy syslog na externí server SYSLOG](https://aka.ms/asi-syslog-cisco-forwarding)a postupujte podle pokynů pro nastavení připojení. Po zobrazení výzvy použijte tyto parametry:
    - Nastavte **port** na 514 nebo na port, který jste nastavili v agentovi.
    - Nastavte **syslog_ip** na IP adresu agenta.

1. Pokud chcete použít příslušné schéma v Log Analytics pro události Cisco, vyhledejte `CommonSecurityLog`.

1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení Cisco ASA ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


