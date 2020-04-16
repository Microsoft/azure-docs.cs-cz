---
title: Spravovaná virtuální síť v Azure Synapse Analytics
description: Článek, který vysvětluje spravovanou virtuální síť v Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423632"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Virtuální síť spravovaná azure synapse analytics (preview)

Tento článek vysvětluje spravovanou virtuální síť v Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>Virtuální síť spravovaného pracovního prostoru

Když vytvoříte pracovní prostor Azure Synapse, můžete ho přidružit k virtuální síti. Virtuální síť přidružená k vašemu pracovnímu prostoru spravuje Azure Synapse. Tato virtuální síť se nazývá *virtuální síť spravovaného pracovního prostoru*.

Virtuální síť spravovaného pracovního prostoru poskytuje hodnotu čtyřmi způsoby:

- S virtuální virtuální sítě spravovaného pracovního prostoru můžete přetížit správu virtuální sítě do Azure Synapse.
- Není nutné konfigurovat příchozí pravidla sítě zabezpečení sítě na vlastních virtuálních sítích, aby provoz správy Azure Synapse mohl vstoupit do vaší virtuální sítě. Nesprávnou konfigurací těchto pravidel nsg způsobí přerušení služeb pro zákazníky.
- Není nutné vytvářet podsíť pro clustery Spark na základě zatížení ve špičce.
- Virtuální síť spravovaného pracovního prostoru spolu se spravovanými privátními koncovými body chrání před exfiltrací dat. Spravované soukromé koncové body můžete vytvořit jenom v pracovním prostoru, ke kterému je přidružena virtuální síť spravovaného pracovního prostoru.

Vytvoření pracovního prostoru s přidruženou virtuální sítí spravovaného pracovního prostoru zajistí, že váš pracovní prostor bude izolovaný od ostatních pracovních prostorů. Azure Synapse poskytuje různé analytické funkce v pracovním prostoru: integrace dat, Apache Spark, FOND SQL a SQL na vyžádání.

Pokud váš pracovní prostor obsahuje virtuální síť spravovaného pracovního prostoru, nasadí se v něm integrace dat a prostředky Spark. Virtuální síť spravovaného pracovního prostoru také poskytuje izolaci na úrovni uživatele pro aktivity Spark, protože každý cluster Spark je ve své vlastní podsíti.

Fond SQL a SQL na vyžádání jsou víceklientské možnosti a proto se nacházejí mimo virtuální síť spravovaného pracovního prostoru. Komunikace v rámci pracovního prostoru do fondu SQL a SQL na vyžádání používá privátní odkazy Azure. Tyto soukromé vazby se automaticky vytvoří při vytváření pracovního prostoru se spravovanou virtuální sítí pracovního prostoru, která je k němu přidružená.

>[!IMPORTANT]
>Tuto konfiguraci pracovního prostoru nelze po vytvoření pracovního prostoru změnit. Například nelze překonfigurovat pracovní prostor, který nemá spravovanou virtuální síť pracovního prostoru k němu spojené a přidružit virtuální síť k němu. Podobně nelze změnit konfiguraci pracovního prostoru se spravovanou virtuální sítí pracovního prostoru, která je k němu přidružena, a zrušit přidružení virtuální sítě k ní.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Vytvoření pracovního prostoru Azure Synapse pomocí spravované virtuální sítě pracovního prostoru

Pokud chcete vytvořit pracovní prostor Azure Synapse, ke kterému je přidružena virtuální síť spravovaného pracovního prostoru, vyberte na webu Azure Portal kartu **Zabezpečení + síť** a zaškrtněte políčko **Povolit spravovanou virtuální síť.**

Pokud ponecháte zaškrtávací políčko nezaškrtnuté, nebude k němu mít virtuální síť přidruženou virtuální síť.

>[!IMPORTANT]
>Soukromé odkazy můžete používat jenom v pracovním prostoru, který má virtuální síť spravovaného pracovního prostoru.

![Povolení virtuální sítě spravovaného pracovního prostoru](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Veškerý odchozí provoz z virtuální sítě spravovaného pracovního prostoru bude v budoucnu blokován. Doporučujeme připojit se ke všem zdrojům dat pomocí spravovaných soukromých koncových bodů.

Můžete zkontrolovat, jestli váš pracovní prostor Azure Synapse je přidružený k virtuální síti spravovaného pracovního prostoru výběrem **Přehled** z webu Azure Portal.

![Přehled pracovního prostoru na Webu Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure Synapse](../quickstart-create-workspace.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů do zdrojů dat](./how-to-create-managed-private-endpoints.md)
