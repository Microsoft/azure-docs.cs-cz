---
title: Připojení dat palo alto sítí k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data palo alto sítí k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588123"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Připojení sítí Palo Alto k Azure Sentinelu



Tento článek vysvětluje, jak připojit zařízení Palo Alto Networks k Azure Sentinelu. Datový konektor Palo Alto Networks umožňuje snadno připojit protokoly palo alto sítí s Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Použití palo alto sítí v Azure Sentinelu vám poskytne další přehled o využití internetu vaší organizace a zlepší možnosti operací zabezpečení. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Forward Palo Alto Networks přihlásí agenta Syslog

Nakonfigurujte sítě Palo Alto tak, aby přesměrovaly zprávy Syslogu ve formátu CeF do pracovního prostoru Azure prostřednictvím agenta Syslog:
1.  Přejděte do [konfiguračních průvodců společného formátu událostí (CEF)](https://docs.paloaltonetworks.com/resources/cef) a stáhněte si pdf pro typ zařízení. Podle všech pokynů uvedených v průvodci nastavte zařízení Palo Alto Networks pro shromažďování událostí ve mandatříku. 

1.  Přejděte na [konfigurace monitorování Syslogu](https://aka.ms/asi-syslog-paloalto-forwarding) a podle kroků 2 a 3 nakonfigurujte předávání událostí nástroje Pro VALB ze zařízení Palo Alto Networks do Azure Sentinelu.

    1. Ujistěte se, že jste **nastavili formát serveru Syslog** na **BSD**.

       > [!NOTE]
       > Operace kopírování a vkládání z PDF mohou změnit text a vložit náhodné znaky. Chcete-li tomu zabránit, zkopírujte text do editoru a odeberte všechny znaky, které by mohly přerušit formát protokolu před vložením, jak je vidět v tomto příkladu.
 
        ![Problém s kopírováním textu nástroje CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Chcete-li použít příslušné schéma v log analytics pro události Palo Alto Networks, vyhledejte **CommonSecurityLog**.

1. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení Palo Alto Networks k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


