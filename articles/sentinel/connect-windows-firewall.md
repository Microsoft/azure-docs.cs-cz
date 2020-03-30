---
title: Připojení dat brány firewall systému Windows k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data brány firewall systému Windows k Azure Sentinelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588055"
---
# <a name="connect-windows-firewall"></a>Připojení brány Windows Firewall



Konektor brány firewall systému Windows umožňuje snadno připojit protokoly brány firewall systému Windows, pokud jsou připojeny k pracovnímu prostoru Azure Sentinel. Toto připojení umožňuje zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit šetření. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti operací zabezpečení. Řešení shromažďuje události brány firewall systému Windows z počítačů se systémem Windows, na kterých je nainstalován agent Log Analytics. 


> [!NOTE]
> - Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.
> - Pokud azure sentinel a Azure Security Center jsou shromažďovány do stejného pracovního prostoru, není nutné povolit řešení Windows Firewall prostřednictvím tohoto konektoru. Pokud jste ji přesto povolili, nezpůsobí duplicitní data. 

## <a name="enable-the-connector"></a>Povolení konektoru 

1. Na portálu Azure Sentinel vyberte **Datové konektory** a klikněte na dlaždici **brány firewall Windows.** 
1.  Pokud jsou vaše počítače s Windows v Azure:
    1. Klikněte **na Nainstalovat agenta na virtuálním počítači Azure Windows**.
    1. V seznamu **Virtuální počítače** vyberte počítač s Windows, který chcete streamovat do Azure Sentinelu. Ujistěte se, že se jedná o virtuální hod windows.
    1. V okně, které se otevře pro tento virtuální virtuální ms, klikněte na **Připojit**.  
    1. V okně **konektoru brány firewall systému Windows** klepněte na **tlačítko Povolit.** 

2. Pokud váš počítač s Windows není virtuální počítač Azure:
    1. Klikněte **na nainstalovat agenta na počítačích jiného než Azure**.
    1. V okně **Přímého agenta** vyberte **buď stáhnout agenta systému Windows (64 bit)** nebo **stáhnout agenta systému Windows (32 bit)**.
    1. Nainstalujte agenta do počítače se systémem Windows. Zkopírujte **ID pracovního prostoru**, **primární klíč**a sekundární **klíč** a použijte je po zobrazení výzvy během instalace.

4. Vyberte, které datové typy chcete streamovat.
5. Klepněte na tlačítko **Nainstalovat řešení**.
6. Chcete-li použít příslušné schéma v log analytics pro bránu firewall systému Windows, vyhledejte **SecurityEvent**.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili, jak připojit bránu firewall systému Windows k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).

