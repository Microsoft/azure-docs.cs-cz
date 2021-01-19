---
title: Spravovaná virtuální síť
description: Článek s vysvětlením spravované virtuální sítě ve službě Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/18/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f55251932c8aa8f632bd3b498943ac722f006dee
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569897"
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

Pokud jste to ještě neudělali, zaregistrujte poskytovatele síťových prostředků. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Po [registraci](../../azure-resource-manager/management/resource-providers-and-types.md)vyberte v seznamu poskytovatelů prostředků možnost *Microsoft. Network* .

Pokud chcete vytvořit pracovní prostor Azure synapse s přidruženým spravovaným pracovním prostorem Virtual Network, vyberte kartu **síť** v Azure Portal a zaškrtněte políčko **Povolit spravovanou virtuální síť** .

Pokud políčko nezaškrtnete, nebude mít k pracovnímu prostoru přidruženou Virtual Network.

>[!IMPORTANT]
>V pracovním prostoru, který má spravovaný pracovní prostor Virtual Network, můžete použít pouze privátní odkazy.

![Povolit spravovaný Virtual Network pracovního prostoru](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

Po zvolení přidružení spravovaného pracovního prostoru Virtual Network s vaším pracovním prostorem můžete chránit před exfiltrace dat tím, že povolíte odchozí připojení ze spravovaného pracovního prostoru Virtual Network jenom ke schváleným cílům pomocí [spravovaných privátních koncových bodů](./synapse-workspace-managed-private-endpoints.md). Vyberte **Ano** , pokud chcete omezit odchozí přenosy ze spravovaného pracovního prostoru Virtual Network na cíle prostřednictvím spravovaných privátních koncových bodů. 


>[!IMPORTANT]
>Metastore je v pracovních prostorech synapse, které mají spravované Virtual Network se zapnutou ochranou exfiltrace dat, zakázaná. V těchto pracovních prostorech nebudete moct použít Spark SQL.

![Odchozí přenosy pomocí spravovaných privátních koncových bodů](./media/synapse-workspace-managed-vnet/select-outbound-connectivity.png)

Pokud chcete, aby odchozí přenosy z pracovního prostoru do libovolného cíle povolovaly, vyberte **ne** .

Můžete taky určit cíle, na které se spravované privátní koncové body vytvoří z pracovního prostoru Azure synapse. Ve výchozím nastavení jsou spravované privátní koncové body pro prostředky ve stejném tenantovi služby AAD, ke kterému vaše předplatné patří, povolené. Pokud chcete vytvořit spravovaný privátní koncový bod k prostředku v tenantovi služby AAD, který se liší od toho, ke kterému vaše předplatné patří, můžete tohoto tenanta AAD přidat tak, že vyberete **+ Přidat**. Můžete buď vybrat tenanta AAD z rozevíracího seznamu, nebo ručně zadat ID tenanta AAD.

![Přidat další klienty AAD](./media/synapse-workspace-managed-vnet/add-additional-azure-active-directory-tenants.png)

Po vytvoření pracovního prostoru můžete ověřit, jestli je váš pracovní prostor Azure synapse přidružený ke spravovanému pracovnímu prostoru Virtual Network tím, že vyberete **Přehled** z Azure Portal.

![Přehled pracovního prostoru v Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](../quickstart-create-workspace.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
