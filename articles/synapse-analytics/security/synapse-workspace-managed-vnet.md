---
title: Spravovaná virtuální síť
description: Článek s vysvětlením spravované virtuální sítě ve službě Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b605c2c9f3c7468d0fe1f2cccd8c3602c556714b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501069"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Virtual Network spravovaných analýz Azure synapse

V tomto článku se dozvíte, jak spravovat Virtual Network ve službě Azure synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Spravovaný Virtual Network pracovního prostoru

Když vytvoříte pracovní prostor Azure synapse, můžete si ho přidružit k Microsoft Azure Virtual Network. Virtual Network přidružené k vašemu pracovnímu prostoru spravuje Azure synapse. Tato Virtual Network se nazývá *spravovaný pracovní prostor Virtual Network*.

Managed Virtual Network pracovního prostoru poskytuje hodnoty čtyřmi způsoby:

- Díky spravovanému pracovnímu prostoru Virtual Network můžete převzít zátěž správy Virtual Network do Azure synapse.
- Nemusíte konfigurovat pravidla příchozího NSG pro vlastní virtuální sítě, aby provoz služby Azure synapse Management mohl zadat Virtual Network. Chybná konfigurace těchto pravidel NSG způsobuje přerušení služby pro zákazníky.
- Nemusíte vytvářet podsíť pro clustery Spark na základě špičky zatížení.
- Spravovaný pracovní prostor Virtual Network spolu se spravovanými soukromými koncovými body chrání před exfiltrace dat. V pracovním prostoru můžete vytvářet jenom spravované privátní koncové body, které mají ke spravovanému pracovnímu prostoru Virtual Network přidružené.

Vytvoření pracovního prostoru se spravovaným pracovním prostorem Virtual Network přidruženým k tomu zajišťuje, že je váš pracovní prostor v síti izolovaný od ostatních pracovních prostorů. Azure synapse nabízí různé analytické možnosti v pracovním prostoru: integraci dat, fond Apache Spark bez serveru, vyhrazený fond SQL a fond SQL bez serveru.

Pokud má váš pracovní prostor spravovaný pracovní prostor Virtual Network, jsou v něm nasazené prostředky pro integraci dat a Spark. Spravovaný pracovní prostor Virtual Network také poskytuje izolaci na úrovni uživatele pro aktivity Sparku, protože každý cluster Spark je ve vlastní podsíti.

Vyhrazený fond SQL a SQL Server bez serveru jsou možnosti víceklientské architektury, takže se nachází mimo spravovaný pracovní prostor Virtual Network. Komunikace uvnitř pracovního prostoru s vyhrazeným fondem SQL a fondem SQL bez serveru používá privátní odkazy Azure. Tyto privátní odkazy se automaticky vytvoří, když vytvoříte pracovní prostor se spravovaným pracovním prostorem Virtual Network k němu přidružená.

>[!IMPORTANT]
>Po vytvoření pracovního prostoru už tuto konfiguraci pracovního prostoru nemůžete změnit. Například nemůžete znovu nakonfigurovat pracovní prostor, který nemá spravovaný pracovní prostor Virtual Network k němu přidružen a přidružit k němu Virtual Network. Podobně nemůžete znovu nakonfigurovat pracovní prostor se spravovaným pracovním prostorem Virtual Network k němu přidružené a zrušit jeho přidružení Virtual Network.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Vytvoření pracovního prostoru Azure synapse pomocí spravovaného pracovního prostoru Virtual Network

Pokud jste to ještě neudělali, zaregistrujte poskytovatele síťových prostředků. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Po [registraci](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)vyberte v seznamu poskytovatelů prostředků možnost *Microsoft. Network* .

Pokud chcete vytvořit pracovní prostor Azure synapse s přidruženým spravovaným pracovním prostorem Virtual Network, vyberte kartu **síť** v Azure Portal a zaškrtněte políčko **Povolit spravovanou virtuální síť** .

Pokud políčko nezaškrtnete, nebude mít k pracovnímu prostoru přidruženou Virtual Network.

>[!IMPORTANT]
>V pracovním prostoru, který má spravovaný pracovní prostor Virtual Network, můžete použít pouze privátní odkazy.

![Povolit spravovaný Virtual Network pracovního prostoru](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)


Vybráním možnosti **Přehled** z Azure Portal můžete zjistit, jestli je váš pracovní prostor Azure synapse přidružený ke spravovanému pracovnímu Virtual Network prostoru.

![Přehled pracovního prostoru v Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](../quickstart-create-workspace.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
