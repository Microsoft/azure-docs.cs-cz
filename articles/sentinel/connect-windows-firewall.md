---
title: Shromažďování dat Windows firewall ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data Windows firewall v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2356a7e5426037ffe9fc8b304ac113f4a3fe2a17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103345"
---
# <a name="connect-windows-firewall"></a>Připojení brány Windows Firewall

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Konektor Windows firewall umožňuje snadno připojit vaše protokoly brány firewall Windows, pokud jsou připojeni k pracovnímu prostoru Azure Sentinelu. Toto připojení umožňuje zobrazit řídicí panely, vytvářet vlastní výstrahy a zlepšit šetření. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení.  


> [!NOTE]
> 
> - Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="enable-the-connector"></a>Povolení konektoru 

1. Na portálu Azure Sentinelu vyberte **shromažďování dat** a potom klikněte na **brány Windows firewall** dlaždici. 
1. Vyberte typy dat, které chcete Streamovat.
1. Klikněte na **Nainstalovat**.

## <a name="validate-connectivity"></a>Ověření připojení

Může trvat upwards of 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak připojit bránu Windows firewall do ověřovacích Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

