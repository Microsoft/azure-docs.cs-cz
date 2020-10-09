---
title: Jak používat replikaci Apache Hive v clusterech Azure HDInsight
description: Naučte se používat replikaci podregistru v clusterech HDInsight k replikaci metastore Hive a Azure Data Lake Storage Gen 2 pro data Lake.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: af74392b3368a25e5d238f774292c80de5f91c65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91857737"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Jak používat replikaci Apache Hive v clusterech Azure HDInsight

V souvislosti s databázemi a sklady je replikace proces duplikace entit z jednoho datového skladu do jiného. Duplikace se může vztahovat na celou databázi nebo na menší úroveň, jako je například tabulka nebo oddíl. Cílem je mít repliku, která se mění pokaždé, když se změní základní entita. Replikace na Apache Hive se zaměřuje na zotavení po havárii a nabízí jednosměrnou replikaci primárního kopírování. V clusterech HDInsight se dá replikace podregistru použít k jednosměrné replikaci metastore Hive a přidruženého základního data Lake na Azure Data Lake Storage Gen2.  

Replikace podregistru se vyvinula v letech s novějšími verzemi, které poskytují lepší funkčnost a jsou rychlejší a méně náročná na prostředky. V tomto článku probereme replikaci podregistru (Replv2), která je podporovaná v typech clusterů HDInsight 3,6 a HDInsight 4,0.

## <a name="advantages-of-replv2"></a>Výhody Replv2

[ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) v podregistru nebo (Replv2) má následující výhody oproti první verzi replikace podregistru, která použila [Import a export](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)podregistru:

- Přírůstková replikace založená na událostech
- Replikace k bodu v čase  
- Menší požadavky na šířku pásma  
- Snížení počtu mezilehlých kopií  
- Stav replikace je zachován.
- Omezená replikace  
- Podpora modelu hvězdicového a paprskového modelu  
- Podpora tabulek pro KYSELINu (ve službě HDInsight 4,0)

## <a name="replication-phases"></a>Replikační fáze

Replikace na základě událostí podregistru je nakonfigurovaná mezi primárním a sekundárním clusterem. Tato replikace se skládá ze dvou různých fází: zavedení a přírůstkové spuštění.

### <a name="bootstrapping"></a>Zavádění

Spouštění je určené ke spuštění jednou pro replikaci základního stavu databází z primární na sekundární. V případě potřeby můžete nakonfigurovat spouštění, aby zahrnovala podmnožinu tabulek v cílové databázi, ve které je potřeba povolit replikaci.

### <a name="incremental-runs"></a>Přírůstkové běhy

Po spuštění jsou přírůstkové běhy na primárním clusteru automatizované a události generované během těchto přírůstkových běhů se přehrávají v sekundárním clusteru. Když se sekundární cluster zachytí s primárním clusterem, sekundární objekt se bude shodovat s událostmi primárního.

## <a name="replication-commands"></a>Příkazy replikace

Podregistr nabízí sadu příkazů REPL – `DUMP` , `LOAD` a `STATUS` – k orchestraci toku událostí. `DUMP`Příkaz vygeneruje místní protokol všech událostí DDL/DML v primárním clusteru. `LOAD`Příkaz je přístup k laxně vytvářená kopírování metadat a dat protokolovaných do extrahovaných výstupů výpisu replikace a je spuštěn v cílovém clusteru. `STATUS`Příkaz se spustí z cílového clusteru, aby poskytoval nejnovější ID události, které bylo úspěšně replikováno z posledního replikačního zatížení.

### <a name="set-replication-source"></a>Nastavit zdroj replikace

Než začnete s replikací, ujistěte se, že databáze, která má být replikovaná, je nastavená jako zdroj replikace. Pomocí `DESC DATABASE EXTENDED <db_name>` příkazu můžete zjistit, jestli `repl.source.for` je parametr nastavený s názvem zásady.

