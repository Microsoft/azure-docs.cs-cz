---
title: Připojení dat společnosti Fortinet k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data Fortinet k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588191"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Připojení fortinetu k Azure Sentinelu



Tento článek vysvětluje, jak připojit zařízení Fortinet k Azure Sentinelu. Datový konektor Fortinet umožňuje snadno připojit protokoly Fortinet s Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Použití fortinet na Azure Sentinel vám poskytne další přehled o využití internetu vaší organizace a zlepší možnosti operací zabezpečení. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Předávání protokolů fortinet agentovi Syslog

Nakonfigurujte fortinet pro předávání zpráv Syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta Syslog.

1. Otevřete rozhraní příkazového příkazu k zaruce na zařízení Fortinet a spusťte následující příkazy:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Nahraďte **adresu IP** serveru adresou IP agenta.
    - Nastavte **port syslogu** na **514** nebo port nastavený na agenta.
    - Chcete-li povolit formát CEF v raných verzích FortiOS, možná budete muset spustit sadu příkazů **csv zakázat**.
 
   > [!NOTE] 
   > Další informace naleznete v [knihovně dokumentů Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Vyberte svou verzi a použijte **příručku** a **odkaz na zprávu protokolu**.

1. Chcete-li použít příslušné schéma v Azure Monitor Log Analytics pro `CommonSecurityLog`události Fortinet, vyhledejte .

1. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak připojit zařízení Fortinet k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


