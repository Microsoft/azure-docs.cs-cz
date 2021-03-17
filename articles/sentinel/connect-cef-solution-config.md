---
title: Konfigurace řešení zabezpečení pro připojení CEF dat ke službě Azure Sentinel Preview | Microsoft Docs
description: Naučte se konfigurovat řešení zabezpečení pro připojení dat CEF k ověřovací konfiguraci Azure.
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
ms.openlocfilehash: 368bae515697599dba35a913ebdb9f2b4065c152
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711905"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>Krok 2: konfigurace řešení zabezpečení pro posílání zpráv CEF

V tomto kroku provedete potřebné změny konfigurace v samotném řešení zabezpečení k odesílání protokolů agentu CEF.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurace řešení pomocí konektoru

Pokud vaše řešení zabezpečení již obsahuje existující konektor, použijte pokyny specifické pro konektor následujícím způsobem:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Akamai události zabezpečení](connect-akamai-security-events.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [Trezor hesel CyberArk Enterprise](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Produkty Forcepoint](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [WireX Network forenzní Platform](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Konfigurovat jiné řešení

Pokud pro konkrétní řešení zabezpečení neexistuje konektor, použijte následující obecné pokyny pro přesměrování protokolů do agenta CEF.

1. V článku o konkrétní konfiguraci najdete postup konfigurace řešení pro posílání zpráv CEF. Pokud vaše řešení není uvedené, na zařízení, které potřebujete k nastavení těchto hodnot, tak, aby zařízení odesílalo potřebné protokoly v potřebném formátu do agenta Azure Sentinel syslog na základě agenta Log Analytics. Tyto parametry můžete upravit v zařízení, pokud je také upravíte v procesu démona syslog na agentu služby Azure Sentinel.
    - Protokol = TCP
    - Port = 514
    - Format = CEF
    - IP adresa – zajistěte odeslání zpráv CEF na IP adresu virtuálního počítače, který jste si pro tento účel vyhradi.

   Toto řešení podporuje syslog RFC 3164 nebo RFC 5424.

1. Pokud chcete v Log Analytics vyhledat události CEF, zadejte `CommonSecurityLog` je do okna dotazu.

1. Pokračujte krokem 3: [ověření připojení](connect-cef-verify.md).

> [!NOTE]
> **Změna zdroje pole TimeGenerated**
>
> - Ve výchozím nastavení agent Log Analytics naplní pole *TimeGenerated* ve schématu časem, kdy agent přijal událost z démona syslog. Proto se ve službě Azure Sentinel nezaznamená čas vygenerování události ve zdrojovém systému.
>
> - Můžete ale spustit následující příkaz, který bude stahovat a spouštět `TimeGenerated.py` skript. Tento skript nakonfiguruje agenta Log Analytics k naplnění pole *TimeGenerated* pomocí původního času události v jeho zdrojovém systému místo času, kdy ho agent přijal.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).