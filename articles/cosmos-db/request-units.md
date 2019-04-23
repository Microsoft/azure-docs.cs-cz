---
title: Jednotek žádosti a propustnosti ve službě Azure Cosmos DB
description: Další informace o tom, jak určit a odhadnout požadavky jednotek žádostí ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798959"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádosti v Azure Cosmos DB

Pomocí služby Azure Cosmos DB platíte za propustnost můžete zřizovat a úložiště, které využijete po hodinách. K zajištění, že dostatečné systémové prostředky jsou k dispozici pro vaši databázi Azure Cosmos za všech okolností musí být zřízená propustnost. Budete potřebovat dostatek prostředků, aby splňovaly nebo překračovaly [smlouvách SLA v Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB podporuje mnoho rozhraní API, jako je SQL, MongoDB, Cassandra, Gremlin a tabulky. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace v rozsahu od jednoduché bodu čte a zapisuje do složitých dotazů. Každá databázová operace využívá systémové prostředky podle složitosti operaci. 

Náklady na všechny databázové operace je normalizovány podle služby Azure Cosmos DB a je vyjádřeno pomocí *jednotek žádostí* (nebo RU, zkráceně). Si můžete představit ru za sekundu propustnosti jako měnu. Jednotek požadavku za sekundu je na základě frekvence měny. To abstrahuje systémové prostředky jako je například procesoru, vstupně-výstupních operací a paměti, které jsou nutné k provedení operací databáze podporovaných službou Azure Cosmos DB. 

Náklady na čtení 1 KB položku je 1 jednotku požadavku (nebo 1 RU). Všechny ostatní operace databáze jsou přiřazeny podobně náklady na použití rezervovaných jednotek. Bez ohledu na to, které rozhraní API používat k interakci se váš kontejner Azure Cosmos se měří podle ru vždy náklady. Zda je databáze operace zápisu, čtení, nebo dotaz, náklady na se vždy měří v RU.

Následující obrázek ukazuje základní představu o rezervovaných jednotek:

![Operace databáze spotřebovat jednotek žádostí](./media/request-units/request-units.png)

Ke správě a plánování kapacity, Azure Cosmos DB zajišťuje, že počet ru pro danou databázi operace v průběhu dané datové sadě deterministický. Hlavička odpovědi ke sledování počtu rezervovaných jednotek, které se spotřebovávají všechny operace databáze můžete zkontrolovat. Až budete znát [faktorů ovlivňujících RU poplatky](request-units.md#request-unit-considerations) a požadavkům na propustnost vaší aplikace, můžete spustit aplikaci nákladově efektivně.

Počet jednotek ru zřídíte pro vaši aplikaci na základě za sekundu v přírůstcích po 100 ru za sekundu. Pokud chcete škálovat zřízená propustnost pro vaši aplikaci, můžete zvýšit nebo snížit počet jednotek ru kdykoli. Můžete horizontálně zvýší nebo sníží 100 RU. Provedení změn můžete programově nebo prostřednictvím webu Azure portal. Se účtují po hodinách.

Můžete zřídit propustnost v přírůstcích po dvou různých: 

* **Kontejnery**: Další informace najdete v tématu [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* **Databáze**: Další informace najdete v tématu [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti

Zatímco odhadnout počet jednotek ru za sekundu na zřízení funkce vezměte v úvahu následující faktory:

* **Velikost položky**: Jak se zvyšuje velikost položky, počet ru spotřebovaných pro čtení nebo zápis položky také zvyšuje.

* **Indexování položky**: Ve výchozím nastavení je automaticky indexováno každé položky. Pokud se rozhodnete indexování některých z položek aplikace v kontejneru se spotřebuje méně rezervovaných jednotek.

* **Počet vlastností položky**: Za předpokladu, že výchozí indexování u všech vlastností, počet jednotek ru spotřebovaných pro zápis, že položka se zvyšuje s rostoucím zvýší počet vlastností položky.

* **Indexované vlastnosti**: Zásady služby index na každý kontejner určuje vlastnosti, které jsou indexovány ve výchozím nastavení. Pokud chcete snížit spotřebu RU pro operace zápisu, omezte počet indexované vlastnosti.

* **Konzistence dat**: Úrovní konzistence silná, ohraničená odolnost spotřebovávat ru přibližně dvojnásobek další při provádění operace čtení ve srovnání se, že jiných mírnější úrovně konzistence.

* **Dotazování vzory**: Složitost dotazu ovlivňuje počet jednotek ru se spotřebovávají pro operaci. Mezi faktory, které mají vliv náklady na operace dotazů patří: 
    
    - Počet výsledků dotazu
    - Počet predikátů
    - Povaha predikáty
    - Počet uživatelsky definovaných funkcí
    - Velikost zdroje dat
    - Velikosti sady výsledků dotazu
    - Projekce

  Azure Cosmos DB zaručuje, že stejný dotaz na stejná data vždy stojí stejný počet jednotek ru na opakovaných spuštění.

* **Skript použití**: Stejně jako u dotazů, uložené procedury a triggery spotřebovávat ru podle složitosti operace, které jsou prováděny. Při vývoji vaší aplikace, zkontrolujte [hlavičce požadavku](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) abyste lépe pochopili, jakou kapacitu RU využívá každé operace.

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak [zřizování propustnosti na kontejnery Azure Cosmos a databází](set-throughput.md).
* Další informace o [logické oddíly](partition-data.md).
* Další informace o tom, jak [globální škálování zřízené propustnosti](scaling-throughput.md).
* Zjistěte, jak [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
* Zjistěte, jak [najít poplatek za jednotky žádosti pro operaci](find-request-unit-charge.md).
* Zjistěte, jak [optimalizovat náklady na zřízenou propustnost v databázi Azure Cosmos DB](optimize-cost-throughput.md).
* Zjistěte, jak [optimalizaci operací čtení a zápisů nákladů ve službě Azure Cosmos DB](optimize-cost-reads-writes.md).
* Zjistěte, jak [optimalizovat náklady na dotazu ve službě Azure Cosmos DB](optimize-cost-queries.md).
