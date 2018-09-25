---
title: Oprava chyb připojení SQL, přechodná chyba | Dokumentace Microsoftu
description: Informace o řešení potíží, Diagnostika a prevence chyb připojení SQL a přechodných chyb ve službě Azure SQL Database.
keywords: připojení k SQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 08/01/2018
ms.openlocfilehash: f381eaad61c98228ea9be2665ebed5878b666317
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064233"
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Oprava a diagnostika chyb připojení SQL a přechodných chyb služby SQL Database a jejich předcházení
Tento článek popisuje, jak zabránit, řešení potíží, diagnostiku a zmírnění chyby připojení a přechodné chyby, které klientské aplikace, zaznamená při interakci s Azure SQL Database. Zjistěte, jak konfigurovat logiku opakování, vytvoření připojovacího řetězce a další nastavení připojení.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Přechodné chyby (přechodné chyby)
O přechodnou chybu, označované také jako přechodných chyb, má základní příčinu, který se brzy přeloží samotný. Příležitostné příčinou přechodných chyb je posune systému Azure rychle hardwarové prostředky lepší vyrovnávání zatížení různé úlohy. Většinu těchto událostí Rekonfigurace dokončit za méně než 60 sekund. Během této změny konfigurace časové období můžete mít problémy s připojením ke službě SQL Database. Aplikace s připojením k SQL Database by měly být sestaveny očekávat tyto přechodné chyby. Jejich zpracování, implementujte logiku opakování ve svém kódu místo jejich zpřístupnění uživatelům za chyby aplikace.

