---
title: Připojení dat Zscaler ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Zscaler ke službě Azure Sentinel.
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
ms.openlocfilehash: ef6677b6f1103c26bd719a3585800765a029f7fb
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056834"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Připojení Zscaler k Internetu do Azure Sentinel

Tento článek vysvětluje, jak připojit zařízení s internetovým přístupem Zscaler ke službě Azure Sentinel. Zscaler data Connector umožňuje snadno připojit protokoly Zscaler Internet Access (ZIA) k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Použití Zscaler v Azure Sentinel vám poskytne více informací o používání internetu vaší organizace a zlepší možnosti jejich provozu. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Konfigurace Zscaler pro posílání zpráv CEF

1. Na zařízení Zscaler musíte nastavit tyto hodnoty tak, aby zařízení odesílalo potřebné protokoly v potřebném formátu do agenta Azure Sentinel syslog na základě agenta Log Analytics. Tyto parametry můžete upravit v zařízení, pokud je také upravíte v procesu démona syslog na agentu služby Azure Sentinel.
    - Protokol = TCP
    - Port = 514
    - Format = CEF
    - IP adresa – zajistěte odeslání zpráv CEF na IP adresu virtuálního počítače, který jste si pro tento účel vyhradi.
 Další informace najdete v [Průvodci nasazením Zscaler a Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Toto řešení podporuje syslog RFC 3164 nebo RFC 5424.


1. Pokud chcete použít příslušné schéma v Log Analytics pro události CEF, vyhledejte `CommonSecurityLog` .
1. Pokračujte [krokem 3: ověření připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit přístup k Internetu Zscaler k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.


