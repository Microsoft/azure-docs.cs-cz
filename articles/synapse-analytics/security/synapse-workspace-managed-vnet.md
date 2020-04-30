---
title: Spravovaná virtuální síť ve službě Azure synapse Analytics
description: Článek s vysvětlením spravované virtuální sítě ve službě Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423632"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure synapse Analytics Managed Virtual Network (Preview)

V tomto článku se dozvíte, jak spravovat Virtual Network ve službě Azure synapse Analytics.

## <a name="managed-workspace-vnet"></a>Virtuální síť spravovaného pracovního prostoru

Když vytváříte pracovní prostor Azure synapse, můžete si ho přidružit k virtuální síti. Virtuální síť přidružená k vašemu pracovnímu prostoru je spravovaná pomocí Azure synapse. Tato virtuální síť se nazývá *spravovaná virtuální síť pracovního prostoru*.

Virtuální síť spravovaného pracovního prostoru poskytuje hodnoty čtyřmi způsoby:

- Díky spravované virtuální síti pracovního prostoru můžete převzít zátěž při správě virtuální sítě do Azure synapse.
- Nemusíte konfigurovat pravidla příchozího NSG pro vlastní virtuální sítě, aby provoz služby Azure synapse Management mohl vstoupit do vaší virtuální sítě. Chybná konfigurace těchto pravidel NSG způsobuje přerušení služby pro zákazníky.
- Nemusíte vytvářet podsíť pro clustery Spark na základě špičky zatížení.
- Spravovaná virtuální síť v pracovním prostoru spolu se spravovanými soukromými koncovými body chrání před exfiltrace dat. V pracovním prostoru můžete vytvářet jenom spravované privátní koncové body, které mají přidruženou virtuální síť spravovaného pracovního prostoru.

Když se vytvoří pracovní prostor, který je přidružený k virtuální síti spravovaného pracovního prostoru, zajistí, že váš pracovní prostor je izolovaný od ostatních pracovních prostorů. Azure synapse nabízí různé analytické funkce v pracovním prostoru: integraci dat, Apache Spark, fond SQL a SQL na vyžádání.

Pokud má váš pracovní prostor spravovanou virtuální síť pracovního prostoru, v něm jsou nasazené prostředky pro integraci dat a Spark. Spravovaná virtuální síť pracovního prostoru také poskytuje izolaci na úrovni uživatele pro aktivity Sparku, protože každý cluster Spark je ve vlastní podsíti.

Fond SQL a SQL na vyžádání jsou možnosti víceklientské architektury, a proto se nachází mimo spravovanou virtuální síť pracovního prostoru. Komunikace uvnitř pracovního prostoru s fondem SQL a SQL na vyžádání používá privátní odkazy Azure. Tyto privátní odkazy se automaticky vytvoří, když vytvoříte pracovní prostor, který je přidružený k virtuální síti spravovaného pracovního prostoru.

>[!IMPORTANT]
>Po vytvoření pracovního prostoru už tuto konfiguraci pracovního prostoru nemůžete změnit. Například nemůžete znovu nakonfigurovat pracovní prostor, který nemá přidruženou virtuální síť spravovaného pracovního prostoru a přidružit k němu virtuální síť. Podobně nemůžete znovu nakonfigurovat pracovní prostor se spravovaným virtuálním pracovním prostorem, který je k němu přidružený, a zrušit jeho přidružení k virtuální síti.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Vytvoření pracovního prostoru Azure synapse s virtuální sítí spravovaného pracovního prostoru

Pokud chcete vytvořit pracovní prostor Azure synapse, který má přidruženou virtuální síť spravovaného pracovního prostoru, vyberte kartu **zabezpečení + sítě** v Azure Portal a zaškrtněte políčko **Povolit spravovanou virtuální síť** .

Pokud políčko nezaškrtnete, nebudete mít k pracovnímu prostoru přidruženou virtuální síť.

>[!IMPORTANT]
>V pracovním prostoru můžete použít jenom privátní odkazy, které mají spravovanou virtuální síť pracovního prostoru.

![Povolit virtuální síť spravovaného pracovního prostoru](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Veškerý odchozí provoz z virtuální sítě spravovaného pracovního prostoru bude v budoucnu blokovaný. Doporučuje se připojit ke všem zdrojům dat pomocí spravovaných privátních koncových bodů.

Výběrem **přehledu** z Azure Portal můžete ověřit, jestli je váš pracovní prostor Azure synapse přidružený k virtuální síti spravovaného pracovního prostoru.

![Přehled pracovního prostoru v Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](../quickstart-create-workspace.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
