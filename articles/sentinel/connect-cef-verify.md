---
title: Ověření připojení k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Ověřte připojení vašeho řešení zabezpečení a ujistěte se, že zprávy CeF jsou předávány do Azure Sentinelu.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588429"
---
# <a name="step-3-validate-connectivity"></a>KROK 3: Ověření připojení



Po nasazení agenta a konfiguraci řešení zabezpečení pro předávání zpráv CeF, použijte tento článek k pochopení, jak ověřit připojení mezi Azure Sentinel a vaše řešení zabezpečení. 

## <a name="how-to-validate-connectivity"></a>Jak ověřit připojení

1. Otevřete analýzu protokolů a ujistěte se, že protokoly jsou přijímány pomocí schématu CommonSecurityLog.<br> Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

1. Před spuštěním skriptu doporučujeme odeslat zprávy z řešení zabezpečení a ujistěte se, že jsou předávány do počítače proxy Syslog, který jste nakonfigurovali. 
1. Musíte mít zvýšená oprávnění (sudo) na vašem počítači. Ujistěte se, že máte Python na vašem počítači pomocí následujícího příkazu:`python –version`
1. Spusťte následující skript a zkontrolujte připojení mezi agentem, Azure Sentinelem a vaším řešením zabezpečení. Zkontroluje, zda je předávání daemonu správně nakonfigurováno, naslouchá na správných portech a že nic neblokuje komunikaci mezi daemonem a agentem Log Analytics. Skript také odesílá falešné zprávy "TestCommonEventFormat" pro kontrolu připojení od konce do konce. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení CEF k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

