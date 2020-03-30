---
title: Migrace dat z cassandry do rozhraní API Azure Cosmos DB Cassandra pomocí blitzzu
description: Zjistěte, jak migrovat data z databáze Apache Cassandra do rozhraní API Azure Cosmos DB Cassandra pomocí blitzzu.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 746232a85e326f08d44cf2dbe0a4ae718c7cc312
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984365"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrace dat z účtu Rozhraní API Azure Cosmos DB Cassandra pomocí Blitzzu

Rozhraní Cassandra API v Azure Cosmos DB se stalo skvělou volbou pro podnikové úlohy spuštěné na Apache Cassandra z různých důvodů, jako jsou: 

* **Žádné režijní náklady na správu a monitorování:** Eliminuje režii správy a monitorování nesčetné množství nastavení v rámci os, JVM a yaml soubory a jejich interakce.

* **Významné úspory nákladů:** Můžete ušetřit náklady s Azure Cosmos DB, který zahrnuje náklady na virtuální počítače, šířku pásma a všechny příslušné licence. Navíc nemusíte spravovat datová centra, servery, úložiště SSD, sítě a náklady na elektřinu. 

* **Možnost použití existujícího kódu a nástrojů:** Azure Cosmos DB poskytuje kompatibilitu na úrovni drátového protokolu se stávajícími sadami Cassandra SDK a nástroji. Tato kompatibilita zajišťuje, že můžete použít stávající základ kódu s rozhraním API Cassandra v Azure Cosmos DB s nepatrnými změnami.

