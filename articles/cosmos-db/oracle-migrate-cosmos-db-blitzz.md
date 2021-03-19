---
title: Migrace dat ze Oracle do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz
description: Naučte se migrovat data z databáze Oracle do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336435"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrace dat z Oracle do Azure Cosmos DB účtu rozhraní API Cassandra pomocí Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB se stal skvělou volbou pro podnikové úlohy spuštěné v Oracle z různých důvodů:

* **Lepší škálovatelnost a dostupnost:** Eliminuje jednotlivé body selhání, lepší škálovatelnost a dostupnost vašich aplikací.

* **Významné úspory nákladů:** Náklady můžete ušetřit pomocí Azure Cosmos DB, což zahrnuje náklady na virtuální počítač, šířku pásma a všechny příslušné licence Oracle. Navíc nemusíte spravovat datové centra, servery, úložiště SSD, sítě a náklady na elektřinu.

* **Žádná režie správy a monitorování:** Jako plně spravovaná cloudová služba Azure Cosmos DB odstraňuje režijní náklady na správu a monitorování nesčetných nastavení.

Existují různé způsoby, jak migrovat databázové úlohy z jedné platformy do jiné. [Blitzz](https://www.blitzz.io) je nástroj, který nabízí zabezpečený a spolehlivý způsob, jak provést migraci s nulovými výpadky z různých databází, aby se Azure Cosmos DB. Tento článek popisuje kroky potřebné k migraci dat z databáze Oracle do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Výhody použití Blitzz pro migraci

Řešení migrace Blitzz se řídí postupem, jak migrovat složitá provozní zatížení. Tady jsou některé klíčové aspekty plánu migrace v rámci nulového výpadku Blitzz:

* Nabízí automatickou migraci obchodní logiky (tabulky, indexy, zobrazení) z databáze Oracle do Azure Cosmos DB. Schémata nemusíte vytvářet ručně.

* Blitzz nabízí vysoce velký objem a paralelní replikaci databáze. Umožňuje, aby zdrojové i cílové platformy byly během migrace synchronizované pomocí techniky označované jako Change-Data-Capture (CDC). Pomocí CDC Blitzz průběžně vyžádá datový proud změn ze zdrojové databáze (Oracle) a použije ho pro cílovou databázi (Azure Cosmos DB).

* Je odolný proti chybám a zaručuje přesně po doručení dat, i když dojde v systému k selhání hardwaru nebo softwaru.

* Zabezpečuje data během přenosu s využitím nejrůznějších metod zabezpečení, jako je TLS/SSL, šifrování.

* Nabízí služby pro převod komplexní obchodní logiky napsané v PL/SQL na ekvivalentní obchodní logiku v Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Postup migrace dat

Tato část popisuje kroky potřebné k nastavení Blitzz a migraci dat z databáze Oracle do Azure Cosmos DB.

1. Z počítače, do kterého plánujete nainstalovat Blitzz replicant, přidejte certifikát zabezpečení. Tento certifikát vyžaduje Blitzz replicant k navázání připojení TLS se zadaným účtem Azure Cosmos DB. Certifikát můžete přidat pomocí následujících kroků:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. organizační jednotka může získat instalaci Blitzz a binární soubory buď tak, že si vyžádá ukázku na [webu Blitzz](https://www.blitzz.io). Alternativně můžete také odeslat [e-mail](mailto:success@blitzz.io) týmu.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Stažení nástroje Blitzz replicant Tool":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Soubory Blitzz replicant":::

1. V terminálu CLI nastavte konfiguraci zdrojové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/oracle.yml`** příkazu a přidejte čárkami oddělený seznam IP adres uzlů Oracle, číslo portu, uživatelské jméno, heslo a další požadované podrobnosti. Následující kód ukazuje příklad konfiguračního souboru:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Otevřít Editor připojení Oracle":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Konfigurace připojení Oracle":::

   Po vyplnění podrobností konfigurace soubor uložte a zavřete.

1. Volitelně můžete nastavit soubor filtru zdrojové databáze. Soubor filtru určuje, která schémata nebo tabulky se mají migrovat. Otevřete konfigurační soubor pomocí **`vi filter/oracle_filter.yml`** příkazu a zadejte následující konfigurační údaje:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Po vyplnění podrobností filtru databáze uložte a zavřete soubor.

1. V dalším kroku nastavíte konfiguraci cílové databáze. Před definováním konfigurace [vytvořte účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account). [Zvolte pravý klíč oddílu](partitioning-overview.md#choose-partitionkey) z vašich dat a pak vytvořte místo na disku a tabulku pro uložení migrovaných dat.

1. Před migrací dat zvyšte propustnost kontejneru na množství potřebné k rychlé migraci vaší aplikace. Můžete například zvýšit propustnost na 100000 ru. Škálování propustnosti před zahájením migrace vám pomůže s migrací dat za kratší dobu. 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Škálování Cosmos kontejneru Azure v celém":::

   Propustnost je nutné po dokončení migrace snížit. Na základě množství uložených dat a ru vyžadovaných pro jednotlivé operace můžete odhadnout propustnost vyžadovanou po migraci dat. Další informace o tom, jak odhadovat požadované ru, najdete v tématech [zřízení propustnosti pro kontejnery a databáze](set-throughput.md) a [odhad ru/s pomocí článků Azure Cosmos DB kapacity pro plánování](estimate-ru-with-capacity-planner.md) .

1. V podokně **připojovací řetězec** Získejte **kontaktní bod, port, uživatelské jméno** a **primární heslo** účtu Azure Cosmos. Tyto hodnoty použijete v konfiguračním souboru.

1. V terminálu CLI nastavte konfiguraci cílové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/cosmosdb.yml`** příkazu a přidejte čárkami oddělený seznam identifikátorů URI hostitele, číslo portu, uživatelské jméno, heslo a další požadované parametry. Následuje příklad obsahu v konfiguračním souboru:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Dále migrujte data pomocí Blitzz. Blizz replicant můžete spustit v režimu **úplného** nebo **snímku** :

   * **Úplný režim** – v tomto režimu se replicant i nadále spouští po migraci a naslouchá jakýmkoli změnám ve zdrojovém systému Oracle. Pokud se nějaké změny zjistí, replikují se v reálném čase na cílový účet Azure Cosmos.

   * **Režim snímků** – v tomto režimu můžete provést migraci schématu a jednorázovou replikaci dat. Replikace v reálném čase není touto možností podporována.


   Pomocí předchozích dvou režimů se dá migrace provést bez výpadků.

1. K migraci dat z terminálu Blitzz replicant CLI spusťte následující příkaz:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Uživatelské rozhraní replicant zobrazuje průběh replikace. Po dokončení operace migrace schématu a snímku se zobrazí průběh 100%. Po dokončení migrace můžete ověřit data v cílové databázi Azure Cosmos.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Výstup migrace dat Oracle":::

1. Vzhledem k tomu, že jste použili úplný režim migrace, můžete provádět operace, jako je například vložení, aktualizace nebo odstranění dat ve zdrojové databázi Oracle. Později můžete ověřit, jestli jsou replikované v reálném čase v cílové databázi Azure Cosmos. Po dokončení migrace nezapomeňte snížit propustnost nakonfigurovanou pro váš kontejner Azure Cosmos.

1. Replicant můžete zastavit v jakémkoli bodě a restartovat ho pomocí přepínače **--Resume** . Replikace se obnoví z bodu, který se zastavil, aniž by došlo k narušení konzistence dat. Následující příkaz ukazuje, jak použít přepínač Resume.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Další informace o migraci dat do cíle v reálném čase najdete v [ukázce Blitzz replicant](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy pro klíč oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků Azure Cosmos DB Capacity Planneru](estimate-ru-with-capacity-planner.md)
