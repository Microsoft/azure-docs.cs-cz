---
title: Úrovně konzistence Apache Cassandra a Azure Cosmos DB
description: Cassandra Apache a Azure Cosmos DB úrovně konzistence.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100422"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Úrovně konzistence Apache Cassandra a Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Na rozdíl od Azure Cosmos DB Apache Cassandra nativně neposkytuje přesně definované záruky konzistence. Místo toho Apache Cassandra poskytuje úroveň konzistence pro zápis a úroveň konzistence pro čtení, aby bylo možné dosáhnout vysoké dostupnosti, konzistence a kompromisů latence. Při použití rozhraní API Cassandra Azure Cosmos DB:

* Úroveň konzistence zápisu Apache Cassandra je namapovaná na výchozí úroveň konzistence nakonfigurovanou na vašem účtu Azure Cosmos. Konzistence operace zápisu (CL) se u jednotlivých požadavků nedá změnit.

* Azure Cosmos DB bude dynamicky mapovat úroveň konzistence čtení určenou ovladačem klienta Cassandra na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovaných dynamicky pro požadavek na čtení.

## <a name="mapping-consistency-levels"></a>Mapování úrovní konzistence

Následující tabulka ukazuje, jak jsou nativní Cassandra úrovně konzistence mapovány na úrovně konzistence Azure Cosmos DB při použití rozhraní API Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapování modelu konzistence Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Pokud je váš účet Azure Cosmos nakonfigurovaný s úrovní konzistence jinou než silná konzistence, můžete zjistit pravděpodobnost, že klienti mohou získat silné a konzistentní čtení pro vaše úlohy, a to tak, že se podíváte na metriku služby PBS ( *probabilistically Bounded* ). Tato metrika se zveřejňuje v Azure Portal. Další informace najdete v tématu [monitorování metriky služby PBS (probabilistically Bounded)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní s ohraničenou neaktuálností ukazuje, jak je to vaše konečná konzistence. Tato metrika poskytuje přehled o tom, jak často můžete získat silnější konzistenci než úroveň konzistence, kterou jste v účtu Azure Cosmos aktuálně nakonfigurovali. Jinými slovy můžete zobrazit pravděpodobnost (měřeno v milisekundách) pro získání silně konzistentních čtení pro kombinaci oblastí zápisu a čtení.

## <a name="next-steps"></a>Další kroky

Další informace o globální distribuci a úrovních konzistence pro Azure Cosmos DB:

* [Přehled globální distribuce](distribute-data-globally.md)
* [Přehled úrovně konzistence](consistency-levels.md)
* [Vysoká dostupnost](high-availability.md)
