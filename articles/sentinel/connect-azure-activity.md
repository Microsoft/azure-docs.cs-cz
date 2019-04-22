---
title: Připojit data aktivit Azure Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k Azure ověřovací aktivita služby Azure data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d0cc13227bfe02594a57a7fb0ba8ee1cb3383d56
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785155"
---
# <a name="connect-data-from-azure-activity-log"></a>Propojení dat od protokolů aktivit Azure

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly z [protokol aktivit Azure](../azure-monitor/platform/activity-logs-overview.md) do Azure Sentinelu jediným kliknutím. Protokol aktivit je předplatné protokol, který poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. To zahrnuje celou řadu dat z Azure Resource Manageru provozních dat k aktualizacím na události služby Service Health. Použití protokolu aktivit, můžete určit "co, kdo a kdy" pro jakékoli zápisu operace (PUT, POST, DELETE) provedených ve prostředků ve vašem předplatném. Můžete také zjištění stavu operace a další relevantní vlastnosti. Protokol aktivit neobsahuje operace čtení (GET) ani operace pro prostředky, které používají Classic nebo model "RDFE". 


## <a name="prerequisites"></a>Požadavky

- Uživatel se oprávnění správce zabezpečení nebo globální správce


## <a name="connect-to-azure-activity-log"></a>Připojit k protokolům aktivit Azure

1. V Azure Sentinelu, vyberte **datové konektory** a potom klikněte na tlačítko **protokol aktivit Azure** dlaždici.

2. V podokně protokolu aktivit Azure vyberte odběry, které chcete Streamovat do ověřovacích Azure. 

3. Klikněte na **Připojit**.

4. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy aktivita služby Azure, vyhledejte **AzureActivity**.


 

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit protokol aktivit Azure do Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
