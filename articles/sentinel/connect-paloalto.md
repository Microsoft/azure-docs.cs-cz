---
title: Připojit data sítí Palo Alto do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor sítě Palo Alto k snadnému propojení protokolů sítě Palo Alto s protokolem Azure Sentinel.
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
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85564559"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Připojení sítí Palo Alto k Azure Sentinel



Tento článek vysvětluje, jak připojit zařízení sítě Palo Alto ke službě Azure Sentinel. Konektor dat sítě Palo Alto umožňuje snadno připojit protokoly Palo Alto Networks k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Palo Alto Networks v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Dopředné protokoly Palo Alto Networks do agenta syslog

Nakonfigurujte Palo Alto Networks pro přeposílání zpráv syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog:
1.  Přejít do [příručky konfigurace CEF (Common Event Format)](https://docs.paloaltonetworks.com/resources/cef) a stáhnout PDF pro váš typ zařízení. Podle všech pokynů v příručce nastavte zařízení sítě Palo Alto pro shromažďování událostí CEF. 

1.  Přejděte ke [konfiguraci monitorování syslogu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) a postupujte podle kroků 2 a 3 ke konfiguraci předávání událostí CEF ze zařízení Palo Alto Networks do Azure Sentinel.

    1. Ujistěte se, že jste nastavili **Formát serveru syslog** na **BSD**.

       > [!NOTE]
       > Operace kopírování a vkládání z PDF mohou změnit text a vkládat náhodné znaky. Aby k tomu nedocházelo, zkopírujte text do editoru a odstraňte všechny znaky, které by mohly přerušit formát protokolu před jeho vložením, jak je uvedeno v tomto příkladu.
 
        ![Problém s kopírováním textu CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Pokud chcete použít příslušné schéma v Log Analytics pro události sítě Palo Alto, vyhledejte **CommonSecurityLog**.

1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení Palo Alto Network ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


