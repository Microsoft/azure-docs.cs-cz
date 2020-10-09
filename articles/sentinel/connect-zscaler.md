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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587987"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Připojení Zscaler k Internetu do Azure Sentinel

> [!IMPORTANT]
> Zscaler data Connector ve službě Azure Sentinel je momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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