Pokud váš klientský program používá technologie ADO.NET, aplikace je oznámen o přechodnou chybu ve vyvolání výjimky z **SqlException**. Porovnání **číslo** vlastnost seznamem přechodné chyby, které se nacházejí v horní části článku [kódy chyb SQL pro klientské aplikace SQL Database](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Připojení a příkaz
Pokus o připojení SQL nebo ho navažte znovu, v závislosti na následující:

* **Vyvolá se o přechodnou chybu během pokusu o připojení**: po trvat několik sekund, pokus o připojení.
* **O přechodnou chybu spadá příkaz dotazu SQL**: Neprovádějte okamžité opakování příkazu. Místo toho po prodlevě čerstvě navázání připojení. Opakujte příkaz.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika opakování pro přechodné chyby
Klientské programy, které občas dojde k přechodné chybě jsou výkonnější při obsahují logiku opakování.

Pokud váš program komunikuje s SQL Database prostřednictvím třetích stran middleware, požádejte dodavatele určuje, zda middleware obsahuje logiku opakování pro přechodné chyby.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zásady opakování
* Pokud chyba je přechodná, zkuste to znovu otevřít připojení.
* Neprovádějte opakování přímo příkazu SQL SELECT, která selhala s přechodnou chybu.
  * Místo toho vytvořit nové připojení a pak zkuste vybrat.
* Nepodaří-li prohlášení aktualizace SQL s přechodnou chybu, vytvořte nové připojení před dalším pokusem o aktualizaci.
  * Logika opakovaných pokusů musí zajistit, že celá databáze transakce dokončena nebo že celá transakce bude vrácena zpět.

### <a name="other-considerations-for-retry"></a>Další důležité informace pro opakování
* Program služby batch, který automaticky spustí po pracovní době a skončí před ráno, můžete si dovolit být velmi pacienty s dlouho časových intervalů mezi jeho opakované pokusy.
* Program uživatelského rozhraní by měl účet pro lidské tendence uvolňovat po příliš dlouhé čekání.
  * Řešení nesmí opakovat každých několik sekund, protože tyto zásady můžete vyplnění systém žádostmi.

### <a name="interval-increase-between-retries"></a>Zvyšte interval mezi opakovanými pokusy
Doporučujeme vám, počkat po dobu 5 sekund před prvním opakováním. Opakování po prodlevě kratší než 5 sekund rizika zahlcení cloudovou službu. Pro každým dalším pokusem zpoždění růst exponenciálně, až do maximálního počtu 60 sekund.

Informace o blokování období pro klienty, kteří používají rozhraní ADO.NET, naleznete v tématu [připojení SQL serveru (ADO.NET) sdružování](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Můžete také chtít nastavit maximální počet opakování před svým ukončí program.

### <a name="code-samples-with-retry-logic"></a>Ukázky kódu s logika opakovaných pokusů
Příklady kódu s logikou opakování jsou k dispozici na:

- [Odolné připojení k SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Odolné připojení k SQL pomocí PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Otestovat svoji logiku opakování
Otestovat svoji logiku opakování, můžete simulovat nebo způsobit chybu, která můžete opravit, když váš program stále běží.

#### <a name="test-by-disconnecting-from-the-network"></a>Otestujte odpojení od sítě
Jedním ze způsobů můžete otestovat svoji logiku opakování je klientský počítač odpojit od sítě, zatímco program běží. Chyba je:

* **SqlException.Number** = 11001
* Zpráva: "žádný takový hostitel se označuje"

Jako součást první pokus o opakování můžete program opravte chyba a pak se pokusíte připojit.

Chcete-li tento test praktické, odpojte před spuštěním programu počítači ze sítě. Potom program rozpozná parametr modulu runtime, který způsobí, že program:

* 11001 přidejte dočasně do seznamu chyb považovat za přechodná.
* Zkuste jeho prvního připojení jako obvykle.
* Po chybě je zachycena, odeberte 11001 ze seznamu.
* Zobrazte zpráva s oznámením uživateli připojit počítač k síti.
   * Další přerušit provádění kódu s použitím buď **Console.ReadLine** metody nebo dialogové okno s tlačítko OK. Uživatel stiskne klávesu Enter, jakmile je počítač připojen do sítě.
* Pokuste se znovu připojit, byl očekáván úspěch.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Otestujte Chyba v při připojování pravopisu název databáze
Váš program může záměrně chybně uživatelské jméno před první pokus o připojení. Chyba je:

* **SqlException.Number** = 18456
* Zpráva: "přihlášení se nezdařilo pro uživatele"WRONG_MyUserName"."

Jako součást první pokus o opakování můžete program opravte chyba a pak se pokusíte připojit.

Chcete-li tento test praktické, váš program rozpozná parametr modulu runtime, který způsobí, že program:

* Přidejte do seznamu chyb považovat za přechodná dočasně 18456.
* Přidáte záměrně "WRONG_" uživatelskému jménu.
* Po chybě je zachycena, odeberte 18456 ze seznamu.
* Odebrání "WRONG_" uživatelské jméno.
* Pokuste se znovu připojit, byl očekáván úspěch.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry připojení SqlConnection .NET pro připojení opakování
Pokud váš klientský program připojuje ke službě SQL Database s použitím rozhraní .NET Framework třída **System.Data.SqlClient.SqlConnection**, použijte verzi .NET 4.6.1 nebo novější (nebo .NET Core) tak, aby můžete používat jeho funkce opakovat připojení. Další informace o funkci najdete v tématu [tuto webovou stránku](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Při sestavení [připojovací řetězec](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pro vaše **SqlConnection** objektu, koordinovat hodnoty mezi následujícími parametry:

* **Atributy ConnectRetryCount**:&nbsp;&nbsp;výchozí hodnota je 1. Rozsah je 0 až 255.
* **ConnectRetryInterval**:&nbsp;&nbsp;výchozí hodnota je 1 sekunda. Rozsah je 1 až 60.
* **Časový limit připojení**:&nbsp;&nbsp;výchozí hodnota je 15 sekund. Rozsah je 0 až 2147483647.

Konkrétně vybrané hodnoty třeba následující rovnosti true:

Časový limit připojení = ConnectRetryCount * ConnectionRetryInterval

Například, pokud počet rovná 3 a interval je rovno 10 sekund, vypršení časového limitu pouze 29 sekund nedává systém dostatek času pro jeho třetí a poslední pokus o připojení: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Připojení a příkaz
**ConnectRetryCount** a **ConnectRetryInterval** parametry umožňují vaše **SqlConnection** objektu opakujte tuto operaci připojení bez sděluje nebo varovat vaší program, jako je například vrácení řízení programu. Opakované pokusy může dojít v následujících situacích:

* volání metody mySqlConnection.Open
* volání metody mySqlConnection.Execute

Není k dispozici subtlety. Pokud dojde k přechodné chybě při vaší *dotazu* se zpracovává, vaše **SqlConnection** objekt nebude opakovat operaci připojení. Je určitě není dotaz opakujte. Ale **SqlConnection** velmi rychle ověří připojení před odesláním dotaz pro spuštění. Pokud je rychlá kontrola zjistí problém připojením **SqlConnection** opakovat operaci připojení. Pokud opakování proběhne úspěšně, odešle se dotaz pro spuštění.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Atributy ConnectRetryCount kombinovat s aplikací logiky opakování?
Předpokládejme, že má vaše aplikace logiky robustní vlastní opakování. Může opakovat operaci připojení čtyřikrát. Pokud chcete přidat **ConnectRetryInterval** a **ConnectRetryCount** = 3 připojovací řetězec se zvýší počet opakování 4 * 3 = 12 opakovaných pokusů. Hodláte nemusí velký počet opakování.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Připojení ke službě SQL Database
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Připojení: Připojovací řetězec
Připojovací řetězec, který je potřebná pro připojení k SQL Database se mírně liší od řetězec použitý k připojení k systému SQL Server. Připojovací řetězec můžete zkopírovat z vaší databáze [webu Azure portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Připojení: IP adresa
Musíte nakonfigurovat databázi SQL serveru tak, aby přijímal komunikaci od IP adresu počítače, který hostuje klientský program. Chcete-li nastavit tuto konfiguraci, upravte nastavení brány firewall prostřednictvím [webu Azure portal](https://portal.azure.com/).

Pokud zapomenete konfigurace IP adresy, program se nezdaří s po ruce chybová zpráva s oznámením potřebné adresy IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Další informace najdete v tématu [konfigurace nastavení brány firewall pro SQL Database](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Připojení: porty
Obvykle je potřeba zajistit, že je otevřený pro odchozí komunikaci na počítači, který hostuje klientský program jenom port 1433.

Například pokud váš klientský program je hostovaný na počítači Windows, můžete bránu Windows Firewall na hostiteli otevřít port 1433.

1. Otevřete ovládací panely.

2. Vyberte **všechny kontrolu nad položkami, Panel** > **brány Windows Firewall** > **upřesňující nastavení** > **odchozí pravidla**   >  **Akce** > **nové pravidlo**.

Pokud váš klientský program je hostovaný na virtuálním počítači Azure (VM), přečtěte si [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Základní informace o konfiguraci portů a adres IP, naleznete v tématu [brány firewall Azure SQL Database](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Připojení: ADO.NET 4.6.2 nebo novější
Pokud program používá třídy rozhraní ADO.NET typu **System.Data.SqlClient.SqlConnection** pro připojení k SQL Database, doporučujeme používat .NET Framework verze 4.6.2 nebo novější.

Od verze ADO.NET 4.6.2:

- Otevřít pokusu o připojení pro Azure SQL Database, následné vylepšení výkonu aplikací povolenou podporu cloudu opakovat okamžitě.

Spouštění pomocí technologie ADO.NET 4.6.1:

* Pro databáze SQL je vylepšená spolehlivost při otevření připojení pomocí **SqlConnection.Open** metody. **Otevřít** metoda nyní zahrnuje mechanismy opakování best effort v reakci na přechodné chyby pro některé chyby během časového limitu připojení.
* Sdružování připojení je podporováno, což zahrnuje efektivní ověření, že objekt připojení poskytuje program pracuje.

Při použití objektu připojení z fondu připojení, doporučujeme, že váš program dočasně zavře připojení, pokud není ihned používá. Není nákladné znovu otevřít připojení, ale je vytvořit nové připojení.

Pokud použijete rozhraní ADO.NET 4.0 nebo starší, doporučujeme upgradovat na nejnovější technologie ADO.NET. Od srpna 2018 můžete [stáhnout ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/2018/04/30/announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostika
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostika: Test, zda se můžete připojit nástroje
Pokud váš program se nedokázal připojit ke službě SQL Database, je jednou z možností diagnostiky snaží připojit pomocí programu nástroje. V ideálním případě nástroj připojuje pomocí, který program používá stejnou knihovnu.

Na libovolném počítači s Windows můžete vyzkoušet tyto nástroje:

* SQL Server Management Studio (ssms.exe), který se připojuje pomocí ADO.NET
* Sqlcmd.exe, který se připojuje pomocí [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

Po připojení aplikace otestujte, zda funguje krátký dotaz SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostika: Zkontrolujte otevřené porty
Pokud se domníváte, že kvůli problémům s port selžou pokusy o připojení, můžete spustit nástroj, který v počítači, který informuje o konfiguraci portů.

V Linuxu můžou být užitečné následující nástroje:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Změňte hodnotu příkladu bude vaši IP adresu.

Na Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) nástroje můžou být užitečné. Tady je příklad spuštění, který dotazovat situace port na serveru služby SQL Database a, která byla spuštěna na přenosný počítač:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostiky: Chyby protokolu
Přerušovaně docházelo k problému se někdy nejlépe zjištěném detekce obecný vzor přes dnů nebo týdnů.

Váš klient může být užitečné při diagnózu funkcí protokolování všech chyb, který nalezne. Je možné korelovat s daty chyba z interně SQL Database samotné protokolování záznamů protokolu.

Knihovna Enterprise 6 (EntLib60) nabízí třídy .NET spravovat jako pomoc s protokolování. Další informace najdete v tématu [5 - stejně jednoduše jako si pádům protokol: použijte Logging Application Block](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostika: Zkontrolujte chyby v protokolech systému
Tady jsou některé příkazy jazyka Transact-SQL SELECT, které se dotazují protokoly chyb a další informace.

| Dotaz protokolu | Popis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) nabízí informace o jednotlivých událostech, které zahrnuje některé, které mohou způsobit přechodné chyby nebo selhání připojení k zobrazení.<br/><br/>V ideálním případě by mohli porovnat **start_time** nebo **end_time** hodnoty s informacemi o Pokud váš klientský program došlo k potížím.<br/><br/>Musíte se připojit k *hlavní* databáze ke spuštění tohoto dotazu. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) zobrazení nabízí souhrnné počty typů událostí pro další diagnostiku.<br/><br/>Musíte se připojit k *hlavní* databáze ke spuštění tohoto dotazu. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostika: Vyhledejte problém události v protokolu databáze SQL
Můžete hledat záznamy o problém události v protokolu databáze SQL. Následující příkaz jazyka Transact-SQL SELECT v akci *hlavní* databáze:

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Několik řádků vrácených z sys.fn_xe_telemetry_blob_target_read_file
Následující příklad ukazuje, jak může vypadat vrácený řádek. Zobrazí hodnoty null nejsou často null v dalších řádcích.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Knihovna Enterprise 6
Knihovna Enterprise 6 (EntLib60) je architektura tříd .NET, která pomáhá implementovat robustní klienti cloudových služeb, z nichž jeden je služba SQL Database. Pokud chcete vyhledat témata vyhrazený pro jednotlivé oblasti, ve kterém může být užitečné EntLib60, naleznete v tématu [knihovny Enterprise 6. dubna 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika opakování pro zpracování přechodných chyb je jednu oblast, ve kterém může být užitečné EntLib60. Další informace najdete v tématu [4 - Perseverance, tajný klíč všechny vítězství: použít blok aplikací zpracování přechodných selhání](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Zdrojový kód pro EntLib60 je k dispozici ke stažení veřejné [Download Center](http://go.microsoft.com/fwlink/p/?LinkID=290898). Společnost Microsoft nemá žádné plány na EntLib provádět další aktualizace funkcí nebo aktualizací údržby.
>
>

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Třídy EntLib60 pro přechodné chyby a zkuste to znovu
Následující třídy EntLib60 jsou zvláště užitečná pro logiku opakování. Všechny tyto třídy se nacházejí v nebo v rámci oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

V oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** třídy

  * **ExecuteAction** – metoda
* **ExponentialBackoff** třídy
* **SqlDatabaseTransientErrorDetectionStrategy** class
* **ReliableSqlConnection** třídy

  * **Parametr ExecuteCommand** – metoda

V oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** třídy
* **NeverTransientErrorDetectionStrategy** třídy

Tady je pár odkazů na informace o EntLib60:

* Knihu zdarma ke stažení: [Příručka pro vývojáře k Microsoft Enterprise Library 2 edition](http://www.microsoft.com/download/details.aspx?id=41145).
* Osvědčené postupy: [obecné pokyny k opakovaným](../best-practices-retry-general.md) má skvělé diskuzi o logika opakovaných pokusů.
* Stažení NuGet: [Enterprise Library - přechodné selhání zpracování aplikace bloku 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Protokolování bloku
* Blok protokolování je vysoce flexibilní a je možné konfigurovat řešení, které vám umožní:

  * Vytvářet a ukládat zprávy protokolu v nejrůznějších umístění.
  * Kategorizace a filtrování zpráv.
  * Shromážděte kontextové informace, které jsou užitečné pro ladění a trasování, stejně jako pro auditování a obecné protokolování požadavky.
* Blok protokolování abstrahuje funkce protokolování cíl protokolu tak, aby kód aplikace je konzistentní bez ohledu na umístění a typ protokolování cílového úložiště.

Další informace najdete v tématu [5 - stejně jednoduše jako si pádům protokol: použijte Logging Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Zdrojový kód EntLib60 IsTransient – metoda
Další z **SqlDatabaseTransientErrorDetectionStrategy** třídy, je zdrojový kód jazyka C# pro **IsTransient** metody. Zdrojový kód objasňuje, které chyby byly považovat za přechodná a albertových opakování, od dubna 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Další postup
* Další informace o řešení potíží s dalších běžných potíží s připojením SQL Database najdete v tématu [řešení problémů s připojením ke službě Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
* [Připojení knihoven pro službu SQL Database a SQL Server](sql-database-libraries.md)
* [Připojení k SQL serveru sdružování (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Opakování* je Apache 2.0 licenci pro obecné účely, opakování knihovny napsané v Pythonu,](https://pypi.python.org/pypi/retrying) zjednodušuje úlohu přidávání chování při opakování k prakticky cokoli.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
