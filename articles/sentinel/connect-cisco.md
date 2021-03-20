---
title: Připojení dat Cisco k Azure Sentinel | Microsoft Docs
description: Naučte se připojit zařízení Cisco ASA ke službě Azure Sentinel a zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření.
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
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85566032"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Připojit Cisco ASA k Azure Sentinel



Tento článek vysvětluje, jak připojit zařízení Cisco ASA ke službě Azure Sentinel. Datový konektor Cisco ASA vám umožní snadno připojit protokoly Cisco ASA k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Cisco ASA v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Předejte protokoly Cisco ASA do agenta syslog.

Cisco ASA nepodporuje CEF, takže protokoly se odesílají jako syslog a Agent Azure Sentinel ví, jak je analyzovat, jako by to byly protokoly CEF. Nakonfigurujte Cisco ASA pro přeposílání zpráv syslog do pracovního prostoru Azure prostřednictvím agenta syslog:

1. Přejděte na [Odeslat zprávy syslog na externí server SYSLOG](https://aka.ms/asi-syslog-cisco-forwarding)a postupujte podle pokynů pro nastavení připojení. Po zobrazení výzvy použijte tyto parametry:
    - Nastavte **port** na 514 nebo na port, který jste nastavili v agentovi.
    - Nastavte **syslog_ip** na IP adresu agenta.

1. Pokud chcete použít příslušné schéma v Log Analytics pro události Cisco, vyhledejte `CommonSecurityLog` .

1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení Cisco ASA ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


