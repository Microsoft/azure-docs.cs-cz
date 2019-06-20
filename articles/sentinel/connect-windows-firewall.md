---
title: Propojení dat Windows firewall Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu Windows firewall data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 09e63612d6e0e70b1bb21c23b158f650d4c34080
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190598"
---
# <a name="connect-windows-firewall"></a>Připojení brány Windows Firewall

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Konektor Windows firewall umožňuje snadno připojit vaše protokoly brány firewall Windows, pokud jsou připojeni k pracovnímu prostoru Azure Sentinelu. Toto připojení umožňuje zobrazit řídicí panely, vytvářet vlastní výstrahy a zlepšit šetření. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení. Řešení shromažďuje události brány firewall pro Windows z počítače Windows, na kterých je nainstalován agenta Log Analytics. 


> [!NOTE]
> Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="enable-the-connector"></a>Povolení konektoru 

1. Na portálu Azure Sentinelu vyberte **datové konektory** a potom klikněte na **brány Windows firewall** dlaždici. 
1.  Pokud jsou vaše počítače s Windows v Azure:
    1. Klikněte na tlačítko **instalace agenta na virtuálním počítači Windows Azure**.
    1. V **virtuálních počítačů** seznamu, vyberte počítač Windows chcete Streamovat do ověřovacích Azure. Ujistěte se, že jde o virtuálního počítače s Windows.
    1. V okně, které se otevře pro tento virtuální počítač, klikněte na tlačítko **připojit**.  
    1. Klikněte na tlačítko **povolit** v **brány Windows firewall konektor** okna. 

2. Pokud váš počítač Windows není virtuální počítač Azure:
    1. Klikněte na tlačítko **instalace agenta do počítače mimo Azure**.
    1. V **přímý agent** okna, vyberte buď **Windows stáhnout agenta (64bitová verze)** nebo **Windows stáhnout agenta (32 bitů)** .
    1. Nainstalujte agenta na svém počítači s Windows. Kopírovat **ID pracovního prostoru**, **primární klíč**, a **sekundární klíč** a využít při zobrazení výzvy v průběhu instalace.

4. Vyberte typy dat, které chcete Streamovat.
5. Klikněte na tlačítko **řešení nainstalovat**.
6. Chcete-li použít odpovídající schématu pro bránu Windows firewall v Log Analytics, vyhledejte **SecurityEvent**.

## <a name="validate-connectivity"></a>Ověření připojení

Může trvat upwards of 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit bránu Windows firewall do ověřovacích Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

