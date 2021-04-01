---
title: Migrace dat z Cassandra do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz
description: Naučte se migrovat data z databáze Apache Cassandra do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: c26d21e74e9808fe65890b7f4eba31ee742552a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339981"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrace dat z Cassandra na účet Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB se stal skvělou volbou pro podniková zatížení, která běží na Apache Cassandra, z nejrůznějších důvodů: 

* **Žádná režie správy a monitorování:** Eliminuje režijní náklady na správu a monitorování nesčetných nastavení napříč systémy souborů OS, JVM a YAML a jejich interakcemi.

* **Významné úspory nákladů:** Náklady můžete ušetřit pomocí Azure Cosmos DB, což zahrnuje náklady na virtuální počítač, šířku pásma a příslušné licence. Navíc nemusíte spravovat datové centra, servery, úložiště SSD, sítě a náklady na elektřinu. 

* **Možnost použít existující kód a nástroje:** Azure Cosmos DB poskytuje kompatibilitu na úrovni přenosů protokolů se stávajícími sadami SDK a nástroji Cassandra. Tato kompatibilita zajišťuje, že můžete použít stávající základ kódu s rozhraním API Cassandra v Azure Cosmos DB s nepatrnými změnami.

Existují různé způsoby, jak migrovat databázové úlohy z jedné platformy do jiné. [Blitzz](https://www.blitzz.io) je nástroj, který nabízí zabezpečený a spolehlivý způsob, jak provést migraci s nulovými výpadky z různých databází, aby se Azure Cosmos DB. Tento článek popisuje kroky potřebné k migraci dat z databáze Apache Cassandra do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Výhody použití Blitzz pro migraci

Řešení migrace Blitzz se řídí postupem, jak migrovat složitá provozní zatížení. Tady jsou některé klíčové aspekty plánu migrace v rámci nulového výpadku Blitzz:

* Nabízí automatickou migraci obchodní logiky (tabulky, indexy, zobrazení) z databáze Apache Cassandra do Azure Cosmos DB. Schémata nemusíte vytvářet ručně.

* Blitzz nabízí vysoce velký objem a paralelní replikaci databáze. Umožňuje, aby zdrojové i cílové platformy byly během migrace synchronizované pomocí techniky označované jako Change-Data-Capture (CDC). Pomocí funkce CDC Blitzz nepřetržitě vyžádá datový proud změn ze zdrojové databáze (Apache Cassandra) a použije ho pro cílovou databázi (Azure Cosmos DB).

* Je odolná proti chybám a poskytuje přesně jedno doručení dat, i když dojde v systému k selhání hardwaru nebo softwaru.

* Zabezpečuje data během přenosu s využitím nejrůznějších metod zabezpečení, jako je TLS, šifrování.

## <a name="steps-to-migrate-data"></a>Postup migrace dat

Tato část popisuje kroky potřebné k nastavení Blitzz a migraci dat z databáze Apache Cassandra na Azure Cosmos DB.

1. Z počítače, do kterého plánujete nainstalovat Blitzz replicant, přidejte certifikát zabezpečení. Tento certifikát vyžaduje Blitzz replicant k navázání připojení TLS se zadaným účtem Azure Cosmos DB. Certifikát můžete přidat pomocí následujících kroků:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Instalaci Blitzz a binární soubory můžete získat buď tak, že si vyžádáte ukázku na [webu Blitzz](https://www.blitzz.io). Alternativně můžete také odeslat [e-mail](mailto:success@blitzz.io) týmu.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Stažení nástroje Blitzz replicant Tool":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Soubory Blitzz replicant":::

1. V terminálu CLI nastavte konfiguraci zdrojové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/cassandra.yml`** příkazu a přidejte čárkami oddělený seznam IP adres uzlů Cassandra, číslo portu, uživatelské jméno, heslo a další požadované podrobnosti. Následuje příklad obsahu v konfiguračním souboru:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="Otevřít Editor připojení Cassandra":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Konfigurace připojení Cassandra":::

   Po vyplnění podrobností konfigurace soubor uložte a zavřete.

1. Volitelně můžete nastavit soubor filtru zdrojové databáze. Soubor filtru určuje, která schémata nebo tabulky se mají migrovat. Otevřete konfigurační soubor pomocí **`vi filter/cassandra_filter.yml`** příkazu a zadejte následující konfigurační údaje:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Po vyplnění podrobností filtru databáze uložte a zavřete soubor.

1. V dalším kroku nastavíte konfiguraci cílové databáze. Před definováním konfigurace [vytvořte účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account) a pak vytvořte místo na disku a tabulku pro uložení migrovaných dat. Vzhledem k tomu, že migrujete z Apache Cassandra na rozhraní API Cassandra v Azure Cosmos DB, můžete použít stejný klíč oddílu, který jste použili s Apache Cassandra.

1. Před migrací dat zvyšte propustnost kontejneru na množství potřebné k rychlé migraci vaší aplikace. Můžete například zvýšit propustnost na 100000 ru. Škálování propustnosti před zahájením migrace vám pomůže s migrací dat za kratší dobu.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Škálování Cosmos kontejneru Azure v celém":::

   Snižte propustnost po dokončení migrace. Na základě množství uložených dat a ru vyžadovaných pro jednotlivé operace můžete odhadnout propustnost vyžadovanou po migraci dat. Další informace o tom, jak odhadovat požadované ru, najdete v tématech [zřízení propustnosti pro kontejnery a databáze](set-throughput.md) a [odhad ru/s pomocí článků Azure Cosmos DB kapacity pro plánování](estimate-ru-with-capacity-planner.md) .

1. V podokně **připojovací řetězec** Získejte **kontaktní bod, port, uživatelské jméno** a **primární heslo** účtu Azure Cosmos. Tyto hodnoty použijete v konfiguračním souboru.

1. V terminálu CLI nastavte konfiguraci cílové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/cosmosdb.yml`** příkazu a přidejte čárkami oddělený seznam identifikátorů URI hostitele, číslo portu, uživatelské jméno, heslo a další požadované parametry. Následující příklad ukazuje obsah konfiguračního souboru:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Dále migrujte data pomocí Blitzz. Blizz replicant můžete spustit v režimu **úplného** nebo **snímku** :

   * **Úplný režim** – v tomto režimu se replicant i nadále spouští po migraci a naslouchá jakýmkoli změnám ve zdrojovém systému Apache Cassandra. Pokud se nějaké změny zjistí, replikují se v reálném čase na cílový účet Azure Cosmos.

   * **Režim snímků** – v tomto režimu můžete provést migraci schématu a jednorázovou replikaci dat. Replikace v reálném čase není touto možností podporována.

   Pomocí předchozích dvou režimů se dá migrace provést bez výpadků. 

1. K migraci dat z terminálu Blitzz replicant CLI spusťte následující příkaz:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   Uživatelské rozhraní replicant zobrazuje průběh replikace. Po dokončení operace migrace schématu a snímku se zobrazí průběh 100%. Po dokončení migrace můžete ověřit data v cílové databázi Azure Cosmos.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Výstup migrace dat Cassandra":::


1. Vzhledem k tomu, že jste použili úplný režim migrace, můžete provádět operace, jako je například vložení, aktualizace nebo odstranění dat ve zdrojové databázi Apache Cassandra. Později ověřte, jestli jsou replikované v reálném čase v cílové databázi Azure Cosmos. Po dokončení migrace nezapomeňte snížit propustnost nakonfigurovanou pro váš kontejner Azure Cosmos.

1. Replicant můžete zastavit v jakémkoli bodě a restartovat ho pomocí přepínače **--Resume** . Replikace se obnoví z bodu, který se zastavil, aniž by došlo k narušení konzistence dat. Následující příkaz ukazuje, jak použít přepínač Resume.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Další informace o migraci dat do cíle v reálném čase najdete v [ukázce Blitzz replicant](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy pro klíč oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků Azure Cosmos DB Capacity Planneru](estimate-ru-with-capacity-planner.md)
