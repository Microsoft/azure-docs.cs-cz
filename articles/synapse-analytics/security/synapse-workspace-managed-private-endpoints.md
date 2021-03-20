---
title: Spravované privátní koncové body
description: Článek s vysvětlením spravovaných privátních koncových bodů ve službě Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569942"
---
# <a name="synapse-managed-private-endpoints"></a>Synapse spravované privátní koncové body

V tomto článku se vysvětlují spravované privátní koncové body ve službě Azure synapse Analytics.

## <a name="managed-private-endpoints"></a>Spravované privátní koncové body

Spravované privátní koncové body jsou soukromé koncové body vytvořené ve spravovaném Virtual Network přidruženém k vašemu pracovnímu prostoru Azure synapse. Spravované soukromé koncové body vytvoří privátní odkaz na prostředky Azure. Azure synapse tyto privátní koncové body spravuje vaším jménem. Můžete vytvořit spravované privátní koncové body z vašeho pracovního prostoru Azure synapse a získat přístup ke službám Azure (například Azure Storage nebo Azure Cosmos DB) a hostovaným zákazníkům a partnerským službám Azure.

Pokud používáte spravované privátní koncové body, provoz mezi vaším pracovním prostorem Azure synapse a dalšími prostředky Azure prochází výhradně přes páteřní síť Microsoftu. Spravované privátní koncové body chrání před exfiltrace dat. Spravovaný privátní koncový bod používá privátní IP adresu ze spravovaného Virtual Network k účinnému uvedení služby Azure, kterou váš pracovní prostor Azure synapse komunikuje do vašeho Virtual Network. Spravované privátní koncové body jsou namapovány na konkrétní prostředek v Azure, nikoli na celou službu. Zákazníci můžou omezit připojení ke konkrétnímu prostředku schválenému jeho organizací. 

Přečtěte si další informace o [privátních odkazech a soukromých koncových bodech](../../private-link/index.yml).

>[!IMPORTANT]
>Spravované privátní koncové body jsou podporované jenom v pracovních prostorech Azure synapse se spravovaným pracovním prostorem Virtual Network.

>[!NOTE]
>Při vytváření pracovního prostoru Azure synapse se můžete rozhodnout, že k němu chcete přidružit spravované Virtual Network. Pokud se rozhodnete ke svému pracovnímu prostoru přidružit spravovaný Virtual Network, můžete také omezit odchozí přenosy z vašeho pracovního prostoru jenom na schválené cíle. K těmto cílům musíte vytvořit spravované privátní koncové body. 


Při vytváření spravovaného privátního koncového bodu ve službě Azure synapse se připojení privátního koncového bodu vytvoří ve stavu čeká na vyřízení. Je spuštěný pracovní postup schvalování. Vlastník prostředku privátního propojení zodpovídá za schválení nebo odmítnutí připojení. Pokud vlastník připojení schválí, vytvoří se privátní odkaz. Pokud ale vlastník připojení neschválí, privátní odkaz se nevytvoří. V obou případech se spravovaný privátní koncový bod aktualizuje se stavem připojení. K odeslání provozu do prostředku privátního propojení, který je propojen se spravovaným koncovým bodem, lze použít pouze spravovaný privátní koncový bod ve schváleném stavu.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Spravované soukromé koncové body pro vyhrazený fond SQL a SQL Server bez serveru

Vyhrazený fond SQL a fond SQL bez serveru jsou analytické možnosti v pracovním prostoru Azure synapse. Tyto možnosti využívají víceklientské infrastruktury, která není nasazená do [spravovaného pracovního prostoru Virtual Network](./synapse-workspace-managed-vnet.md).

Po vytvoření pracovního prostoru vytvoří Azure synapse dva spravované privátní koncové body v pracovním prostoru, jednu pro vyhrazený fond SQL a jednu pro fond SQL bez serveru. 

Tyto dva spravované privátní koncové body jsou uvedeny v synapse studiu. V levém navigačním panelu vyberte **Spravovat** a potom vyberte **spravované privátní koncové body** , které se zobrazí v studiu.

Spravovaný soukromý koncový bod, který cílí na fond SQL, se nazývá *synapse-WS- \<workspacename\> SQL--* a ten, který cílí na SQL fond bez serveru, se nazývá *synapse- \<workspacename\> WS-sqlOnDemand--*.

![Spravované soukromé koncové body pro vyhrazený fond SQL a SQL Server bez serveru](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Při vytváření pracovního prostoru Azure synapse se automaticky vytvoří tyto dva spravované privátní koncové body. Neúčtují se vám poplatky za tyto dva spravované privátní koncové body.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přejděte k článku [Vytvoření spravovaných privátních koncových bodů do vašich zdrojů dat](./how-to-create-managed-private-endpoints.md) .