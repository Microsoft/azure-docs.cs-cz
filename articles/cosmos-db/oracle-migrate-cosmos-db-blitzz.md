---
title: Migrace dat z oracle do rozhraní API Azure Cosmos DB Cassandra pomocí blitzzu
description: Zjistěte, jak migrovat data z databáze Oracle do rozhraní API Azure Cosmos DB Cassandra pomocí blitzzu.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: fe132ee6ab90fdae99463f11ecf46f352690b810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984352"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrace dat z Oracle do účtu API Azure Cosmos DB Cassandra pomocí blitzzu

Rozhraní Cassandra API v Azure Cosmos DB se stalo skvělou volbou pro podnikové úlohy, které běží na oracle z různých důvodů, jako jsou:

* **Lepší škálovatelnost a dostupnost:** Eliminuje jednotlivé body selhání, lepší škálovatelnost a dostupnost pro vaše aplikace.

* **Významné úspory nákladů:** Můžete ušetřit náklady s Azure Cosmos DB, který zahrnuje náklady na virtuální počítače, šířku pásma a všechny příslušné licence Oracle. Navíc nemusíte spravovat datová centra, servery, úložiště SSD, sítě a náklady na elektřinu.

* **Žádné režijní náklady na správu a monitorování:** Jako plně spravovaná cloudová služba Azure Cosmos DB odstraňuje režii správy a monitorování nesčetných nastavení.