Pokud je naplánovaná zásada a `repl.source.for` parametr není nastavený, musíte nejdřív nastavit tento parametr pomocí `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Vypsat metadata pro data Lake

`REPL DUMP [database name]. => location / event_id`Příkaz se používá ve fázi Bootstrap k výpisu relevantních metadat do Azure Data Lake Storage Gen2. `event_id`Určuje minimální událost, do které jsou příslušná metadata vložena Azure Data Lake Storage Gen2. 
 
```sql
repl dump tpcds_orc; 
```
Příklad výstupu:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Načtení dat do cílového clusteru

`REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }`Příkaz se používá k načtení dat do cílového clusteru pro Bootstrap i přírůstkové fáze replikace. `[database name]`Může být stejný jako zdroj nebo jiný název v cílovém clusteru. `[location]`Představuje umístění z výstupu dřívějšího `REPL DUMP` příkazu. To znamená, že cílový cluster by měl být schopný komunikovat se zdrojovým clusterem. `WITH`Klauzule byla primárně přidána, aby zabránila restartování cílového clusteru, což umožňuje replikaci.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Výstup ID poslední replikované události

`REPL STATUS [database name]`Příkaz se spustí v cílových clusterech a vytvoří výstup poslední repliky `event_id` . Příkaz také umožňuje uživatelům zjistit, do jakého stavu je cílový cluster replikován. K vytvoření dalšího `REPL DUMP` příkazu pro přírůstkovou replikaci můžete použít výstup tohoto příkazu.

```sql
repl status tpcds_orc;
```

Příklad výstupu:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Vypsat relevantní data a metadata do data Lake

`REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }`Příkaz slouží k výpisu relevantních metadat a dat do Azure Data Lake Storage. Tento příkaz se používá v přírůstkové fázi a je spuštěný ve zdrojovém skladu. `FROM [event-id]`Je vyžadován pro přírůstkovou fázi a hodnotu `event-id` lze odvodit spuštěním `REPL STATUS [database name]` příkazu v cílovém skladu.

```sql
repl dump tpcds_orc from 2925;
```

Příklad výstupu:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Proces replikace podregistru

Následující kroky jsou sekvenční události, které se provádějí během procesu replikace podregistru.

1. Zajistěte, aby se tabulky, které se replikují, nastavily jako zdroj replikace pro určité zásady.

1. `REPL_DUMP`Příkaz se vydá pro primární cluster s přidruženými omezeními, jako je název databáze, rozsah ID události a Azure Data Lake Storage Gen2 adresa URL úložiště.

1. Systém zaserializace výpis všech sledovaných událostí z metastore na nejnovější. Tento výpis je uložený v Azure Data Lake Storage Gen2 účet úložiště v primárním clusteru na adrese URL, která je určená v `REPL_DUMP` .  

1. Primární cluster uchovává metadata replikace do úložiště Azure Data Lake Storage Gen2 primárního clusteru. Cestu lze konfigurovat v uživatelském rozhraní konfigurace podregistru v Ambari. Proces poskytuje cestu, kde jsou uložena metadata, a ID poslední sledované události DML/DDL.

1. `REPL_LOAD`Příkaz je vydán ze sekundárního clusteru. Příkaz odkazuje na cestu nakonfigurovanou v kroku 3.

1. Sekundární cluster načte soubor metadat se sledovanými událostmi, které byly vytvořeny v kroku 3. Ujistěte se, že sekundární cluster má síťové připojení k Azure Data Lake Storage Gen2 úložišti primárního clusteru, ve kterém `REPL_DUMP` jsou uložené sledované události.  

1. Sekundární cluster vytvoří výpočetní prostředky distribuované kopie ( `DistCP` ).

1. Sekundární cluster kopíruje data z úložiště primárního clusteru.  

1. Metastore v sekundárním clusteru se aktualizuje.  

1. ID poslední sledované události je uloženo v primárním metastore.

Přírůstková replikace se řídí stejným procesem a vyžaduje jako vstup ID poslední replikované události. To vede k přírůstkové kopii od poslední události replikace. Přírůstkové replikace jsou normálně automatizované s předem stanovenou frekvencí, aby bylo dosaženo požadovaných cílů bodu obnovení (RPO).

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Diagram replikace podregistru":::

## <a name="replication-patterns"></a>Vzorce replikace  

Replikace se obvykle konfiguruje jednosměrovým způsobem mezi primárním a sekundárním, přičemž primární rozhraní pro čtení a zápis požadavků. Sekundární clustery pro stravování mají pouze požadavky na čtení. Zápisy jsou povoleny na sekundárním počítači, pokud dojde k havárii, ale zpětná replikace musí být nakonfigurována zpět na primární.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Diagram replikace podregistru":::

Replikace podregistru je vhodná pro celou řadu schémat, včetně primárního – sekundárního, centrálního a paprskového a přenosového.

V rámci služby HDInsight Active Primary – pohotovostní sekundární síť je běžným vzorem pro provozní kontinuitu a zotavení po havárii (BCDR) a HiveReplicationV2 může tento model použít s oblastními oddělenými clustery systému Hadoop ve službě HDInsight s partnerským vztahem. Pro hostování replikačních skriptů můžete použít společný virtuální počítač partnerský pro oba clustery. Další informace o možných vzorech BCDR ve službě HDInsight najdete v [dokumentaci k provozní kontinuitě HDInsight](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Replikace podregistru s Balíček zabezpečení podniku  

V případech, kdy je replikace podregistru plánována v clusterech HDInsight Hadoop pomocí Balíček zabezpečení podniku, je nutné faktorovat v mechanismech replikace pro Ranger metastore a Azure Active Directory Domain Services (služba AD DS).  

Použijte funkci sady replik služby Azure služba AD DS k vytvoření více než jedné sady replik Azure služba AD DS na jeden tenant služby Azure AD napříč několika oblastmi. Jednotlivé sady replik musí mít v příslušných oblastech partnerský vztah s HDInsight virtuální sítě. V této konfiguraci se změny v Azure služba AD DS, včetně konfigurace, identity uživatelů a přihlašovacích údajů, skupin, objektů zásad skupiny, počítačových objektů a dalších změn, aplikují na všechny sady replik ve spravované doméně pomocí replikace Azure služba AD DS.
  
Zásady Ranger se dají pravidelně zálohovat a replikovat z primární na sekundární pomocí funkcí Ranger Import-Export. V závislosti na úrovni autorizací, které chcete implementovat v sekundárním clusteru, můžete zvolit replikaci všech nebo podmnožin zásad Ranger.  

## <a name="sample-code"></a>Ukázka kódu  

Následující sekvence kódu poskytuje příklad, jak lze implementovat a přírůstkovou replikaci pro ukázkovou tabulku s názvem `tpcds_orc` .  

1. Nastavte tabulku jako zdroj pro zásady replikace.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Spouštěcí výpis v primárním clusteru.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Příklad výstupu:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Zavedení zátěže v sekundárním clusteru. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Ověřte `REPL` stav v sekundárním clusteru.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Přírůstkový výpis v primárním clusteru.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Příklad výstupu:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Přírůstkové zatížení v sekundárním clusteru.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Zkontroluje `REPL` stav v sekundárním clusteru.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Další kroky

Další informace o položkách, které jsou popsány v tomto článku, najdete v těchto tématech:

- [Provozní kontinuita Azure HDInsight](../hdinsight-business-continuity.md)
- [Architektury Azure HDInsight pro provozní kontinuitu](../hdinsight-business-continuity-architecture.md)
- [Případová studie architektury řešení Azure HDInsight s vysokou dostupností](../hdinsight-high-availability-case-study.md)
- [Co je Apache Hive a HiveQL ve službě Azure HDInsight?](../hadoop/hdinsight-use-hive.md)