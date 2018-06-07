---
title: Opravte chyby připojení SQL, přechodné chybě. | Microsoft Docs
description: Informace o odstraňování potíží, diagnostikovat a zabránit chyba připojení SQL nebo přechodné chybě ve službě Azure SQL Database.
keywords: připojení SQL, připojovací řetězec, problémy s připojením, přechodná chyba, došlo k chybě připojení
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: daleche
ms.openlocfilehash: 37cd099e6efe44ee70dc1799ef4b2b4377c571d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647253"
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Oprava a diagnostika chyb připojení SQL a přechodných chyb služby SQL Database a jejich předcházení
Tento článek popisuje, jak zabránit, odstraňování, diagnostikovat a opravit chyby připojení a přechodné chyby, které klientské aplikace, zaznamená při komunikuje se službou Azure SQL Database. Zjistěte, jak nakonfigurovat logika opakovaných pokusů, sestavení připojovacího řetězce a další nastavení připojení.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Přechodné chyby (přechodné chyby)
Přechodná chyba, také známé jako přechodná chyba, má základní příčinu, které brzy vyřeší sám sebe. Příležitostně příčinu přechodné chyby je, když Azure systému rychle posune hardwarové prostředky lepší vyrovnávání zatížení různé úlohy. Většinu těchto událostí Rekonfigurace dokončit za méně než 60 sekund. Během zadaného období Rekonfigurace můžete mít problémy s připojením k databázi SQL. Aplikace, které se připojují k databázi SQL by měly být vytvořeny očekávat tyto přechodné chyby. K jejich zpracování, implementujte logiku opakovaných pokusů v jejich kódu místo zpřístupnění je uživatelům jako chyby aplikace.

