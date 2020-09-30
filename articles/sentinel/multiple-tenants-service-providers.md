---
title: Správa více tenantů v Azure Sentinel jako poskytovatele spravované služby zabezpečení | Microsoft Docs
description: Jak integrovat a spravovat víc tenantů v Azure Sentinel jako spravovaného poskytovatele služby zabezpečení (MSSP) pomocí Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578138"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Správa více tenantů v Azure Sentinel jako MSSP

Pokud jste spravovaným poskytovatelem zabezpečení (MSSP) a používáte [Azure Lighthouse](../lighthouse/overview.md) k poskytování služeb Security Center (SOC) zákazníkům, můžete spravovat prostředky Sentinel Azure na svých zákaznících přímo z vlastního tenanta Azure, aniž byste se museli připojovat k tenantovi zákazníka. 

## <a name="prerequisites"></a>Požadavky

- [Zprovoznění Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Aby to fungovalo správně, musí mít váš tenant (tenant MSSP) poskytovatele prostředků služby Azure Sentinel registrovaný alespoň v jednom předplatném. Kromě toho musí mít každý tenant vašeho zákazníka zaregistrovaný poskytovatelé prostředků. Pokud jste ve svém tenantovi zaregistrovali Sentinel Azure a vaše zákazníky v nich, jste připraveni začít. Chcete-li ověřit registraci, proveďte následující kroky:

    1. Z Azure Portal vyberte **předplatná** a v nabídce vyberte příslušné předplatné.

    1. Z navigační nabídky na obrazovce předplatné v části **Nastavení**vyberte **poskytovatelé prostředků**.

    1. Z ** *názvu předplatného* | Na obrazovce poskytovatelé prostředků** vyhledejte a vyberte *Microsoft. OperationalInsights* a *Microsoft. SecurityInsights*a zaškrtněte políčko ve sloupci **stav** . Pokud je stav poskytovatele *NotRegistered*, vyberte **zaregistrovat**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Kontrolovat poskytovatele prostředků":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Přístup ke službě Azure Sentinel ve spravovaných klientech

1. V části **adresář + předplatné**vyberte delegované adresáře (adresář = tenant) a odběry, ve kterých se nacházejí pracovní prostory Azure Sentinel zákazníka.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Kontrolovat poskytovatele prostředků":::

1. Otevřete Azure Sentinel. Ve vybraných předplatných se zobrazí všechny pracovní prostory a budete s nimi moct pracovat hladce, stejně jako libovolný pracovní prostor ve vašem tenantovi.

> [!NOTE]
> V rámci spravovaného pracovního prostoru nebudete moct nasazovat konektory v Azure Sentinel. Chcete-li nasadit konektor, je nutné, abyste se přihlásili přímo do tenanta, na kterém chcete nasadit konektor, a ověřit je s požadovanými oprávněními.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak hladce spravovat více tenantů Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

