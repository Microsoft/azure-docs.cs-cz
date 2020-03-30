---
title: Připojení dat Zscaler k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data Zscaler k Azure Sentinelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587987"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Připojení internetového přístupu Zscaler k Azure Sentinelu

> [!IMPORTANT]
> Datový konektor Zscaler v Azure Sentinelu je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit zařízení Zscaler Internet Access k Azure Sentinelu. Datový konektor Zscaler umožňuje snadno připojit protokoly Zscaler Internet Access (ZIA) pomocí Azure Sentinelu, zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Použití Zscaleru v Azure Sentinelu vám poskytne další přehled o využití internetu vaší organizace a zlepší možnosti operací zabezpečení. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Konfigurace zařízení Zscaler pro odesílání zpráv cef

1. Na zařízení Zscaler je třeba nastavit tyto hodnoty tak, aby zařízení odešle potřebné protokoly v potřebném formátu agenta Azure Sentinel Syslog, na základě agenta Log Analytics. Tyto parametry můžete upravit ve vašem zařízení, tak dlouho, dokud je upravit v daemon U Syslog na agenta Azure Sentinel.
    - Protokol = TCP
    - Port = 514
    - Formát = CeF
    - IP adresa - nezapomeňte odeslat zprávy CEF na IP adresu virtuálního počítače, který jste pro tento účel věnovali.
 Další informace naleznete v [Průvodci nasazením Zscaler a Azure Sentinel .](https://aka.ms/ZscalerCEFInstructions)
 
   > [!NOTE]
   > Toto řešení podporuje Syslog RFC 3164 nebo RFC 5424.


1. Chcete-li použít příslušné schéma v log analytics pro události `CommonSecurityLog`CeF, vyhledejte .
1. Pokračovat krokem [3: Ověřit připojení](connect-cef-verify.md).


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Zscaler Internet Access k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)


