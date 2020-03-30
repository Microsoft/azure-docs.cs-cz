---
title: Připojení dat Cisco k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data Cisco k Azure Sentinelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588395"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Připojení asa společnosti Cisco k Azure Sentinelu



Tento článek vysvětluje, jak připojit zařízení Cisco ASA k Azure Sentinelu. Datový konektor Cisco ASA umožňuje snadno propojit protokoly Cisco ASA s Azure Sentinelem, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Použití cisco ASA v Azure Sentinelu vám poskytne další přehled o využití internetu vaší organizace a zlepší možnosti operací zabezpečení. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Předat protokoly Cisco ASA agentovi Syslog

Cisco ASA nepodporuje nástroj ProCeF, takže protokoly jsou odesílány jako Syslog a agent Azure Sentinel ví, jak je analyzovat, jako by se jedná o protokoly cef. Nakonfigurujte cisco ASA pro předávání zpráv Syslog do pracovního prostoru Azure prostřednictvím agenta Syslog:

1. Přejděte na [odeslat zprávy Syslog na externí server Syslog](https://aka.ms/asi-syslog-cisco-forwarding)a podle pokynů nastavte připojení. Po zobrazení výzvy použijte tyto parametry:
    - Nastavte **port** na 514 nebo port, který jste nastavili v agentovi.
    - Nastavte **syslog_ip** na IP adresu agenta.

1. Chcete-li použít příslušné schéma v Log Analytics pro `CommonSecurityLog`události Cisco, vyhledejte .

1. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili, jak připojit zařízení Cisco ASA k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


