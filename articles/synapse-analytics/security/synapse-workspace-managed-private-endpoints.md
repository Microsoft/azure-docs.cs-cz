---
title: Spravované privátní koncové body
description: Článek s vysvětlením spravovaných privátních koncových bodů ve službě Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: dc748260c6e71eaaa469defb227d5cc2748a1345
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83645309"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Spravované privátní koncové body synapse (Preview)

V tomto článku se vysvětlují spravované privátní koncové body ve službě Azure synapse Analytics.

## <a name="managed-private-endpoints"></a>Spravované privátní koncové body

Spravované privátní koncové body jsou privátní koncové body vytvořené ve virtuální síti spravovaného pracovního prostoru a vytvářejí privátní odkaz na prostředky Azure. Azure synapse tyto privátní koncové body spravuje vaším jménem.

Azure synapse podporuje privátní odkazy. Privátní odkaz vám umožní přístup ke službám Azure (například Azure Storage, Azure Cosmos DB a Azure SQL Data Warehouse) a službám Azure Hosted Customer/Partnerská služba z vaší virtuální sítě Azure.

Když použijete privátní odkaz, přenosy mezi vaší virtuální sítí a pracovním prostorem procházejí výhradně přes páteřní síť Microsoftu. Soukromé propojení chrání před riziky exfiltrace dat. Vytvořením privátního koncového bodu vytvoříte privátní odkaz na prostředek.

Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě k efektivnímu uvedení služby do vaší virtuální sítě. Soukromé koncové body jsou namapované na konkrétní prostředek v Azure, a ne na celou službu. Zákazníci můžou omezit připojení ke konkrétnímu prostředku schválenému jeho organizací. Přečtěte si další informace o [privátních odkazech a soukromých koncových bodech](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Spravované privátní koncové body jsou podporované jenom v pracovních prostorech Azure synapse se spravovanými virtuálními sítěmi pracovního prostoru.

>[!NOTE]
>Všechny odchozí přenosy z virtuální sítě spravovaného pracovního prostoru s výjimkou spravovaných privátních koncových bodů budou v budoucnu blokované. Doporučuje se vytvořit spravované privátní koncové body pro připojení ke všem zdrojům dat Azure, které jsou externí pro daný pracovní prostor. 

Při vytváření spravovaného privátního koncového bodu ve službě Azure synapse se připojení privátního koncového bodu vytvoří ve stavu čeká na vyřízení. Byl zahájen pracovní postup schválení. Vlastník prostředku privátního propojení zodpovídá za schválení nebo odmítnutí připojení.

Pokud vlastník připojení schválí, vytvoří se privátní odkaz. V opačném případě se privátní odkaz nevytvoří. V obou případech se spravovaný privátní koncový bod aktualizuje se stavem připojení.

Pouze spravovaný privátní koncový bod ve schváleném stavu může odesílat provoz do daného prostředku privátního propojení.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Spravované soukromé koncové body pro fond SQL a SQL na vyžádání

SQL fond a SQL na vyžádání jsou analytické možnosti v pracovním prostoru Azure synapse. Tyto možnosti využívají víceklientské infrastruktury, která není nasazená do [virtuální sítě spravovaného pracovního prostoru](./synapse-workspace-managed-vnet.md).

Když se vytvoří pracovní prostor, vytvoří Azure synapse dva spravované privátní koncové body pro fond SQL a SQL na vyžádání v daném pracovním prostoru. 

Tyto dva spravované privátní koncové body jsou uvedené v Azure synapse studiu. V levém navigačním panelu vyberte **Spravovat** a potom v nástroji Studio vyberte **spravované virtuální sítě** .

Spravovaný privátní koncový bod, který cílí na fond SQL, se nazývá *synapse-WS-SQL--název \< pracovního prostoru \> * a ta, která cílí na SQL na vyžádání, se nazývá *synapse-WS-sqlOnDemand \< \> --Workspace*.
![Spravované soukromé koncové body pro fond SQL a SQL na vyžádání](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Při vytváření pracovního prostoru Azure synapse se automaticky vytvoří tyto dva spravované privátní koncové body. Neúčtují se vám poplatky za tyto dva spravované privátní koncové body.

## <a name="next-steps"></a>Další kroky

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
