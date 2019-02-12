---
title: Jednotek žádosti a propustnosti ve službě Azure Cosmos DB
description: Další informace o tom, jak určit a odhadnout požadavky jednotek žádostí ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 3801c19fbef70bf5d67670c4d9acc950291853e6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990151"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádosti v Azure Cosmos DB

Pomocí služby Azure Cosmos DB platíte za propustnost můžete zřizovat a úložiště, které využijete po hodinách. K zajištění, že dostatečné systémové prostředky jsou k dispozici pro vaši databázi Azure Cosmos neustále musí být zřízená propustnost. Budete potřebovat dostatek prostředků, aby splňovaly nebo překračovaly smlouvy SLA pro Azure Cosmos DB.

Azure Cosmos DB podporuje mnoho rozhraní API, jako je SQL, MongoDB, Cassandra, Gremlin a tabulky. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace v rozsahu od jednoduché bodu čte a zapisuje do složitých dotazů. Každá databázová operace využívá systémové prostředky podle složitosti operaci. 

Náklady na všechny databázové operace je normalizovány podle služby Azure Cosmos DB a je vyjádřena v jednotkách žádostí (ru). Náklady na čtení 1 KB položku je 1 jednotku požadavku (RU). Všechny ostatní operace databáze podobně jsou přiřazeny s náklady přes RU. Bez ohledu na to, které rozhraní API používat k interakci se váš kontejner Azure Cosmos se měří podle ru vždy náklady. Zda je databáze operace zápisu, čtení, nebo dotaz, náklady na se vždy měří v RU.

Si můžete představit ru za sekundu propustnosti jako měnu. Jednotek požadavku za sekundu je na základě frekvence měny. To abstrahuje systémové prostředky jako je například procesoru, vstupně-výstupních operací a paměti, které jsou nutné k provedení operací databáze podporovaných službou Azure Cosmos DB. Následující obrázek ukazuje počet ru spotřebovaných podle různých databázových operací:

![Operace databáze spotřebovat jednotek žádostí](./media/request-units/request-units.png)

Ke správě a plánování kapacity, Azure Cosmos DB zajišťuje, že počet ru pro danou databázi operace v průběhu dané datové sadě deterministický. Prozkoumejte hlavička odpovědi ke sledování počtu rezervovaných jednotek, které se spotřebovávají všechny operace databáze. Až porozumíte faktorů ovlivňujících poplatky RU a požadavkům na propustnost vaší aplikace, můžete efektivně spustit vaše náklady na aplikaci.

Bude se vám účtovat po hodinách. Počet jednotek ru zřídíte pro vaši aplikaci na základě za sekundu v přírůstcích po 100 ru za sekundu. Pokud chcete škálovat zřízená propustnost pro vaši aplikaci, můžete zvýšit nebo snížit počet jednotek ru kdykoli. Proveďte požadované změny v zvýší nebo sníží 100 RU. Provedení změn můžete programově nebo prostřednictvím webu Azure portal.

Můžete zřídit propustnost v přírůstcích po dvou různých: 

* **Kontejnery**. Další informace najdete v tématu [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* **Databáze**. Další informace najdete v tématu [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti

Zatímco odhadnout počet jednotek ru za sekundu na zřízení funkce vezměte v úvahu následující faktory:

* **Velikost položky**: Jak se zvyšuje velikost položky, počet ru spotřebovaných pro čtení nebo zápis položky také zvyšuje.

* **Indexování položky**: Ve výchozím nastavení je automaticky indexováno každé položky. Pokud se rozhodnete indexování některých z položek aplikace v kontejneru se spotřebuje méně rezervovaných jednotek.

* **Počet vlastností položky**: Za předpokladu, že výchozí indexování u všech vlastností, počet jednotek ru spotřebovaných pro zápis, že položka se zvyšuje s rostoucím zvýší počet vlastností položky.

* **Indexované vlastnosti**: Zásady služby index na každý kontejner určuje vlastnosti, které jsou indexovány ve výchozím nastavení. Pokud chcete snížit spotřebu RU pro operace zápisu, omezte počet indexované vlastnosti.

* **Konzistence dat**: Úrovní konzistence silná, ohraničená odolnost spotřebovávat ru přibližně dvojnásobek další při provádění operace čtení ve srovnání se, že jiných mírnější úrovně konzistence.

* **Dotazování vzory**: Složitost dotazu ovlivňuje počet jednotek ru se spotřebovávají pro operaci. Mezi faktory, které mají vliv náklady na operace dotazů patří: 
    
    - Počet výsledků dotazu.
    - Počet predikátů.
    - Povaha predikáty.
    - Číslo uživatelem definované funkce.
    - Velikost zdrojová data.
    - Velikost sady výsledků dotazu.
    - Projekce.

  Azure Cosmos DB zaručuje, že stejný dotaz na stejná data vždy stojí stejný počet jednotek ru na opakovaných spuštění.

* **Skript použití**: Stejně jako u dotazů, uložené procedury a triggery spotřebovávat ru podle složitosti operace, které jsou prováděny. Při vývoji vaší aplikace, kontrolovat hlavičky požadavku poplatek abyste lépe pochopili, jakou kapacitu RU využívá každé operace.

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak [zřizování propustnosti pro kontejnery Azure Cosmos a databáze](set-throughput.md).
* Další informace o [logické oddíly](partition-data.md).
* Další informace o tom, jak [globální škálování zřízené propustnosti](scaling-throughput.md).
* Zjistěte, jak [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