Existují různé způsoby migrace databázových úloh z jedné platformy na druhou. [Blitzz](https://www.blitzz.io) je nástroj, který nabízí bezpečný a spolehlivý způsob, jak provádět nulovou migraci prostojů z různých databází do Azure Cosmos DB. Tento článek popisuje kroky potřebné k migraci dat z databáze Apache Cassandra do rozhraní API Azure Cosmos DB Cassandra pomocí Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Výhody využívající Blitzz pro migraci

Řešení migrace blitzz ubírá krok za krokem k migraci složitých provozních úloh. Níže jsou uvedeny některé z klíčových aspektů blitzz je zero-prostoje migrační plán:

* Nabízí automatickou migraci obchodní logiky (tabulky, indexy, zobrazení) z databáze Apache Cassandra do Azure Cosmos DB. Schémata není nutné vytvářet ručně.

* Blitzz nabízí velkoobjemovou a paralelní replikaci databáze. Umožňuje zdrojové i cílové platformy, které mají být synchronizovány během migrace pomocí techniky s názvem Change-Data-Capture (CDC). Pomocí CDC Blitzz průběžně natahuje proud změn ze zdrojové databáze (Apache Cassandra) a použije jej na cílovou databázi (Azure Cosmos DB).

* Je odolný proti chybám a zaručuje přesně jednou dodání dat i při selhání hardwaru nebo softwaru v systému.

* Zabezpečuje data během přenosu pomocí různých metodik zabezpečení, jako je SSL, šifrování.

## <a name="steps-to-migrate-data"></a>Postup migrace dat

Tato část popisuje kroky potřebné k nastavení Blitzz a migruje data z databáze Apache Cassandra do Azure Cosmos DB.

1. Z počítače, kde plánujete nainstalovat replikant Blitzz, přidejte bezpečnostní certifikát. Tento certifikát je vyžadován replikantblitzz navázat připojení SSL se zadaným účtem Azure Cosmos DB. Certifikát můžete přidat pomocí následujících kroků:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Můžete získat Blitzz instalaci a binární soubory buď tím, že žádá demo na [webových stránkách Blitzz](https://www.blitzz.io). Případně můžete také poslat [e-mail](mailto:success@blitzz.io) týmu.

   ![Blitzz replikant nástroj ke stažení](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replikantsoubory](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. Z terminálu CLI nastavte konfiguraci zdrojové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/cassandra.yml`** příkazu a přidejte seznam IP adres uzlů Cassandra, číslo portu, uživatelské jméno, heslo a další požadované podrobnosti. Následuje příklad obsahu v konfiguračním souboru:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![Otevřít editor připojení Cassandra](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Konfigurace připojení Cassandra](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   Po vyplnění podrobností o konfiguraci soubor uložte a zavřete.

1. Volitelně můžete nastavit soubor filtru zdrojové databáze. Soubor filtru určuje, která schémata nebo tabulky mají být migrovány. Otevřete konfigurační soubor pomocí **`vi filter/cassandra_filter.yml`** příkazu a zadejte následující podrobnosti konfigurace:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Po vyplnění podrobností filtru databáze uložte a zavřete soubor.

1. Dále nastavíte konfiguraci cílové databáze. Před definováním konfigurace [vytvořte účet rozhraní API Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account) a pak vytvořte Keyspace a tabulku pro uložení migrovaných dat. Vzhledem k tomu, že migrujete z Apache Cassandra do rozhraní CASSandry API v Azure Cosmos DB, můžete použít stejný klíč oddílu, který jste použili s Apache Cassandra.

1. Před migrací dat zvyšte propustnost kontejneru na částku potřebnou k rychlé migraci aplikace. Můžete například zvýšit propustnost na 100000 ru. Změna velikosti propustnost před zahájením migrace vám pomůže migrovat data v kratším čase.

   ![Škálování kontejneru Azure Cosmos v celém](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   Po dokončení migrace snižte propustnost. Na základě množství dat uložených a ru požadovaných pro každou operaci můžete odhadnout propustnost požadovanou po migraci dat. Další informace o tom, jak odhadnout požadované ru, najdete v článku [Zřízení propustnosti pro kontejnery a databáze](set-throughput.md) a [odhadru/s pomocí článků plánovač kapacity Azure Cosmos DB.](estimate-ru-with-capacity-planner.md)

1. Získejte **kontaktní místo, port, uživatelské jméno**a primární **heslo** účtu Azure Cosmos z podokna **Připojovací řetězec.** Tyto hodnoty použijete v konfiguračním souboru.

1. Z terminálu CLI nastavte konfiguraci cílové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/cosmosdb.yml`** příkazu a přidejte seznam identifikátorů URI, čísla portu, uživatelského jména, hesla a dalších požadovaných parametrů oddělených čárkami. Následující příklad ukazuje obsah konfiguračního souboru:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Další migrovat data pomocí Blitzz. Replikant Blizz můžete spustit v **režimu úplného** **nebo snímku:**

   * **Plný režim** – V tomto režimu replicant pokračuje v běhu po migraci a naslouchá všem změnám ve zdrojovém systému Apache Cassandra. Pokud zjistí všechny změny, jsou replikovány na cílovém účtu Azure Cosmos v reálném čase.

   * **Režim snímek** – v tomto režimu můžete provést migraci schématu a jednorázovou replikaci dat. Tato možnost není podporována replikace v reálném čase.

   Pomocí výše uvedených dvou režimů lze migraci provést s nulovými prostoji. 

1. Chcete-li migrovat data, spusťte z terminálu blitzzského replikantského příkazu CLI následující příkaz:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   Replikační ui zobrazuje průběh replikace. Po dokončení migrace schématu a operace snímku se zobrazí průběh 100 %. Po dokončení migrace můžete ověřit data v cílové databázi Azure Cosmos.

   ![Výstup migrace dat Cassandra](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. Vzhledem k tomu, že jste pro migraci použili úplný režim, můžete provádět operace, jako je vkládání, aktualizace nebo odstraňování dat ve zdrojové databázi Apache Cassandra. Později ověřte, že jsou replikovány v reálném čase v cílové databázi Azure Cosmos. Po migraci nezapomeňte snížit propustnost nakonfigurovanou pro váš kontejner Azure Cosmos.

1. Replikant můžete zastavit libovolný bod a restartovat jej pomocí **přepínače --resume.** Replikace pokračuje od bodu, který byl zastaven bez ohrožení konzistence dat. Následující příkaz ukazuje, jak používat přepínač životopisu.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Další informace o migraci dat do cíle v reálném čase najdete v [tématu Ukázka replikantu Blitzz](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy klíče oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků plánovačkapacity Azure Cosmos DB](estimate-ru-with-capacity-planner.md)