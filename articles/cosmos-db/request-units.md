---
title: Jednotek žádosti a propustnosti ve službě Azure Cosmos DB
description: Další informace o tom, jak zadat a odhadnout požadavky jednotek žádosti ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 709cd16c7eee30dd2d88ea87b5f704ad20530ffb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621339"
---
# <a name="request-units-in-azure-cosmos-db"></a>Požadované jednotky ve službě Azure Cosmos DB

Pomocí služby Azure Cosmos DB platíte za propustnost můžete zřizovat a úložiště, které využijete po hodinách. K zajištění, že dostatečné systémové prostředky jsou k dispozici pro vaši databázi Cosmos neustále aby splňovaly nebo překračovaly Cosmos DB SLA musí být zřízená propustnost.

Cosmos DB podporuje širokou škálu rozhraní API (SQL, MongoDB, Cassandra, Gremlin a tabulky). Každé rozhraní API má svou vlastní sadu databázových operací, od jednoduchého bodu čte a zapisuje do složitých dotazů. Každá databázová operace využívá systémové prostředky v závislosti na složitosti operaci.  Náklady na všechny databázové operace je normalizovány podle Cosmos DB a se vyjadřují v jednotkách žádostí (ru). Náklady na čtení 1 KB položku je 1 jednotku požadavku (1 RU). Všechny ostatní operace databáze jsou přiřazeny podobně nákladů z hlediska RU. Bez ohledu na rozhraní API, které používáte k interakci s kontejneru Cosmos a provozu databáze (zápisu, čtení, dotaz) jsou náklady na vždy měří RU.

RU/s si lze představit jako měnu propustnost. RU/s je míra měny, který abstrahuje systémové prostředky jako je například procesoru, vstupně-výstupních operací a paměti, které jsou nutné k provedení operace databáze Cosmos DB podporuje. Následující obrázek ukazuje jednotek žádosti spotřebovaná různých databázových operací:

![Databázové operace využití jednotek žádosti](./media/request-units/request-units.png)

Ke správě a plánování kapacity, Cosmos DB zajišťuje, že počet ru pro danou databázi operace v průběhu dané datové sadě deterministický. Můžete sledovat počet rezervovaných jednotek, které jsou všechny operace databáze tím, že kontroluje hlavičky odpovědi. Jakmile porozumíte faktorů ovlivňujících za jednotky žádosti a požadavkům na propustnost vaší aplikace, můžete za výhodnou cenu spuštění aplikace.

Když se účtují po hodinách, zřídíte počet jednotek ru pro vaši aplikaci na základě za sekundu v přírůstcích po 100 RU/s. Pokud chcete škálovat zřízená propustnost pro vaši aplikaci, můžete zvýšit nebo snížit počet jednotek ru (v zvýší nebo sníží 100 ru) v okamžiku, buď programově, nebo pomocí webu Azure portal.

Můžete zřídit propustnost v přírůstcích po dvou různých: 

* **Kontejnery**. Další informace najdete v tématu [jak zřídit propustnosti kontejneru Cosmos.](how-to-provision-container-throughput.md)
* **Databáze**. Další informace najdete v tématu [jak zřídit propustnost v databázi Cosmos.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti

Při odhadování počet RU/s na zřízení funkce, je důležité vzít v úvahu následující faktory:

* **Velikost položky** – jak se zvyšuje velikost položky, počet ru spotřebovaných pro čtení nebo zápis položky také zvyšuje.

* **Indexování dokumentů** – ve výchozím nastavení, je automaticky indexováno každé položky. Menší počet jednotek žádostí se spotřebuje, pokud se rozhodnete indexování některých z položek aplikace v kontejneru.

* **Počet vlastností položky** – za předpokladu, že výchozí indexování u všech vlastností, počet jednotek ru spotřebovaných pro zápis položky se zvyšuje s rostoucím zvýší počet vlastností položky.

* **Indexované vlastnosti** -Určuje zásadu index na každý kontejner vlastnosti, které jsou indexovány ve výchozím nastavení. Můžete snížit spotřebu jednotek žádosti pro operace zápisu tím, že omezíte počet indexované vlastnosti.

* **Konzistence dat** -úrovní konzistence silná, ohraničená odolnost využívat přibližně 2 × více rezervovaných jednotek při provádění operace čtení ve srovnání se, že jiných mírnější úrovně konzistence.

* **Dotazování vzory** -složitost dotazu má vliv na tom, kolik jednotek žádosti se spotřebovávají pro operaci. Nastavte počet výsledků dotazu, počet predikátů, povaze predikáty, číslo uživatelem definované funkce, velikost zdroje dat, velikost výsledku a projekce ovlivnit náklady na operace dotazů. Cosmos DB zaručuje, že stejný dotaz na stejná data budou vždy náklady na stejný počet jednotek ru na při opakovaném spuštění.

* **Skript použití** – s dotazy, uložené procedury a triggery spotřebovávat ru podle složitosti prováděných operací. Při vývoji vaší aplikace, kontrolovat hlavičky požadavku poplatek lépe pochopit, jakou kapacitu jednotek žádosti využívá každé operace.

## <a name="next-steps"></a>Další postup

* Další informace o [zřizování propustnosti pro kontejnery služby Cosmos DB a databáze](set-throughput.md)
* Další informace o [Llogical oddílů](partition-data.md)
* Další informace o [globální škálování zřízená propustnost](scaling-throughput.md)
* Přečtěte si [jak zřídit propustnosti kontejneru Cosmos](how-to-provision-container-throughput.md)
* Přečtěte si [jak zřídit propustnost v databázi Cosmos](how-to-provision-database-throughput.md)
