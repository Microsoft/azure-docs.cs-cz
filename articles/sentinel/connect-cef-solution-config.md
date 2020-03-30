---
title: Nakonfigurujte své řešení zabezpečení pro připojení dat cef k Azure Sentinel Preview| Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat řešení zabezpečení pro připojení dat CeF k Azure Sentinelu.
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588446"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>KROK 2: Konfigurace řešení zabezpečení pro odesílání zpráv cef

V tomto kroku provedete potřebné změny konfigurace na samotném bezpečnostním řešení, abyste odeslali protokoly agentovi Nástroje pro řešení Pro vyřízení.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurace řešení pomocí konektoru

Pokud vaše řešení zabezpečení již obsahuje existující konektor, postupujte podle následujících pokynů pro konkrétní konektor:

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Konfigurace jakéhokoli jiného řešení
Pokud konektor neexistuje pro konkrétní řešení zabezpečení, použijte následující obecné pokyny pro předávání protokolů agentovi cef.

1. Přejděte do konkrétního článku konfigurace, kde najdete postup konfigurace řešení pro odesílání zpráv nástroje Pro VPo. Pokud vaše řešení není uveden, na zařízení je třeba nastavit tyto hodnoty tak, aby zařízení odešle potřebné protokoly v potřebném formátu agenta Azure Sentinel Syslog, na základě agenta Log Analytics. Tyto parametry můžete upravit ve vašem zařízení, tak dlouho, dokud je upravit v daemon U Syslog na agenta Azure Sentinel.
    - Protokol = TCP
    - Port = 514
    - Formát = CeF
    - IP adresa - nezapomeňte odeslat zprávy CEF na IP adresu virtuálního počítače, který jste pro tento účel věnovali.

   > [!NOTE]
   > Toto řešení podporuje Syslog RFC 3164 nebo RFC 5424.


1. Chcete-li použít příslušné schéma v log analytics pro události `CommonSecurityLog`CeF, vyhledejte .

1. Pokračujte krokem 3: [ověřte připojení](connect-cef-verify.md).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení CEF k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).

