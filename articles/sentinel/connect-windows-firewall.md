---
title: Připojit data brány Windows Firewall ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data brány Windows Firewall ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588055"
---
# <a name="connect-windows-firewall"></a>Připojení brány Windows Firewall



Konektor brány Windows Firewall umožňuje snadno připojit protokoly brány Windows Firewall, pokud jsou připojené k pracovnímu prostoru Sentinel Azure. Toto připojení umožňuje zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti vaší operace zabezpečení. Řešení shromažďuje události brány Windows Firewall z počítačů se systémem Windows, na kterých je nainstalován agent Log Analytics. 


> [!NOTE]
> - Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.
> - Pokud se do stejného pracovního prostoru shromažďují služby Azure Sentinel a Azure Security Center, není nutné povolit řešení brány Windows Firewall prostřednictvím tohoto konektoru. Pokud tuto možnost povolíte, nezpůsobí duplikovaná data. 

## <a name="enable-the-connector"></a>Povolení konektoru 

1. Na portálu Sentinel Azure vyberte **datové konektory** a pak klikněte na dlaždici **Windows Firewall** . 
1.  Pokud jsou počítače s Windows v Azure:
    1. Klikněte na **nainstalovat agenta na virtuální počítač Azure s Windows**.
    1. V seznamu **virtuální počítače** vyberte počítač s Windows, který chcete streamovat do Azure Sentinel. Ujistěte se, že se jedná o virtuální počítač s Windows.
    1. V okně, které se otevře pro daný virtuální počítač, klikněte na **připojit**.  
    1. V okně **konektor brány Windows Firewall** klikněte na **Povolit** . 

2. Pokud Váš počítač s Windows není virtuální počítač Azure:
    1. Klikněte na **instalovat agenta na počítačích mimo Azure**.
    1. V okně **přímý agent** vyberte buď možnost **Stáhnout agenta pro Windows (64 bitů)** , nebo **stáhnout agenta pro Windows (32 bitů)** .
    1. Nainstalujte agenta na počítač s Windows. Zkopírujte **ID pracovního prostoru**, **primární klíč**a **sekundární klíč** a použijte je při zobrazení výzvy během instalace.

4. Vyberte datové typy, které chcete streamovat.
5. Klikněte na **instalovat řešení**.
6. Pokud chcete použít příslušné schéma v Log Analytics pro bránu Windows Firewall, vyhledejte **SecurityEvent**.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit bránu Windows Firewall ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

