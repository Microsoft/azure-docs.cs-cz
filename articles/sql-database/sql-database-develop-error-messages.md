---
title: Kódy chyb SQL – chyba připojení k databázi | Microsoft Docs
description: 'Přečtěte si o kódech chyb SQL pro klientské aplikace SQL Database, jako jsou třeba běžné chyby připojení k databázi, problémy s kopírováním databáze a obecné chyby. '
keywords: kód chyby SQL, přístup k SQL, Chyba připojení k databázi, kódy chyb SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 19febc5a0a6e4a72cfebfaecd917185538130152
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035033"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Kódy chyb SQL pro klientské aplikace SQL Database: Chyby připojení k databázi a další problémy

V tomto článku jsou uvedeny kódy chyb SQL pro klientské aplikace SQL Database, včetně chyb připojení k databázi, přechodné chyby (označované také jako přechodné chyby), chyby zásad správného řízení prostředků, problémy s kopírováním databáze, elastický fond a další chyby. Většina kategorií je obzvláště Azure SQL Database a neplatí pro Microsoft SQL Server. Viz také [chybové zprávy systému](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Chyby připojení k databázi, přechodné chyby a další dočasné chyby

Následující tabulka obsahuje kódy chyb SQL pro chyby ztráty připojení a další přechodné chyby, se kterými se můžete setkat, když se aplikace pokusí o přístup k SQL Database. Výukové kurzy Začínáme s připojením k Azure SQL Database najdete v tématu [připojení k Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Nejčastější chyby připojení k databázi a chyby přechodného selhání

Infrastruktura Azure má možnost dynamicky překonfigurovat servery, pokud se v SQL Database službě vyskytnou těžká zatížení.  Toto dynamické chování může způsobit, že váš klientský program ztratí připojení k SQL Database. Tento druh chybové podmínky se nazývá *přechodná chyba*.

Důrazně doporučujeme, aby váš klientský program měl logiku opakování, aby mohl znovu navázat připojení, a to tak, že by došlo k přechodné chybě při jejich opravě.  Před prvním opakováním doporučujeme, abyste prodlevu zaznamenali na 5 sekund. Opakuje se pokus po zpoždění kratším než 5 sekund při zahlcení cloudové služby. U každého následného opakování by se mělo zpozdit exponenciálně, maximálně 60 sekund.

Chyby přechodných chyb obvykle naplňují manifest jako jednu z následujících chybových zpráv z klientských programů:

* Databáze @no__t – 0db_name @ no__t-1 na serveru &lt;Azure_instance @ no__t-3 není aktuálně k dispozici. Zkuste prosím připojení znovu později. Pokud potíže potrvají, obraťte se na zákaznickou podporu a poskytněte jim ID trasování relace &lt;session_id @ no__t-1.
* Databáze @no__t – 0db_name @ no__t-1 na serveru &lt;Azure_instance @ no__t-3 není aktuálně k dispozici. Zkuste prosím připojení znovu později. Pokud potíže potrvají, obraťte se na zákaznickou podporu a poskytněte jim ID trasování relace &lt;session_id @ no__t-1. (Microsoft SQL Server, chyba: 40613)
* Existující připojení bylo vynuceně ukončeno vzdáleným hostitelem.
* System. data. entity. Core. EntityCommandExecutionException: při provádění definice příkazu došlo k chybě. Podrobnosti naleznete v informacích o vnitřní výjimce. ---> System. data. SqlClient. SqlException: při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu. (poskytovatel: Zprostředkovatel relace, chyba: 19 – fyzické připojení není použitelné)
* Pokus o připojení k sekundární databázi se nezdařil, protože databáze probíhá rekonfigurace a je zaneprázdněna při použití nových stránek v průběhu aktivní transakce v primární databázi. 

Příklady kódu logiky opakování naleznete zde:

* [Knihovny připojení pro SQL Database a SQL Server](sql-database-libraries.md) 
* [Akce pro opravu chyb připojení a přechodných chyb v SQL Database](sql-database-connectivity-issues.md)

Diskuze o *době blokování* klientů, kteří používají ADO.NET, je k dispozici v části [připojení k fondu SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Kódy chyb přechodné chyby

Následující chyby jsou přechodné a měly by se opakovat v aplikační logice: 

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 4060 |16bitovém |Databázi% nelze otevřít. &#x2a;LS vyžadovaná přihlášením Přihlášení se nezdařilo. Další informace najdete v tématu [chyby 4000 až 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) .|
| 40197 |sedmnáct |Služba zjistila chybu při zpracování vaší žádosti. Zkuste to prosím znovu. Kód chyby:% d.<br/><br/>Tato chyba se zobrazí, když dojde k výpadku služby kvůli softwarovým nebo hardwarovým upgradům, selháním hardwaru nebo jakýmkoli jiným problémům s podporou převzetí služeb při selhání. Kód chyby (% d) [vložený v rámci zprávy chyby 40197](sql-database-develop-error-messages.md#embedded-error-codes) poskytuje další informace o druhu selhání nebo převzetí služeb při selhání. Některé příklady kódů chyb jsou vložené v rámci zprávy chyby 40197 jsou 40020, 40143, 40166 a 40540.<br/><br/>Po opětovném připojení k vašemu SQL Database serveru se vás automaticky připojí k kopii vaší databáze v pořádku. Vaše aplikace musí zachytit chybu 40197, zaprotokolovat vložený kód chyby (% d) ve zprávě pro řešení potíží a zkusit se znovu připojit k SQL Database, dokud nebudou k dispozici prostředky a připojení se znovu naváže. Další informace najdete v tématu [přechodné chyby](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20o |Služba je momentálně zaneprázdněna. Požadavek opakujte po 10 sekundách. ID incidentu:% ls. Kód:% d. Další informace naleznete v tématu: <br/>[omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>@no__t[omezení založené na DTU](sql-database-service-tiers-dtu.md) -0 &nbsp; pro izolované databáze<br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;.|
| 40613 |sedmnáct |Databáze%. &#x2a;ls na serveru%. &#x2a;ls není aktuálně k dispozici. Zkuste prosím připojení znovu později. Pokud se problém opakuje, obraťte se na zákaznickou podporu a sdělte mu ID trasování relace%. &#x2a;ls.<br/><br/> K této chybě může dojít, pokud již existuje existující vyhrazené připojení správce (DAC) pro databázi. Další informace najdete v tématu [přechodné chyby](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16bitovém |Požadavek nelze zpracovat. Pro zpracování požadavku není dostatek prostředků.<br/><br/>Služba je momentálně zaneprázdněna. Opakujte prosím požadavek později. Další informace naleznete v tématu: <br/>[omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>@no__t[omezení založené na DTU](sql-database-service-tiers-dtu.md) -0 &nbsp; pro izolované databáze<br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 49919 |16bitovém |Nelze zpracovat žádost o vytvoření nebo aktualizaci. Pro předplatné% ld probíhá příliš mnoho probíhajících operací vytvoření nebo aktualizace.<br/><br/>Služba je zaneprázdněná zpracováním více žádostí o vytvoření nebo aktualizaci vašeho předplatného nebo serveru. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [Sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro operace, které čekají na zpracování. Počkejte, dokud nebudou dokončeny žádosti o vytvoření nebo aktualizaci, nebo odstraňte jednu z vašich čekajících žádostí a opakujte požadavek později. Další informace naleznete v tématu: <br/>[omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>@no__t[omezení založené na DTU](sql-database-service-tiers-dtu.md) -0 &nbsp; pro izolované databáze<br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 49920 |16bitovém |Požadavek nelze zpracovat. U předplatného% ld probíhá příliš mnoho operací.<br/><br/>Služba je zaneprázdněná zpracováním více požadavků pro toto předplatné. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [Sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro stav operace Počkejte na dokončení čekajících žádostí nebo odstraňte jednu z vašich čekajících žádostí a opakujte požadavek později. Další informace naleznete v tématu: <br/>[omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>@no__t[omezení založené na DTU](sql-database-service-tiers-dtu.md) -0 &nbsp; pro izolované databáze<br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 4221 |16bitovém |Přihlášení k sekundárnímu pro čtení se nezdařilo z důvodu dlouhého čekání na ' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING '. Replika není k dispozici pro přihlášení, protože pro transakce, které byly v letadle při recyklování repliky, chybí verze řádků. Problém se dá vyřešit vrácením zpět nebo potvrzením aktivních transakcí na primární replice. Výskyty tohoto stavu lze minimalizovat tím, že se vyhnete dlouhým transakcím zápisu na primárním. |

## <a name="embedded-error-codes"></a>Vložené kódy chyb

V obecnější kód chyby 40197 jsou vloženy následující chyby:

```
The service has encountered an error processing your request. Please try again. Error code %d.
```

| Kód chyby | Závažnost | Popis | 
| ---:| ---:|:---|
|  1104 |sedmnáct |V databázi TEMPDB došlo během nalití mimo prostor. Uvolněte místo vyřazením objektů nebo přepsáním dotazu pro využití méně řádků. Pokud problém stále přetrvává, zvažte upgrade na vyšší cíl na úrovni služby.|
| 40020 |16bitovém |Databáze je v přechodu a operace se ukončuje.|
| 40143 |16bitovém |Replika, kterou hostuje datový uzel pro požadovaný oddíl, není primární.|
| 40166 |16bitovém |Probíhá rekonfigurace databáze clouddb a všechny nové uživatelské transakce budou přerušeny.|
| 40540 |16bitovém |Transakce byla přerušena, protože databáze byla přesunuta do režimu jen pro čtení. Jedná se o dočasnou situaci a zkuste operaci zopakovat.|

Podrobnosti o dalších vložených chybách najdete na dotazech `sys.messages`:

```sql
SELECT * FROM sys.[messages] WHERE [message_id] = <error_code>
```

## <a name="database-copy-errors"></a>Chyby kopírování databáze

Při kopírování databáze v Azure SQL Database může dojít k následujícím chybám. Další informace najdete v tématu [kopírování Azure SQL Database](sql-database-copy.md).

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 40635 |16bitovém |Klient s IP adresou%. &#x2a;LS je dočasně zakázané. |
| 40637 |16bitovém |Vytvoření kopie databáze je aktuálně zakázané. |
| 40561 |16bitovém |Kopírování databáze se nezdařilo. Buď zdrojová, nebo cílová databáze neexistuje. |
| 40562 |16bitovém |Kopírování databáze se nezdařilo. Zdrojová databáze byla vyřazena. |
| 40563 |16bitovém |Kopírování databáze se nezdařilo. Cílová databáze byla vyřazena. |
| 40564 |16bitovém |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40565 |16bitovém |Kopírování databáze se nezdařilo. Nepovoluje se více než jedna souběžná kopie databáze ze stejného zdroje. Vyřaďte prosím cílovou databázi a zkuste to znovu později. |
| 40566 |16bitovém |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40567 |16bitovém |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40568 |16bitovém |Kopírování databáze se nezdařilo. Zdrojová databáze se stala nedostupnou. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40569 |16bitovém |Kopírování databáze se nezdařilo. Cílová databáze již není k dispozici. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40570 |16bitovém |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu později. |
| 40571 |16bitovém |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu později. |

## <a name="resource-governance-errors"></a>Chyby zásad správného řízení prostředků

Následující chyby jsou způsobeny nadměrným využitím prostředků při práci s Azure SQL Database. Příklad:

* Transakce byla otevřena příliš dlouho.
* Transakce drží příliš mnoho zámků.
* Aplikace spotřebovává příliš mnoho paměti.
* Aplikace spotřebovává příliš mnoho `TempDb` místa.

Související témata:

* Další informace naleznete v tématu:
  * [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)
  * [Omezení založené na DTU pro izolované databáze](sql-database-service-tiers-dtu.md)
  * [Omezení pro elastické fondy založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)
  * [limity založené na vCore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)
  * [omezení pro elastické fondy založené na vCore](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md). 

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 10928 |20o |ID prostředku:% d. Limit% s pro databázi je% d a byl dosažen. Další informace najdete v tématu [SQL Database omezení prostředků pro databáze s jednou a ve fondu](sql-database-resource-limits-database-server.md).<br/><br/>ID prostředku indikuje prostředek, který dosáhl limitu. Pro pracovní vlákna, ID prostředku = 1. Pro relace, ID prostředku = 2.<br/><br/>Další informace o této chybě a o tom, jak ji vyřešit, najdete v těchto tématech: <br/>[omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>@no__t[omezení založené na DTU](sql-database-service-tiers-dtu.md) -0 &nbsp; pro izolované databáze<br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;. |
| 10929 |20o |ID prostředku:% d. Minimální záruka% s je% d, maximální limit je% d a aktuální využití databáze je% d. Server je však v současnosti příliš zaneprázdněn, aby podporoval žádosti větší než% d pro tuto databázi. ID prostředku indikuje prostředek, který dosáhl limitu. Pro pracovní vlákna, ID prostředku = 1. Pro relace, ID prostředku = 2. Další informace naleznete v tématu: <br/>[omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>@no__t[omezení založené na DTU](sql-database-service-tiers-dtu.md) -0 &nbsp; pro izolované databáze<br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;. <br/>V opačném případě zkuste to prosím znovu později. |
| 40544 |20o |Databáze dosáhla kvóty velikosti. Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení. Informace o škálování databáze najdete v tématu [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).|
| 40549 |16bitovém |Relace je ukončena, protože máte dlouhotrvající transakci. Zkuste zkrátit transakci. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16bitovém |Relace byla ukončena, protože získala příliš mnoho zámků. Zkuste číst nebo upravit méně řádků v jedné transakci. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16bitovém |Relace byla ukončena z důvodu nadměrného využití @no__t 0. Zkuste upravit dotaz, aby se snížilo využití místa na dočasné tabulce.<br/><br/>Pokud používáte dočasné objekty, můžete ušetřit místo v databázi `TEMPDB` tak, že vyřadíte dočasné objekty, které už relace nepotřebují. Další informace o využití databáze tempdb v SQL Database najdete v tématu [databáze tempdb v SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16bitovém |Relace byla ukončena z důvodu nadměrného využití místa v protokolu transakcí. Zkuste upravit menší počet řádků v jedné transakci. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](sql-database-use-batching-to-improve-performance.md).<br/><br/>Provádíte-li hromadné vložení pomocí nástroje `bcp.exe` nebo třídy `System.Data.SqlClient.SqlBulkCopy`, zkuste použít možnosti `-b batchsize` nebo `BatchSize` k omezení počtu řádků zkopírovaných na server v každé transakci. Pokud znovu sestavíte index s příkazem `ALTER INDEX`, zkuste použít možnost `REBUILD WITH ONLINE = ON`. Informace o velikostech protokolů transakcí pro model nákupu vCore naleznete v tématu: <br/>omezení Vcore-0 &nbsp;[pro izolované databáze na základě limitu](sql-database-vcore-resource-limits-single-databases.md) @no__t<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp; na Vcore<br/>@no__t[omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md)&nbsp;.|
| 40553 |16bitovém |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz pro zpracování méně řádků.<br/><br/>Snížení počtu `ORDER BY` a `GROUP BY` operací v kódu Transact-SQL snižuje požadavky na paměť v dotazu. Informace o škálování databáze najdete v tématu [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Chyby elastického fondu

Následující chyby souvisejí s vytvářením a používáním elastických fondů:

| Kód chyby | Závažnost | Popis | Nápravná opatření |
|:--- |:--- |:--- |:--- |
| 1132 | sedmnáct |Elastický fond dosáhl svého limitu úložiště. Využití úložiště pro elastický fond nemůže přesáhnout (% d) MB. Došlo k pokusu o zápis dat do databáze, když bylo dosaženo limitu úložiště elastického fondu. Informace o omezeních prostředků najdete v těchto tématech: <br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md)&bull; &nbsp; na Vcore <br/> |Pokud je to možné, zvažte zvýšení DTU a/nebo Přidání úložiště do elastického fondu, pokud je to možné, abyste zvýšili jeho limit úložiště, omezili jste úložiště používané jednotlivými databázemi v elastickém fondu nebo z elastického fondu odebíráte databáze. Škálování elastického fondu najdete v tématu [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).|
| 10929 | 16bitovém |Minimální záruka% s je% d, maximální limit je% d a aktuální využití databáze je% d. Server je však v současnosti příliš zaneprázdněn, aby podporoval žádosti větší než% d pro tuto databázi. Informace o omezeních prostředků najdete v těchto tématech: <br/>[omezení pro elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp; na DTU<br/>[omezení pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md)&bull; &nbsp; na Vcore <br/> V opačném případě zkuste to prosím znovu později. Minimální počet jednotek DTU/vCore na databázi; DTU/vCore Max na databázi. Celkový počet souběžných pracovních procesů (požadavků) napříč všemi databázemi v elastickém fondu se pokusil překročit limit fondu. |Pokud je to možné, můžete zvýšit DTU nebo virtuální jádra elastického fondu, pokud je to možné, aby se zvýšil jeho limit pracovního procesu, nebo odebrat databáze z elastického fondu. |
| 40844 | 16bitovém |Databáze% ls na serveru% LS je databáze edice% LS v elastickém fondu a nemůže mít relaci průběžného kopírování.  |Není k dispozici |
| 40857 | 16bitovém |Nenašel se elastický fond pro server:% ls, název elastického fondu:% ls. Zadaný elastický fond na zadaném serveru neexistuje. | Zadejte platný název elastického fondu. |
| 40858 | 16bitovém |Elastický fond% LS už na serveru:% LS existuje. Zadaný elastický fond již na zadaném serveru SQL Database existuje. | Zadejte nový název elastického fondu. |
| 40859 | 16bitovém |Elastický fond nepodporuje úroveň služby% ls. Zadaná úroveň služby není podporovaná pro zřizování elastického fondu. |Zadejte správnou edici nebo ponechte prázdnou vrstvu služby, aby používala výchozí úroveň služby. |
| 40860 | 16bitovém |Kombinace elastického fondu% LS a cíle služby% ls není platná. Elastický fond a úroveň služby lze zadat společně pouze v případě, že je typ prostředku zadán jako ' ElasticPool '. |Zadejte správnou kombinaci elastického fondu a úrovně služeb. |
| 40861 | 16bitovém |Edice databáze%. *ls se nesmí lišit od úrovně služby elastického fondu, která je%.* ls. Edice databáze se liší od úrovně služby elastického fondu. |Nezadávejte edici databáze, která se liší od úrovně služby elastického fondu.  Všimněte si, že není nutné zadávat edici databáze. |
| 40862 | 16bitovém |Pokud je zadaný cíl služby elastického fondu, musí být zadaný název elastického fondu. Cíl služby elastického fondu neidentifikuje jednoznačně elastický fond. |Pokud používáte cíl služby elastického fondu, zadejte název elastického fondu. |
| 40864 | 16bitovém |DTU pro elastický fond musí být minimálně (% d) DTU pro úroveň služby%. * ls. Probíhá pokus o nastavení DTU pro elastický fond pod minimálním limitem. |Opakujte nastavení DTU pro elastický fond alespoň na minimální limit. |
| 40865 | 16bitovém |DTU pro elastický fond nemůže přesáhnout (% d) DTU pro úroveň služby%. * ls. Došlo k pokusu o nastavení DTU pro elastický fond nad rámec maximálního limitu. |Opakujte nastavení DTU pro elastický fond tak, aby nepřesahoval maximální limit. |
| 40867 | 16bitovém |Maximální počet jednotek DTU na databázi musí být minimálně (% d) pro úroveň služby%. * ls. Byl proveden pokus o nastavení maximálního počtu jednotek DTU na databázi pod podporovaným limitem. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40868 | 16bitovém |Maximální počet jednotek DTU na databázi nemůže být větší než (% d) pro úroveň služby%. * ls. Byl proveden pokus o nastavení maximálního počtu jednotek DTU na databázi nad rámec podporovaného limitu. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40870 | 16bitovém |Minimální počet jednotek DTU na databázi nemůže být větší než (% d) pro úroveň služby%. * ls. Došlo k pokusu o nastavení minimální hodnoty DTU na databázi nad rámec podporovaného limitu. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40873 | 16bitovém |Počet databází (% d) a minimální počet jednotek DTU na databázi (% d) nesmí překročit DTU elastického fondu (% d). Probíhá pokus o určení minimálního počtu DTU pro databáze v elastickém fondu, které přesahují DTU elastického fondu. | Zvažte zvýšení DTU elastického fondu nebo snižte minimální počet jednotek DTU na databázi nebo snižte počet databází v elastickém fondu. |
| 40877 | 16bitovém |Elastický fond nelze odstranit pouze v případě, že neobsahuje žádné databáze. Elastický fond obsahuje jednu nebo více databází, a proto jej nelze odstranit. |Chcete-li odstranit databáze z elastického fondu, odstraňte je. |
| 40881 | 16bitovém |Elastický fond%. * ls dosáhl svého limitu počtu databází.  Omezení počtu databází pro elastický fond nemůže překročit (% d) pro elastický fond s (% d) DTU. Došlo k pokusu o vytvoření nebo přidání databáze do elastického fondu, pokud bylo dosaženo limitu počtu databází elastického fondu. | Zvažte zvýšení DTU elastického fondu, pokud je to možné, aby se zvýšil limit databáze nebo odebraly databáze z elastického fondu. |
| 40889 | 16bitovém |DTU nebo úložiště pro elastický fond%. * ls nejde snížit, protože by neposkytoval dostatek úložného prostoru pro své databáze. Probíhá pokus o snížení limitu úložiště elastického fondu pod využitím úložiště. | Zvažte snížení využití úložiště pro jednotlivé databáze v elastickém fondu nebo odebírání databází z fondu, aby se snížila velikost DTU nebo úložiště. |
| 40891 | 16bitovém |Minimální počet jednotek DTU na databázi (% d) nemůže být vyšší než maximální počet jednotek DTU na databázi (% d). Došlo k pokusu o nastavení minimální hodnoty DTU na databázi vyšší než maximální počet jednotek DTU na databázi. |Zajistěte, aby minimální počet jednotek DTU na databázi nepřesáhl maximální hodnotu DTU na databázi. |
| Bude určeno později | 16bitovém |Velikost úložiště pro jednotlivé databáze v elastickém fondu nemůže překročit maximální velikost povolenou pro elastický fond úrovně služby%. * ls. Maximální velikost databáze překračuje maximální velikost povolenou vrstvou služby elastického fondu. |Nastavte maximální velikost databáze v rámci omezení maximální velikosti povolené úrovní služby elastického fondu. |

Související témata:

* [Vytvoření elastického fondu (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Správa elastického fondu (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Vytvoření elastického fondu (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Monitorování a Správa elastického fondu (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Obecné chyby

Následující chyby nespadají do žádné předchozí kategorie.

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16bitovém |(AdministratorLogin) není platný název, protože obsahuje neplatné znaky.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |čtrnáct |Přihlášení se nezdařilo. Přihlašovací jméno pochází z nedůvěryhodné domény a nedá se použít s ověřováním systému Windows.%. &#x2a;LS (přihlášení systému Windows nejsou v této verzi SQL Server podporována.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |čtrnáct |Přihlášení uživatele% se nezdařilo. &#x2a;ls.%. &#x2a;ls%. &#x2a;LS (přihlášení uživatele "%" se nezdařilo&#x2a; . ls ".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |čtrnáct |Přihlášení uživatele% se nezdařilo. &#x2a;ls. Důvod: účet je zakázaný.%. &#x2a;LS |
| 40014 |16bitovém |Ve stejné transakci nelze použít více databází. |
| 40054 |16bitovém |Tabulky bez clusterovaného indexu nejsou v této verzi SQL Server podporovány. Vytvořte clusterovaný index a zkuste to znovu. |
| 40133 |15 |Tato operace není v této verzi SQL Server podporována. |
| 40506 |16bitovém |Zadaný identifikátor SID je pro tuto verzi SQL Server neplatný. |
| 40507 |16bitovém |'%. &#x2a;LS nelze vyvolat s parametry v této verzi SQL Server. |
| 40508 |16bitovém |Příkaz USE není podporován pro přepínání databází. Pro připojení k jiné databázi použijte nové připojení. |
| 40510 |16bitovém |Příkaz%. &#x2a;ls se v této verzi SQL Server nepodporuje. |
| 40511 |16bitovém |Integrovaná funkce%. &#x2a;ls není v této verzi SQL Server podporován. |
| 40512 |16bitovém |Zastaralá funkce% ls není v této verzi SQL Server podporována. |
| 40513 |16bitovém |Proměnná serveru '%. &#x2a;ls není v této verzi SQL Server podporován. |
| 40514 |16bitovém |% ls není v této verzi SQL Server podporován. |
| 40515 |16bitovém |Odkaz na název databáze nebo serveru v%. &#x2a;ls není v této verzi SQL Server podporován. |
| 40516 |16bitovém |Globální dočasné objekty nejsou v této verzi SQL Server podporovány. |
| 40517 |16bitovém |Klíčová slova nebo možnost příkazu '%. &#x2a;ls není v této verzi SQL Server podporován. |
| 40518 |16bitovém |Příkaz DBCC '%. &#x2a;ls není v této verzi SQL Server podporován. |
| 40520 |16bitovém |Zabezpečitelný třída% S_MSG není v této verzi SQL Server podporována. |
| 40521 |16bitovém |Zabezpečitelný třída% S_MSG není v oboru serveru v této verzi SQL Server podporována. |
| 40522 |16bitovém |Objekt zabezpečení databáze%. &#x2a;typ ls není v této verzi SQL Server podporován. |
| 40523 |16bitovém |Implicitní uživatel% &#x2a;vytváření ls není v této verzi SQL Server podporováno. Před použitím uživatele explicitně vytvořte. |
| 40524 |16bitovém |Datový typ%. &#x2a;ls není v této verzi SQL Server podporován. |
| 40525 |16bitovém |S '%. ls ' není v této verzi SQL Server podporován. |
| 40526 |16bitovém |'%. &#x2a;zprostředkovatel sady řádků ls není v této verzi SQL Server podporován. |
| 40527 |16bitovém |Odkazované servery nejsou v této verzi SQL Server podporovány. |
| 40528 |16bitovém |V této verzi SQL Server nelze mapovat uživatele na certifikáty, asymetrické klíče ani na přihlášení systému Windows. |
| 40529 |16bitovém |Integrovaná funkce%. &#x2a;LS v kontextu zosobnění není v této verzi SQL Server podporován. |
| 40532 |odst |Nejde otevřít server%. &#x2a;LS vyžadovaná přihlášením Přihlášení se nezdařilo. |
| 40553 |16bitovém |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz pro zpracování méně řádků.<br/><br/> Snížení počtu `ORDER BY` a `GROUP BY` operací v kódu Transact-SQL pomáhá snižovat nároky na paměť dotazu. |
| 40604 |16bitovém |DATABÁZI nelze vytvořit nebo změnit, protože by překročila kvótu serveru. |
| 40606 |16bitovém |Připojení databází není v této verzi SQL Server podporováno. |
| 40607 |16bitovém |Přihlášení systému Windows nejsou v této verzi SQL Server podporována. |
| 40611 |16bitovém |U serverů může být definováno maximálně 128 pravidel brány firewall. |
| 40614 |16bitovém |Počáteční IP adresa pravidla brány firewall nesmí přesáhnout koncovou IP adresu. |
| 40615 |16bitovém |Nelze otevřít server ' {0} ' požadovaný přihlášením. Klient s IP adresou {1} nemá povolený přístup k serveru.<br /><br />Pokud chcete povolit přístup, pomocí portálu SQL Database nebo spuštěním SP @ no__t-0set @ no__t-1firewall @ no__t-2rule v hlavní databázi vytvořte pravidlo brány firewall pro tuto IP adresu nebo rozsah adres. Tato změna může trvat až pět minut. |
| 40617 |16bitovém |Název pravidla brány firewall, který začíná na (název pravidla), je příliš dlouhý. Maximální délka je 128. |
| 40618 |16bitovém |Název pravidla brány firewall nesmí být prázdný. |
| 40620 |16bitovém |Přihlášení uživatele "%" se nezdařilo. &#x2a;ls. Změna hesla se nezdařila. Změna hesla během přihlášení není v této verzi SQL Server podporována. |
| 40627 |20o |Probíhá operace na serveru {0} a v databázi {1}. Počkejte prosím několik minut, než to zkusíte znovu. |
| 40630 |16bitovém |Ověření hesla se nezdařilo. Heslo nesplňuje požadavky zásad, protože je příliš krátké. |
| 40631 |16bitovém |Zadané heslo je příliš dlouhé. Heslo by nemělo mít více než 128 znaků. |
| 40632 |16bitovém |Ověření hesla se nezdařilo. Heslo nesplňuje požadavky zásad, protože není dostatečně složité. |
| 40636 |16bitovém |Nelze použít rezervovaný název databáze%. &#x2a;LS v této operaci. |
| 40638 |16bitovém |Neplatné ID předplatného (ID předplatného). Předplatné neexistuje. |
| 40639 |16bitovém |Požadavek nevyhovuje schématu: (chyba schématu). |
| 40640 |20o |Na serveru došlo k neočekávané výjimce. |
| 40641 |16bitovém |Zadané umístění je neplatné. |
| 40642 |sedmnáct |Server je aktuálně zaneprázdněný. Zkuste to prosím znovu později. |
| 40643 |16bitovém |Zadaná hodnota hlavičky x-MS-Version je neplatná. |
| 40644 |čtrnáct |Nepovedlo se autorizovat přístup k zadanému předplatnému. |
| 40645 |16bitovém |Název_serveru (servername) nemůže být prázdný nebo mít hodnotu null. Může obsahovat pouze malá písmena "a-z", čísla 0-9 a spojovník. Spojovník nesmí v názvu vést ani končit. |
| 40646 |16bitovém |ID odběru nemůže být prázdné. |
| 40647 |16bitovém |Předplatné (ID předplatného) nemá server (servername). |
| 40648 |sedmnáct |Bylo provedeno příliš mnoho požadavků. Zkuste to prosím znovu později. |
| 40649 |16bitovém |Byl zadán neplatný typ obsahu. Podporuje se jenom Application/XML. |
| 40650 |16bitovém |Předplatné (ID předplatného) neexistuje nebo není připravené na operaci. |
| 40651 |16bitovém |Nepovedlo se vytvořit server, protože předplatné (ID předplatného) je zakázané. |
| 40652 |16bitovém |Nelze přesunout nebo vytvořit server. Předplatné (ID předplatného) bude přesáhnout kvótu serveru. |
| 40671 |sedmnáct |Selhání komunikace mezi bránou a službou pro správu. Zkuste to prosím znovu později. |
| 40852 |16bitovém |Nelze otevřít databázi '%. \*ls ' na serveru '%. \*LS ', který požádal o přihlášení. Přístup k databázi je povolen pouze pomocí připojovacího řetězce s povoleným zabezpečením. Chcete-li získat přístup k této databázi, upravte připojovací řetězce tak, aby obsahovaly hodnotu Secure v plně kvalifikovaném názvu domény serveru – název serveru. Database. Windows. NET by se měly změnit na název serveru. Database. `secure`.windows.net. |
| 40914 | 16bitovém | Nejde otevřít server *[název serveru]* , který požádal o přihlášení. Klient nemá povolený přístup k serveru.<br /><br />Pokud chcete problém opravit, zvažte přidání [pravidla virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16bitovém |SQL Azure systém je zatížen a umísťuje horní limit souběžných operací databáze CRUD pro jeden SQL Database Server (např. vytvořit databázi). Server uvedený v chybové zprávě překročil maximální počet souběžných připojení. Zkuste to znovu později. |
| 45169 |16bitovém |Systém SQL Azure je zatížen a umísťuje horní limit počtu souběžných operací operace CRUD serveru pro jedno předplatné (např. vytvořit server). Předplatné zadané v chybové zprávě překročilo maximální počet souběžných připojení a žádost byla zamítnuta. Zkuste to znovu později. |

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [funkcích Azure SQL Database](sql-database-features.md).
* Přečtěte si o [nákupním modelu založeném na DTU](sql-database-service-tiers-dtu.md).
* Přečtěte si o [modelu nakupování založeném na Vcore](sql-database-service-tiers-vcore.md).

