---
title: Práce s více klienty na Azure Sentinel pro poskytovatele služeb MSSP| Dokumenty společnosti Microsoft
description: Jak pracovat s více klienty azure sentinelu pro poskytovatele služeb MSSP.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476011"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Práce s více klienty v Azure Sentinelu 

Pokud jste poskytovatel služeb spravovaného zabezpečení (MSSP) a používáte [Azure Lighthouse](../lighthouse/overview.md) ke správě operačních center zabezpečení vašich zákazníků (SOC), budete moct spravovat prostředky Azure Sentinelu vašich zákazníků, aniž byste se připojili přímo k tenantovi zákazníka, od vašeho vlastního tenanta Azure. 

## <a name="prerequisites"></a>Požadavky
- [Palubní maják Azure](../lighthouse/how-to/onboard-customer.md)
- Aby to fungovalo správně, musí být váš tenant registrovaný u zprostředkovatele prostředků Azure Sentinel u alespoň jednoho předplatného. Pokud máte registrované Azure Sentinel ve vašem tenantovi, jste připraveni začít. Pokud ne, vyberte **Předplatná** z webu Azure Portal, následovaný **zprostředkovateli prostředků**.  Potom na obrazovce **SOC - Resource Providers** `Microsoft.OperationalInsights` vyhledejte a vyberte a `Microsoft.SecurityInsights`vyberte a vyberte **registrovat**.
   ![Kontrola poskytovatelů prostředků](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Jak získat přístup k Azure Sentinelu od jiných klientů
1. V části **Directory + subscription**vyberte delegované adresáře a předplatná, kde jsou umístěny pracovní prostory Azure Sentinel u zákazníka.

   ![Generovat bezpečnostní incidenty](media/multiple-tenants-service-providers/directory-subscription.png)

1. Otevřete Azure Sentinel. Zobrazí se všechny pracovní prostory ve vybraných předplatných a budete s nimi moci bez problémů pracovat, jako každý pracovní prostor ve vašem vlastním tenantovi.

> [!NOTE]
> Nebude možné nasadit konektory v Azure Sentinelu z v rámci spravovaného pracovního prostoru. Chcete-li nasadit konektor, musíte se přímo přihlásit do tenanta, ve kterém chcete nasadit konektor a ověřit se tam s požadovanými oprávněními.





## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili, jak bez problémů spravovat více klientů Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).

