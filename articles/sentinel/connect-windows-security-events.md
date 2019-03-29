---
title: Shromažďování dat událostí zabezpečení Windows ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak ke shromažďování dat událostí zabezpečení Windows v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 90bff4cd98046a42fa31d98ff00e76d7fc9ac307
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576084"
---
# <a name="connect-windows-security-events"></a>Připojení událostí zabezpečení systému Windows 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Všechny události zabezpečení z Windows serverů, připojený k pracovnímu prostoru Sentinelu Azure můžete Streamovat. Toto připojení umožňuje zobrazit řídicí panely, vytvářet vlastní výstrahy a zlepšit šetření. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení.  Můžete vybrat události datového proudu:

- **Všechny události** – všechny Windows zabezpečení a událostí nástroje AppLocker.
- **Běžné** – standardní sadu událostí, pro účely auditování.
- **Minimální** -malou sadu událostí, které může znamenat potenciální hrozby. Když tuto možnost povolíte, nebudete moci mít úplný záznam pro audit.
- **Žádný** – žádné události zabezpečení ani Applockeru.

> [!NOTE]
> 
> - Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Nastavení konektoru události zabezpečení Windows

Umožňuje plnou integraci události zabezpečení Windows s Azure Sentinelu:

1. Na portálu Azure Sentinelu vyberte **shromažďování dat** a potom klikněte na **události zabezpečení Windows** dlaždici. 
1. Vyberte typy dat, které chcete Streamovat.
1. Klikněte na tlačítko **aktualizace**.
6. Chcete-li použít příslušné schéma v Log Analytics pro události zabezpečení Windows, vyhledejte **SecurityEvent**.

## <a name="validate-connectivity"></a>Ověření připojení

Může trvat přibližně 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu události zabezpečení Windows. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

