---
title: Práce s více klienty v Azure Sentinel pro poskytovatele služeb MSSP | Microsoft Docs
description: Jak v Azure Sentinel pro poskytovatele služeb MSSP pracovat s více klienty.
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
ms.openlocfilehash: caa79b572d0024b93abd2d32ca99d92cc2a8b4bb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582071"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Práce s více klienty v Azure Sentinel 

Pokud jste spravovaným poskytovatelem zabezpečení (MSSP) a používáte [Azure Lighthouse](../lighthouse/overview.md) ke správě služeb provozu Security Center (SOC) vašich zákazníků, budete moct spravovat prostředky Sentinel Azure pro vaše zákazníky bez přímého připojení k tenantovi zákazníka, a to z vlastního tenanta Azure. 

## <a name="prerequisites"></a>Předpoklady
- [Zprovoznění Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Aby to fungovalo správně, musí být váš tenant zaregistrován pro poskytovatele prostředků Azure Sentinel aspoň na jednom předplatném. Pokud máte ve svém tenantovi registrovanou ověřovací službu Azure, budete připraveni začít. Pokud ne, v Azure Portal vyberte **předplatná** , která následují **poskytovatelé prostředků** , a pak vyhledejte `Microsoft.Security.Insights` a vyberte **zaregistrovat**.
   ![Projděte poskytovatele prostředků](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Přístup k Azure Sentinel z jiných tenantů
1. V části **adresář + předplatné**vyberte delegované adresáře a předplatné, kde se nacházejí pracovní prostory Azure Sentinel zákazníka.

   ![Generovat incidenty zabezpečení](media/multiple-tenants-service-providers/directory-subscription.png)

1. Otevřete Azure Sentinel. Ve vybraných předplatných se zobrazí všechny pracovní prostory a budete s nimi moct pracovat hladce, stejně jako libovolný pracovní prostor ve vašem tenantovi.

> [!NOTE]
> V rámci spravovaného pracovního prostoru nebudete moct nasazovat konektory v Azure Sentinel. Chcete-li nasadit konektor, je nutné, abyste se přihlásili přímo do tenanta, na kterém chcete nasadit konektor, a ověřit je s požadovanými oprávněními.





## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak hladce spravovat více tenantů Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