Existují různé způsoby migrace databázových úloh z jedné platformy na druhou. [Blitzz](https://www.blitzz.io) je nástroj, který nabízí bezpečný a spolehlivý způsob, jak provádět nulovou migraci prostojů z různých databází do Azure Cosmos DB. Tento článek popisuje kroky potřebné k migraci dat z databáze Oracle do rozhraní API Azure Cosmos DB Cassandra pomocí blitzzu.

## <a name="benefits-using-blitzz-for-migration"></a>Výhody využívající Blitzz pro migraci

Řešení migrace blitzz ubírá krok za krokem k migraci složitých provozních úloh. Níže jsou uvedeny některé z klíčových aspektů blitzz je zero-prostoje migrační plán:

* Nabízí automatickou migraci obchodní logiky (tabulky, indexy, zobrazení) z databáze Oracle do Azure Cosmos DB. Schémata není nutné vytvářet ručně.

* Blitzz nabízí velkoobjemovou a paralelní replikaci databáze. Umožňuje zdrojové i cílové platformy, které mají být synchronizovány během migrace pomocí techniky s názvem Change-Data-Capture (CDC). Pomocí CDC Blitzz průběžně stahuje proud změn ze zdrojové databáze (Oracle) a použije ji na cílovou databázi (Azure Cosmos DB).

* Je odolný proti chybám a zaručuje přesně jednou dodání dat i při selhání hardwaru nebo softwaru v systému.

* Zabezpečuje data během přenosu pomocí různých metodik zabezpečení, jako je SSL, šifrování.

* Nabízí služby pro převod komplexní obchodní logiky napsané v PL/SQL na ekvivalentní obchodní logiku v Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Postup migrace dat

Tato část popisuje kroky potřebné k nastavení Blitzz a migruje data z databáze Oracle do Azure Cosmos DB.

1. Z počítače, kde plánujete nainstalovat replikant Blitzz, přidejte bezpečnostní certifikát. Tento certifikát je vyžadován replikantblitzz navázat připojení SSL se zadaným účtem Azure Cosmos DB. Certifikát můžete přidat pomocí následujících kroků:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou může dostat Blitzz instalaci a binární soubory buď tím, že žádá demo na [webových stránkách Blitzz](https://www.blitzz.io). Případně můžete také poslat [e-mail](mailto:success@blitzz.io) týmu.

   ![Blitzz replikant nástroj ke stažení](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replikantsoubory](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. Z terminálu CLI nastavte konfiguraci zdrojové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/oracle.yml`** příkazu a přidejte seznam IP adres oracle uzlů, čísla portu, uživatelského jména, hesla a dalších požadovaných podrobností. Následující kód ukazuje ukázkový konfigurační soubor:

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

   ![Otevřít editor připojení Oracle](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Konfigurace připojení Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Po vyplnění podrobností o konfiguraci soubor uložte a zavřete.

1. Volitelně můžete nastavit soubor filtru zdrojové databáze. Soubor filtru určuje, která schémata nebo tabulky mají být migrovány. Otevřete konfigurační soubor pomocí **`vi filter/oracle_filter.yml`** příkazu a zadejte následující podrobnosti konfigurace:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Po vyplnění podrobností filtru databáze uložte a zavřete soubor.

1. Dále nastavíte konfiguraci cílové databáze. Před definováním konfigurace [vytvořte účet rozhraní API Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account). [Zvolte správný klíč oddílu](partitioning-overview.md#choose-partitionkey) z dat a vytvořte Keyspace a tabulku pro uložení migrovaných dat.

1. Před migrací dat zvyšte propustnost kontejneru na částku potřebnou k rychlé migraci aplikace. Můžete například zvýšit propustnost na 100000 ru. Změna velikosti propustnost před zahájením migrace vám pomůže migrovat data v kratším čase. 

   ![Škálování kontejneru Azure Cosmos v celém](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   Po dokončení migrace je nutné snížit propustnost. Na základě množství dat uložených a ru požadovaných pro každou operaci můžete odhadnout propustnost požadovanou po migraci dat. Další informace o tom, jak odhadnout požadované ru, najdete v článku [Zřízení propustnosti pro kontejnery a databáze](set-throughput.md) a [odhadru/s pomocí článků plánovač kapacity Azure Cosmos DB.](estimate-ru-with-capacity-planner.md)

1. Získejte **kontaktní místo, port, uživatelské jméno**a primární **heslo** účtu Azure Cosmos z podokna **Připojovací řetězec.** Tyto hodnoty použijete v konfiguračním souboru.

1. Z terminálu CLI nastavte konfiguraci cílové databáze. Otevřete konfigurační soubor pomocí **`vi conf/conn/cosmosdb.yml`** příkazu a přidejte seznam identifikátorů URI, čísla portu, uživatelského jména, hesla a dalších požadovaných parametrů oddělených čárkami. Následuje příklad obsahu v konfiguračním souboru:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Další migrovat data pomocí Blitzz. Replikant Blizz můžete spustit v **režimu úplného** **nebo snímku:**

   * **Úplný režim** – V tomto režimu replicant pokračuje v běhu po migraci a naslouchá všem změnám ve zdrojovém systému Oracle. Pokud zjistí všechny změny, jsou replikovány na cílovém účtu Azure Cosmos v reálném čase.

   * **Režim snímek** – v tomto režimu můžete provést migraci schématu a jednorázovou replikaci dat. Tato možnost není podporována replikace v reálném čase.


   Pomocí výše uvedených dvou režimů lze migraci provést s nulovými prostoji.

1. Chcete-li migrovat data, spusťte z terminálu blitzzského replikantského příkazu CLI následující příkaz:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Replikační ui zobrazuje průběh replikace. Po dokončení migrace schématu a operace snímku se zobrazí průběh 100 %. Po dokončení migrace můžete ověřit data v cílové databázi Azure Cosmos.

   ![Výstup migrace dat Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Vzhledem k tomu, že jste pro migraci použili úplný režim, můžete provádět operace, jako je vkládání, aktualizace nebo odstraňování dat ve zdrojové databázi Oracle. Později můžete ověřit, že jsou replikovány v reálném čase v cílové databázi Azure Cosmos. Po migraci nezapomeňte snížit propustnost nakonfigurovanou pro váš kontejner Azure Cosmos.

1. Replikant můžete zastavit libovolný bod a restartovat jej pomocí **přepínače --resume.** Replikace pokračuje od bodu, který byl zastaven bez ohrožení konzistence dat. Následující příkaz ukazuje, jak používat přepínač životopisu.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Další informace o migraci dat do cíle v reálném čase najdete v [tématu Ukázka replikantu Blitzz](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy klíče oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků plánovačkapacity Azure Cosmos DB](estimate-ru-with-capacity-planner.md)