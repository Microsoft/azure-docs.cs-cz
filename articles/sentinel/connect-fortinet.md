---
title: Připojení dat Fortinet ke službě Azure Sentinel | Microsoft Docs
description: Připojte zařízení Fortinet ke službě Azure Sentinel a zobrazte řídicí panely, vytvářejte vlastní výstrahy a vylepšete šetření. 
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
ms.openlocfilehash: 8aa8599cbaab6af00d7b4122b94c9e24870881f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511326"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Připojení Fortinet ke službě Azure Sentinel



Tento článek vysvětluje, jak připojit zařízení Fortinet ke službě Azure Sentinel. Fortinet data Connector vám umožní snadno připojit protokoly Fortinet ke službě Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Fortinet v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Přeposílání protokolů Fortinet do agenta syslog

Nakonfigurujte Fortinet pro přeposílání zpráv syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Otevřete rozhraní příkazového řádku na zařízení Fortinet a spusťte následující příkazy:

    ```console
    config log syslogd setting
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    set status enable
    end
    ```

    - Nahraďte **IP adresu** serveru IP adresou agenta.
    - Nastavte **port SYSLOG** na **514** nebo na port nastavený na agentovi.
    - Pokud chcete povolit formát CEF ve verzích rané FortiOS, možná budete muset spustit sadu příkazů **CSV Disable**.
 
   > [!NOTE] 
   > Další informace najdete v [knihovně dokumentů Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Vyberte svou verzi a použijte **odkaz na zprávu** **příručky** a protokolu.

1. Pokud chcete použít příslušné schéma v Azure Monitor Log Analytics pro události Fortinet, vyhledejte `CommonSecurityLog` .

1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak připojit zařízení Fortinet ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


