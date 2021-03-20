---
title: Mapování úrovní konzistence pro rozhraní Azure Cosmos DB API pro MongoDB
description: Mapování úrovní konzistence pro rozhraní Azure Cosmos DB API pro MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333150"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Úrovně konzistence pro Azure Cosmos DB a rozhraní API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Na rozdíl od Azure Cosmos DB nativní MongoDB neposkytuje přesně definované záruky konzistence. Místo toho nativní MongoDB umožňuje uživatelům konfigurovat následující záruky konzistence: informace o zápisu, problém týkající se čtení a direktiva The-Master – k nasměrování operací čtení do primární nebo sekundární repliky za účelem dosažení požadované úrovně konzistence.

Pokud používáte rozhraní API Azure Cosmos DB pro MongoDB, ovladač MongoDB považuje vaši oblast zápisu za primární repliku a všechny ostatní oblasti jsou repliky čtení. Můžete zvolit, která oblast je přidružená k vašemu účtu Azure Cosmos, jako primární repliku.

> [!NOTE]
> Výchozím modelem konzistence pro Azure Cosmos DB je relace. Relace je model konzistence orientovaný na klienta, který není nativně podporován buď Cassandra nebo MongoDB. Další informace o tom, jaký model konzistence se má zvolit, najdete [v tématu úrovně konzistence v Azure Cosmos DB](consistency-levels.md)

Při použití rozhraní API Azure Cosmos DB pro MongoDB:

* V části týkající se zápisu je namapována na výchozí úroveň konzistence nakonfigurovanou v účtu Azure Cosmos.

* Azure Cosmos DB bude k dis dynamicky mapovat obavy týkající se čtení určené ovladačem klienta MongoDB na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovanou dynamicky pro žádost o čtení.  

* Konkrétní oblast, která je přidružená k účtu Azure Cosmos, můžete označit jako "primární" tím, že tuto oblast nastavíte jako první zapisovatelnou oblast. 

## <a name="mapping-consistency-levels"></a>Mapování úrovní konzistence

Následující tabulka ukazuje, jak se při použití rozhraní Azure Cosmos DB API pro MongoDB namapují nativní problémy zápisu a čtení nativního MongoDB na úrovně konzistence Azure Cosmos:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapování modelu konzistence MongoDB" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Pokud je váš účet Azure Cosmos nakonfigurovaný s úrovní konzistence jinou než silná konzistence, můžete zjistit pravděpodobnost, že klienti mohou získat silné a konzistentní čtení pro vaše úlohy, a to tak, že se podíváte na metriku služby PBS ( *probabilistically Bounded* ). Tato metrika se zveřejňuje v Azure Portal. Další informace najdete v tématu [monitorování metriky služby PBS (probabilistically Bounded)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní s ohraničenou neaktuálností ukazuje, jak je to vaše konečná konzistence. Tato metrika poskytuje přehled o tom, jak často můžete získat silnější konzistenci než úroveň konzistence, kterou jste v účtu Azure Cosmos aktuálně nakonfigurovali. Jinými slovy můžete zobrazit pravděpodobnost (měřeno v milisekundách) pro získání silně konzistentních čtení pro kombinaci oblastí zápisu a čtení.

## <a name="next-steps"></a>Další kroky

Další informace o globální distribuci a úrovních konzistence pro Azure Cosmos DB:

* [Přehled globální distribuce](distribute-data-globally.md)
* [Přehled úrovně konzistence](consistency-levels.md)
* [Vysoká dostupnost](high-availability.md)
