---
title: Spravované soukromé koncové body
description: Článek, který vysvětluje spravované privátní koncové body v Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423681"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Soukromé koncové body spravované synapse (náhled)

Tento článek vysvětluje spravované privátní koncové body v Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Spravované soukromé koncové body

Spravované privátní koncové body jsou privátní koncové body vytvořené ve virtuální síti spravovaného pracovního prostoru, které vytvářejí privátní propojení s prostředky Azure. Azure Synapse spravuje tyto soukromé koncové body vaším jménem.

Azure Synapse podporuje privátní odkazy. Privátní propojení umožňuje přístup ke službám Azure (jako je Azure Storage, Azure Cosmos DB a Azure SQL Data Warehouse) a službám hostovaných zákazníkům/partnerům Azure z vaší virtuální sítě Azure bezpečně.

Při použití privátní propojení, provoz mezi vaší virtuální sítí a pracovního prostoru prochází zcela přes páteřní síť Microsoftu. Private Link chrání před riziky exfiltrace dat. Privátní propojení s prostředkem vytvoříte privátním koncovým bodem.

Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě efektivně přenést službu do virtuální sítě. Privátní koncové body jsou mapovány na konkrétní prostředek v Azure a ne na celou službu. Zákazníci mohou omezit připojení k určitému prostředku schválenému jejich organizací. Další informace o [soukromých odkazech a soukromých koncových bodech](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Spravované privátní koncové body jsou podporované jenom v pracovních prostorech Azure Synapse s virtuální sítí spravovaného pracovního prostoru.
>[!NOTE]
>Doporučujeme vytvořit spravované soukromé koncové body pro připojení ke všem zdrojům dat Azure. Veškerý odchozí provoz z virtuální sítě >spravovaného pracovního prostoru bude v budoucnu zablokován.

Privátní koncového bodu připojení se vytvoří ve stavu čekající na vyřízení při vytváření spravovaného privátního koncového bodu v Azure Synapse. Je zahájen pracovní postup schválení. Vlastník prostředku soukromého propojení je zodpovědný za schválení nebo odmítnutí připojení.

Pokud vlastník připojení schválí, nastotím se soukromé propojení. V opačném případě nebude privátní propojení vytvořeno. V obou případech bude spravované soukromé koncový bod aktualizován o stav připojení.

Pouze spravovaný soukromý koncový bod ve schváleném stavu může odesílat provoz do daného prostředku privátního propojení.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Spravované privátní koncové body pro fond SQL a SQL na vyžádání

Fond SQL a SQL na vyžádání jsou analytické funkce ve vašem pracovním prostoru Azure Synapse. Tyto možnosti používají infrastrukturu s více klienty, která není nasazená do [virtuální sítě spravovaného pracovního prostoru](./synapse-workspace-managed-vnet.md).

Při vytvoření pracovního prostoru Azure Synapse vytvoří dva spravované soukromé koncové body do fondu SQL a SQL na vyžádání v tomto pracovním prostoru. 

Tyto dva spravované soukromé koncové body jsou uvedeny v Azure Synapse Studio. V levém navigačním panelu vyberte **Spravovat** a pak vyberte **Spravované virtuální sítě,** kterou chcete zobrazit ve studiu.

Spravované privátní koncový bod, který se zaměřuje na fond SQL se nazývá *synapse-ws-sql--\<název_pracovního\> prostoru* a ten, který se zaměřuje na SQL na vyžádání se nazývá *synapse-ws-sqlOnDemand--\<název_pracovního\>prostoru*.
![Spravované privátní koncové body pro fond SQL a SQL na vyžádání](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Tyto dva spravované soukromé koncové body se automaticky vytvoří při vytváření pracovního prostoru Azure Synapse. Za tyto dva spravované soukromé koncové body se neúčtují poplatky.

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaných privátních koncových bodů do zdrojů dat](./how-to-create-managed-private-endpoints.md)