Pokud váš klientský program používá ADO.NET, je váš program sdělili o přechodné chybě podle throw z **SqlException**. Porovnání **číslo** vlastnost proti seznamu přechodné chyby, které se nacházejí v horní části článku [kódy chyb SQL pro klientské aplikace SQL Database](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Připojení oproti příkaz
Pokus o připojení SQL nebo znovu vytvořit v závislosti na následující:

* **Při pokusu o připojení dojde k přechodné chybě**: Po prodlevě několik sekund, pokus o připojení.
* **Příkaz dotazu SQL došlo k přechodné chybě**: není okamžitě příkaz opakujte. Místo toho po prodlevě, čerstvě navázání připojení. Opakujte příkaz.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika opakování pro přechodné chyby
Klient programy, které někdy dojde k přechodné chybě jsou robustnější při obsahují logiku opakovaných pokusů.

Pokud váš program komunikuje s databází SQL prostřednictvím middlewaru třetích stran, požádejte dodavatele, jestli middleware obsahuje logiku opakovaných pokusů pro přechodné chyby.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zásady pro opakování
* Pokud chyba je přechodná, opakujte k otevření připojení.
* Nezkoušejte zopakovat přímo příkazu SQL SELECT, který se nezdařilo s přechodnou chybou.
  * Místo toho udržování připojení a poté opakujte SELECT.
* Při aktualizaci SQL příkazu selže s přechodná chyba transakce, vytvořte novou připojení předtím, než se znovu pokusíte o aktualizaci.
  * Logika opakovaných pokusů musí zajistit, že dokončení transakce celé databáze nebo že celá transakce bude vrácena zpět.

### <a name="other-considerations-for-retry"></a>Další důležité informace pro opakování
* Dávkový program, který automaticky spustí po pracovní době a dokončení před ráno si může dovolit být velmi pacientů s dlouho časových intervalů mezi jeho počet pokusů o opakování.
* Program uživatelského rozhraní by měl účet pro lidské tendence, že uvolňovat po příliš dlouho čekání.
  * Řešení nesmí opakovat každých několik sekund, protože tyto zásady můžete vyplnění systému s žádostí.

### <a name="interval-increase-between-retries"></a>Zvyšte interval mezi opakovanými pokusy
Doporučujeme, abyste počkali na 5 sekund před první opakování. Opakování po prodlevě kratší než 5 sekund rizika zahltí cloudové služby. Pro každý další pokusy zpoždění růst exponenciálnímu, až do maximálního počtu 60 sekund.

Informace o blokování období pro klienty, kteří používají ADO.NET, najdete v části [připojení serveru SQL Server sdružování (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Můžete také nastavit maximální počet opakovaných pokusů, než program samoobslužné ukončí.

### <a name="code-samples-with-retry-logic"></a>Ukázky kódu s logika opakovaných pokusů
Příklady kódu s logikou opakování užitečnou jsou k dispozici na:

- [Pružným způsobem připojení k serveru SQL s ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Pružným způsobem připojení k serveru SQL s PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testování logika opakovaných pokusů
K testování logika opakovaných pokusů, musíte simulovat nebo způsobit chybu, která může být vyřešen při běhu programu.

#### <a name="test-by-disconnecting-from-the-network"></a>Test odpojení od sítě
Jedním ze způsobů, které můžete testovat logika opakovaných pokusů je klientský počítač odpojit od sítě, když je aplikace spuštěna. Chyba je:

* **SqlException.Number** = 11001
* Zpráva: "znám žádný takový hostitel je"

Jako součást první pokus opakovat můžete program opravte chyby v pravopisu a pak se pokusíte připojit.

Chcete-li tento test praktické, odpojte počítač ze sítě předtím, než aplikace. Potom váš program rozpozná runtime parametr, který vede k:

* 11001 dočasně přidáte do seznamu chyb vzít v úvahu jako přechodný.
* Pokus první připojení jako obvykle.
* Poté, co je chyba zachycení, odeberte 11001 ze seznamu.
* Zobrazí zprávu, že uživatel připojit počítač k síti.
   * Další pozastavit provádění pomocí **Console.ReadLine** metoda nebo dialogovém okně tlačítko OK. Uživatel stisknutím klávesy Enter po je počítač připojen do sítě.
* Pokusí znovu připojit, byla očekávána úspěch.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Test Chyba v pravopisu název databáze při připojování
Váš program můžete účelově chybně uživatelské jméno před první pokus o připojení. Chyba je:

* **SqlException.Number** = 18456
* Zpráva: "přihlášení se nezdařilo pro uživatele 'WRONG_MyUserName'."

Jako součást první pokus opakovat můžete program opravte chyby v pravopisu a pak se pokusíte připojit.

Chcete-li tento test praktické, váš program rozpozná runtime parametr, který vede k:

* Dočasně 18456 přidejte do jeho seznam chyb vzít v úvahu jako přechodný.
* Přidejte účelově 'WRONG_' na jméno uživatele.
* Poté, co je chyba zachycení, odeberte 18456 ze seznamu.
* Odeberte 'WRONG_' uživatelského jména.
* Pokusí znovu připojit, byla očekávána úspěch.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry objektu SqlConnection .NET pro opakování připojení
Pokud váš klientský program připojí k databázi SQL s použitím třídy rozhraní .NET Framework **System.Data.SqlClient.SqlConnection**, pomocí rozhraní .NET 4.6.1 nebo novější (nebo .NET Core) tak, aby jeho připojení opakování funkci můžete používat. Další informace o funkci najdete v tématu [tato webová stránka](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Při vytváření [připojovací řetězec](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pro vaše **SqlConnection** objektu, koordinaci hodnoty mezi následujícími parametry:

* **ConnectRetryCount**:&nbsp;&nbsp;výchozí hodnota je 1. Rozsah je od 0 do 255.
* **ConnectRetryInterval**:&nbsp;&nbsp;výchozí hodnota je 1 sekunda. Rozsah je 1 až 60.
* **Časový limit připojení**:&nbsp;&nbsp;výchozí hodnota je 15 sekund. Rozsah hodnot je 0 až 2147483647.

Konkrétně vybrané hodnoty měli následující true rovnosti:

Časový limit připojení = ConnectRetryCount * ConnectionRetryInterval

Například pokud je počet rovná 3 a interval rovná 10 sekund, vypršení časového limitu jenom 29 sekund nedává systému dostatek času pro jeho třetí a finální opakovat, pokud chcete připojit: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Připojení oproti příkaz
**ConnectRetryCount** a **ConnectRetryInterval** parametry umožní vaše **SqlConnection** objekt opakujte operaci připojení bez informuje nebo bothering programu, například vrácení řízení vašeho programu. Opakované pokusy se může objevit v následujících situacích:

* volání metody mySqlConnection.Open
* volání metody mySqlConnection.Execute

Není k dispozici subtlety. Pokud dojde k přechodné chybě. při vaší *dotazu* je spouštěna, vaše **SqlConnection** objekt nepodporuje operaci připojení. Je určitě není dotaz opakujte. Ale **SqlConnection** velmi rychle zkontroluje připojení před odesláním dotazu pro provedení. Pokud Rychlá kontrola zjistí problému s připojením **SqlConnection** opakování operace připojení. Pokud opakovaném úspěšné, odešle dotaz pro provedení.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Měli ConnectRetryCount nelze kombinovat s logika opakovaných pokusů aplikace?
Předpokládejme, že aplikace má robustní vlastní opakování logiku. Může opakujte operaci připojení čtyřikrát. Pokud přidáte **ConnectRetryInterval** a **ConnectRetryCount** = 3 pro připojovací řetězec, zvýšíte počet opakování do 4 * 3 = 12 opakování. Hodláte nemusí takové velký počet opakování.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Připojení k databázi SQL
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Připojení: Připojovací řetězec
Připojovací řetězec, který je potřeba připojit k databázi SQL se mírně liší od řetězec použitý k připojení k systému SQL Server. Připojovací řetězec můžete zkopírovat pro vaši databázi z [portál Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Připojení: IP adresa
Je nutné nakonfigurovat databázi SQL serveru tak, aby přijímal komunikaci z IP adresy počítače, který hostuje váš klientský program. Nastavení této konfigurace, upravte nastavení brány firewall pomocí [portál Azure](https://portal.azure.com/).

Pokud zapomenete konfigurace IP adresy, váš program selže s užitečný chybovou zprávu, která stavy nezbytné IP adresu.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Další informace najdete v tématu [nakonfigurovat nastavení brány firewall pro službu SQL Database](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Připojení: porty
Obvykle je potřeba zajistit, že pouze port 1433 je otevřený pro odchozí komunikaci na počítači, který hostuje váš klientský program.

Například když je váš klientský program hostované na počítači se systémem Windows, můžete bránu Windows Firewall na hostiteli otevřít port 1433.

1. Otevřete ovládací panely.

2. Vyberte **všechny položky okna Ovládací panely** > **brány Windows Firewall** > **upřesňující nastavení** > **odchozí pravidla**   >  **Akce** > **nové pravidlo**.

Pokud je váš klientský program hostované na virtuální počítač Azure (VM), přečtěte si [porty nad rámec 1433 pro technologii ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Obecné informace o konfiguraci portů a adres IP, najdete v části [brány firewall databáze Azure SQL Database](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Připojení: ADO.NET 4.6.1
Pokud váš program používá třídy ADO.NET jako **System.Data.SqlClient.SqlConnection** pro připojení k databázi SQL, doporučujeme použít rozhraní .NET Framework verze 4.6.1 nebo novější.

ADO.NET 4.6.1:

* Pro databázi SQL, spolehlivost při otevření připojení pomocí **SqlConnection.Open** metoda. **Otevřete** metoda mechanismů best effort opakování v reakci na přechodných některých chyb nyní zahrnuje během časového limitu připojení.
* Sdružování připojení je podporováno, což zahrnuje efektivní ověření, který funguje objekt připojení nabízí vašeho programu.

Použijete-li objekt připojení z fondu připojení, doporučujeme vám, že váš program dočasně ukončení připojení, když není okamžitě používán. Není nákladné znovu otevřít připojení, ale je k vytvoření nového připojení.

Pokud používáte ADO.NET 4.0 nebo starší, doporučujeme upgradovat na nejnovější ADO.NET. Od listopadu 2015, můžete [stáhnout ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostika
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostika: Test, zda může připojit nástroje
Pokud vašeho programu se nepodaří připojit k databázi SQL, je jednou z možností diagnostiky se pokusí připojit k programu nástroj. V ideálním případě nástroj připojuje pomocí stejné knihovny, který program používá.

Na libovolném počítači Windows můžete vyzkoušet tyto nástroje:

* SQL Server Management Studio (ssms.exe), který se připojuje pomocí ADO.NET
* Sqlcmd.exe, který se připojuje pomocí [rozhraní ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

Až se váš program připojí, otestujte, zda funguje krátké SQL SELECT dotazu.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostika: Zkontrolujte otevřené porty
Pokud máte podezření, že pokusy o připojení se nezdaří kvůli problémům s port, můžete spustit nástroj v počítači, která generuje sestavy na konfiguraci portů.

V systému Linux může být užitečné následující nástroje:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Změňte hodnotu příkladu na IP adresu.

V systému Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) nástroj může být užitečné. Tady je příklad spuštění, dotaz situace port na serveru SQL Database a která byla spuštěna na přenosný počítač:

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
Občasný problém je někdy nejlépe zjištěném detekce obecné vzoru přes dny nebo týdny.

Váš klient může být užitečné při diagnostiku protokolováním všechny chyby, který nalezne. Je možné ke korelaci položky protokolu s daty chyba, která interně SQL Database samotné protokoly.

Enterprise knihovny 6 (EntLib60) nabízí spravované rozhraní .NET třídy vám pomůže při protokolování. Další informace najdete v tématu [5 – stejně snadná jako návratem vypnout protokolu: použijte blokem protokolování aplikace](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostika: Zkontrolujte protokoly systému chyby
Tady jsou některé vyberte Transact-SQL příkazy, které dotaz protokoly chyb a další informace.

| Dotaz protokolu | Popis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) zobrazení nabízí informace o jednotlivých událostech, které zahrnují některé, která mohou způsobit přechodné chyby nebo chyby připojení.<br/><br/>V ideálním případě mohou korelovat **start_time** nebo **end_time** hodnoty s informacemi o když váš klientský program došlo k potížím.<br/><br/>Musíte se připojit k *hlavní* databáze ke spuštění tohoto dotazu. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) zobrazení nabízí souhrnné počty typů událostí pro další diagnostiku.<br/><br/>Musíte se připojit k *hlavní* databáze ke spuštění tohoto dotazu. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostika: Vyhledejte problém události v protokolu databáze SQL
Můžete hledat položky o problém události v protokolu databáze SQL. Zkuste následující příkaz Transact-SQL, vyberte v *hlavní* databáze:

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


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Několik řádků vrácená z sys.fn_xe_telemetry_blob_target_read_file
Následující příklad ukazuje, jak může vypadat vrácený řádek. Hodnoty null, zobrazí se často není null v dalších řádcích.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Knihovna Enterprise 6
Enterprise knihovny 6 (EntLib60) je architektura tříd rozhraní .NET usnadňující implementovat robustní klienti cloudové služby, z nichž jeden je služba SQL Database. Pokud chcete vyhledat témata vyhrazený pro každou oblast, ve kterém může být užitečné EntLib60, najdete v části [Enterprise knihovny 6 – duben 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika opakovaných pokusů pro přechodné chyby zpracování je jedné oblasti, ve kterém může být užitečné EntLib60. Další informace najdete v tématu [4 - Perseverance, tajný klíč všechny vítězství: použití bloku aplikace zpracování přechodné chyby](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Zdrojový kód pro EntLib60 je k dispozici ke stažení veřejné [Download Center](http://go.microsoft.com/fwlink/p/?LinkID=290898). Společnost Microsoft nemá žádné plány pro EntLib provést další funkce aktualizace nebo aktualizace údržby.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Třídy EntLib60 pro přechodné chyby a zkuste to znovu
Následující třídy EntLib60 jsou obzvláště užitečná pro logiku opakovaných pokusů. Všechny tyto třídy se nacházejí v nebo pod oborem názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

V oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** – třída
  
  * **ExecuteAction** – metoda
* **ExponentialBackoff** – třída
* **SqlDatabaseTransientErrorDetectionStrategy** class
* **ReliableSqlConnection** – třída
  
  * **Parametr ExecuteCommand** – metoda

V oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** – třída
* **NeverTransientErrorDetectionStrategy** – třída

Zde jsou některé odkazy na informace o EntLib60:

* Stažení seznamu volné: [– Příručka vývojáře do knihovny Microsoft Enterprise, 2. vydání](http://www.microsoft.com/download/details.aspx?id=41145).
* Osvědčené postupy: [opakujte obecné pokyny](../best-practices-retry-general.md) má vynikající podrobné informace o logika opakovaných pokusů.
* Stažení NuGet: [Enterprise Library - přechodné chyby zpracování aplikace bloku 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Protokolování bloku
* Blok protokolování je velmi flexibilní a konfigurovat řešení, které vám pomůže:
  
  * Vytvoření a uložení zprávy protokolu v celé řadě umístění.
  * Kategorizaci a filtrování zprávy.
  * Shromážděte kontextové informace, které jsou užitečné pro ladění a trasování, a také pro auditování a obecné protokolování požadavky.
* Protokolování bloku abstrahuje funkce protokolování cíl protokolu tak, aby je konzistentní, bez ohledu na umístění a typ úložiště cíl protokolování kódu aplikace.

Další informace najdete v tématu [5 – stejně snadná jako návratem vypnout protokolu: použijte blokem protokolování aplikace](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metoda zdrojového kódu
Další z **SqlDatabaseTransientErrorDetectionStrategy** třídy, je C# zdrojový kód **IsTransient** metoda. Zdrojový kód vysvětluje, které chyby považovány za přechodný a worthy z opakování od dubna 2013.

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
* Další informace o řešení potíží s další běžné problémy s připojením databáze SQL najdete v tématu [řešení problémů s připojením k databázi SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
* [Knihovny připojení k databázi SQL a SQL Server](sql-database-libraries.md)
* [Připojení serveru SQL Server sdružování (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Opakováním* je Apache 2.0 licenci pro obecné účely, opakování knihovny, které jsou napsané v Pythonu,](https://pypi.python.org/pypi/retrying) zjednodušit úkolu přidávání opakování chování pro jakoukoli.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

