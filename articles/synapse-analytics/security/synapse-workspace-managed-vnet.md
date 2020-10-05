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
ms.openlocfilehash: 06b535b25df19e5062d16184f4469d9e9253b9c0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87042610"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure synapse Analytics Managed Virtual Network (Preview)

V tomto článku se dozvíte, jak spravovat Virtual Network ve službě Azure synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Spravovaný Virtual Network pracovního prostoru

Když vytvoříte pracovní prostor Azure synapse, můžete si ho přidružit k Microsoft Azure Virtual Network. Virtual Network přidružené k vašemu pracovnímu prostoru spravuje Azure synapse. Tato Virtual Network se nazývá *spravovaný pracovní prostor Virtual Network*.

Managed Virtual Network pracovního prostoru poskytuje hodnoty čtyřmi způsoby:

- Díky spravovanému pracovnímu prostoru Virtual Network můžete převzít zátěž správy Virtual Network do Azure synapse.
- Nemusíte konfigurovat pravidla příchozího NSG pro vlastní virtuální sítě, aby provoz služby Azure synapse Management mohl zadat Virtual Network. Chybná konfigurace těchto pravidel NSG způsobuje přerušení služby pro zákazníky.
- Nemusíte vytvářet podsíť pro clustery Spark na základě špičky zatížení.
- Spravovaný pracovní prostor Virtual Network spolu se spravovanými soukromými koncovými body chrání před exfiltrace dat. V pracovním prostoru můžete vytvářet jenom spravované privátní koncové body, které mají ke spravovanému pracovnímu prostoru Virtual Network přidružené.

Vytvoření pracovního prostoru se spravovaným pracovním prostorem Virtual Network přidruženým k tomu zajišťuje, že je váš pracovní prostor v síti izolovaný od ostatních pracovních prostorů. Azure synapse nabízí různé analytické funkce v pracovním prostoru: integraci dat, Apache Spark, fond SQL a SQL na vyžádání.

Pokud má váš pracovní prostor spravovaný pracovní prostor Virtual Network, jsou v něm nasazené prostředky pro integraci dat a Spark. Spravovaný pracovní prostor Virtual Network také poskytuje izolaci na úrovni uživatele pro aktivity Sparku, protože každý cluster Spark je ve vlastní podsíti.

Fond SQL a SQL na vyžádání jsou možnosti víceklientské architektury, a proto se nachází mimo spravovaný pracovní prostor Virtual Network. Komunikace uvnitř pracovního prostoru s fondem SQL a SQL na vyžádání používá privátní odkazy Azure. Tyto privátní odkazy se automaticky vytvoří, když vytvoříte pracovní prostor se spravovaným pracovním prostorem Virtual Network k němu přidružená.

>[!IMPORTANT]
>Po vytvoření pracovního prostoru už tuto konfiguraci pracovního prostoru nemůžete změnit. Například nemůžete znovu nakonfigurovat pracovní prostor, který nemá spravovaný pracovní prostor Virtual Network k němu přidružen a přidružit k němu Virtual Network. Podobně nemůžete znovu nakonfigurovat pracovní prostor se spravovaným pracovním prostorem Virtual Network k němu přidružené a zrušit jeho přidružení Virtual Network.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Vytvoření pracovního prostoru Azure synapse pomocí spravovaného pracovního prostoru Virtual Network

Pokud jste to ještě neudělali, zaregistrujte poskytovatele síťových prostředků. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Po [registraci](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)vyberte v seznamu poskytovatelů prostředků možnost *Microsoft. Network* .

Pokud chcete vytvořit pracovní prostor Azure synapse s přidruženým spravovaným pracovním prostorem Virtual Network, vyberte kartu **zabezpečení + sítě** v Azure Portal a zaškrtněte políčko **Povolit spravovanou virtuální síť** .

Pokud políčko nezaškrtnete, nebude mít k pracovnímu prostoru přidruženou Virtual Network.

>[!IMPORTANT]
>V pracovním prostoru, který má spravovaný pracovní prostor Virtual Network, můžete použít pouze privátní odkazy.

![Povolit spravovaný Virtual Network pracovního prostoru](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Veškerý odchozí provoz ze spravovaného pracovního prostoru Virtual Network s výjimkou spravovaných privátních koncových bodů bude v budoucnu blokovaný. Doporučuje se vytvořit spravované privátní koncové body pro připojení ke všem zdrojům dat Azure, které jsou externí pro daný pracovní prostor. 

Vybráním možnosti **Přehled** z Azure Portal můžete zjistit, jestli je váš pracovní prostor Azure synapse přidružený ke spravovanému pracovnímu Virtual Network prostoru.

![Přehled pracovního prostoru v Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](../quickstart-create-workspace.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
