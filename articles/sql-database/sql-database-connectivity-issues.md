---
title: Práce s přechodnými chybami
description: Zjistěte, jak řešit potíže, diagnostikovat a zabránit chybě připojení SQL nebo přechodné chybě v Azure SQL Database.
keywords: připojení SQL,připojovací řetězec,problémy s připojením,přechodná chyba,chyba připojení
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: d2b56e259f551f7655936c975a7a864a27a1df79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269078"
---
# <a name="troubleshooting-transient-connection-errors-to-sql-database"></a>Poradce při potížích s chybami přechodných připojení k databázi SQL

Tento článek popisuje, jak zabránit chybám připojení, jejich řešení problémů, diagnostikovat je a zmírnit, s nimiž se klientská aplikace setká při interakci s databází Azure SQL Database. Zjistěte, jak nakonfigurovat logiku opakování, sestavit připojovací řetězec a upravit další nastavení připojení.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Přechodné chyby (přechodné chyby)

Přechodná chyba, označovaná také jako přechodná chyba, má základní příčinu, která se brzy vyřeší sama. Příležitostnou příčinou přechodných chyb je, když systém Azure rychle přesune hardwarové prostředky, aby lépe vyvážil různé úlohy. Většina těchto událostí změny konfigurace skončí za méně než 60 sekund. Během tohoto časového rozpětí změny konfigurace může mít problémy s připojením k databázi SQL. Aplikace, které se připojují k databázi SQL, by měly být vytvořeny tak, aby očekávaly tyto přechodné chyby. Chcete-li je zpracovat, implementujte logiku opakování v jejich kódu namísto jejich navařování uživatelům jako chyby aplikace.

Pokud váš klientský program používá ADO.NET, program je informován o přechodné chybě při vyvolání **sqlexception**. 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Připojení vs. příkaz

Opakujte připojení SQL nebo jej znovu vytvořte v závislosti na následujících vlastnostech:

- **Během pokusu o připojení dojde k přechodné chybě.**

Po několikasekundové prodlevě opakujte připojení.

- **Během příkazu dotazu SQL došlo k přechodné chybě.**

Příkaz okamžitě neopakujte. Místo toho, po zpoždění, čerstvě navázat připojení. Potom opakujte příkaz.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika opakování pro přechodné chyby

Klientské programy, které občas dojít přechodné chyby jsou robustnější, pokud obsahují logiku opakování. Když váš program komunikuje s SQL Database prostřednictvím middleware jiného výrobce, zeptejte se dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zásady pro opakování

- Pokud je chyba přechodná, zkuste znovu otevřít připojení.
- Neopakujte přímo příkaz `SELECT` SQL, který selhal s přechodnou chybou. Místo toho vytvořte nové připojení a `SELECT`opakujte .
- Pokud příkaz `UPDATE` SQL selže s přechodnou chybou, vytvořte nové připojení před opakováním update. Logika opakování musí zajistit, že byla dokončena celá databázová transakce nebo že je vrácena zpět celá transakce.

### <a name="other-considerations-for-retry"></a>Další důležité informace o opakování

- Dávkový program, který se automaticky spustí po pracovní době a skončí před ránem, si může dovolit být velmi trpělivý s dlouhými časovými intervaly mezi pokusy o opakování.
- Program uživatelského rozhraní by měl vysvětlit lidskou tendenci vzdát se po příliš dlouhém čekání. Řešení nesmí opakovat každých několik sekund, protože tato zásada může zaplavit systém s požadavky.

### <a name="interval-increase-between-retries"></a>Intervalové zvýšení mezi opakovanými pokusy

Doporučujeme počkat 5 sekund před prvním opakováním. Opakování po zpoždění kratší než 5 sekund hrozí zahlcení cloudové služby. Pro každou další opakování zpoždění by měl růst exponenciálně, až maximálně 60 sekund.

Diskuse o období blokování pro klienty, kteří používají ADO.NET, naleznete v [tématu sql server sdružování připojení (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Můžete také nastavit maximální počet opakování před programem samouk.

### <a name="code-samples-with-retry-logic"></a>Ukázky kódu s logikou opakování

Příklady kódu s logikou opakování jsou k dispozici na adrese:

- [Připojení k SQL pružně pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Připojení k SQL s PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Otestujte logiku opakování

Chcete-li otestovat logiku opakování, je nutné simulovat nebo způsobit chybu, která může být opravena, zatímco program je stále spuštěn.

#### <a name="test-by-disconnecting-from-the-network"></a>Test odpojením od sítě

Jedním ze způsobů, jak můžete otestovat logiku opakování, je odpojení klientského počítače od sítě v době, kdy je program spuštěn. Chyba je:

- **SqlException.Number** = 11001
- Zpráva " "Žádný takový hostitel není znám"

Jako součást prvního pokusu o opakování můžete znovu připojit klientský počítač k síti a potom se pokusit o připojení.

Chcete-li tento test provést jako praktický, odpojte před spuštěním programu počítač ze sítě. Program pak rozpozná parametr runtime, který způsobí, že program:

- Dočasně přidat 11001 do seznamu chyb považovat za přechodné.
- Pokuste se o jeho první připojení jako obvykle.
- Po zachycovat chybu odeberte 11001 ze seznamu.
- Zobrazí zprávu s upozorněním, že má uživatel připojit počítač k síti.
- Pozastavte další provádění pomocí metody **Console.ReadLine** nebo dialogového okna s tlačítkem OK. Uživatel stiskne klávesu Enter po připojení počítače k síti.
- Pokuste se znovu připojit a očekáváte úspěch.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Test chybně napsaný název databáze při připojování

Program může úmyslně chybně napsané uživatelské jméno před prvním pokusem o připojení. Chyba je:

- **SqlException.Number** = 18456
- Zpráva " Přihlášení se nezdařilo pro uživatele 'WRONG_MyUserName'."

V rámci prvního pokusu o opakování může program opravit chybný pravopis a poté se pokusit o připojení.

Chcete-li tento test provést jako praktický, program rozpozná parametr runtime, který způsobí, že program:

- Dočasně přidat 18456 do seznamu chyb považovat za přechodné.
- Záměrně přidejte "WRONG_" k uživatelskému jménu.
- Po zachycovat chybu, odebrat 18456 ze seznamu.
- Odeberte z uživatelského jména WRONG_.
- Pokuste se znovu připojit a očekáváte úspěch.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry .NET SqlConnection pro opakování připojení

Pokud se klientský program připojuje k databázi SQL pomocí třídy .NET Framework **System.Data.SqlClient.SqlConnection**, použijte rozhraní .NET 4.6.1 nebo novější (nebo .NET Core), abyste mohli použít její funkci opakování připojení. Další informace o této funkci naleznete na [této webové stránce](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Při vytváření [připojovacího řetězce](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pro objekt **SqlConnection** souřadněte hodnoty mezi následujícími parametry:

- **ConnectRetryCount**&nbsp;&nbsp;: Výchozí hodnota je 1. Rozsah je 0 až 255.
- **ConnectRetryInterval**&nbsp;&nbsp;: Výchozí hodnota je 10 sekund. Rozsah je 1 až 60.
- **Časový limit**&nbsp;&nbsp;připojení : Výchozí hodnota je 15 sekund. Rozsah je 0 až 2147483647.

Konkrétně vybrané hodnoty by měly provést následující rovnost true: Časový limit připojení = ConnectRetryCount * ConnectionRetryInterval

Pokud se například počet rovná 3 a interval se rovná 10 sekund, časový výtok pouze 29 sekund neposkytne systému dostatek času pro třetí a poslední pokus o připojení: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Připojení vs. příkaz

Parametry **ConnectRetryCount** a **ConnectRetryInterval** umožňují objektu **SqlConnection** opakovat operaci připojení bez sdělit nebo obtěžovat váš program, jako je například vrácení ovládacího prvku do programu. Opakování může dojít v následujících situacích:

- volání metody mySqlConnection.Open
- volání metody mySqlConnection.Execute

Je tu jemnost. Pokud dojde k přechodné chybě při provádění *dotazu,* objekt **SqlConnection** neprovede opakování operace připojení. Určitě není opakovat dotaz. **SqlConnection** však velmi rychle zkontroluje připojení před odesláním dotazu ke spuštění. Pokud rychlá kontrola zjistí problém s připojením, **sqlconnection** opakuje operaci připojení. Pokud je opakování úspěšné, dotaz je odeslán k provedení.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Má být ConnectRetryCount kombinován s logikou opakování aplikace.

Předpokládejme, že vaše aplikace má robustní vlastní logiku opakování. Může opakovat operaci připojení čtyřikrát. Pokud přidáte **ConnectRetryInterval** a **ConnectRetryCount** =3 do připojovacího řetězce, zvýšíte počet opakování na 4 * 3 = 12 opakování. Pravděpodobně nemáte v úmyslu tak vysoký počet opakování.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Připojení k databázi SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Připojení: Připojovací řetězec

Připojovací řetězec, který je nezbytný pro připojení k databázi SQL, se mírně liší od řetězce použitého pro připojení k serveru SQL Server. Připojovací řetězec pro vaši databázi můžete zkopírovat z [webu Azure Portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Připojení: IP adresa

Je nutné nakonfigurovat databázový server SQL tak, aby přijímal komunikaci z adresy IP počítače, který je hostitelem klientského programu. Chcete-li tuto konfiguraci nastavit, upravte nastavení brány firewall na [webu Azure Portal](https://portal.azure.com/).

Pokud zapomenete nakonfigurovat adresu IP, program selže s užitečnou chybovou zprávou, která uvádí potřebnou adresu IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Další informace naleznete v [tématu Konfigurace nastavení brány firewall v databázi SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Připojení: Porty

Obvykle je třeba zajistit, aby byl otevřen pouze port 1433 pro odchozí komunikaci v počítači, který hostuje klientský program.

Pokud je například klientský program hostován v počítači se systémem Windows, můžete k otevření portu 1433 pomocí brány Windows Firewall na hostiteli.

1. Otevřete okno Ovládací panely.
2. Vyberte **možnost Všechny položky ovládacího panelu** > **Windows Firewall** > **Upřesnit nastavení** > **Odchozí pravidla** > **Akce** > **Nové pravidlo**.

Pokud je váš klientský program hostovaný na virtuálním počítači Azure ( VM), přečtěte si [porty nad rámec 1433 pro ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Základní informace o konfiguraci portů a IP adres naleznete v tématu [Brána firewall Azure SQL Database](sql-database-firewall-configure.md)firewall .

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Připojení: ADO.NET 4.6.2 nebo novější

Pokud váš program používá ADO.NET třídy jako **System.Data.SqlClient.SqlConnection** pro připojení k databázi SQL, doporučujeme použít rozhraní .NET Framework verze 4.6.2 nebo novější.

#### <a name="starting-with-adonet-462"></a>Počínaje ADO.NET 4.6.2

- Pokus o otevření připojení se okamžitě pokusí o okamžitou opakovapro databází Azure SQL, čímž se zlepší výkon cloudových aplikací.

#### <a name="starting-with-adonet-461"></a>Počínaje ADO.NET 4.6.1

- Pro sql database je vyšší spolehlivost při otevření připojení pomocí metody **SqlConnection.Open.** Open **Open** Metoda nyní zahrnuje nejlepší úsilí opakování mechanismy v reakci na přechodné chyby pro určité chyby v rámci časového limitu připojení.
- Je podporováno sdružování připojení, které zahrnuje efektivní ověření, že objekt připojení, který poskytuje vašemu programu, funguje.

Při použití objektu připojení z fondu připojení doporučujeme, aby program dočasně ukončí připojení, když není okamžitě používán. Není nákladné znovu otevřít připojení, ale je vytvořit nové připojení.

Pokud používáte ADO.NET 4.0 nebo starší, doporučujeme upgradovat na nejnovější ADO.NET. Od srpna 2018 si můžete [stáhnout ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostika

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostika: Testování, zda se nástroje mohou připojit

Pokud se programu nepodaří připojit k databázi SQL, je jednou z diagnostických možností pokusit se připojit k nástroji. V ideálním případě se nástroj připojí pomocí stejné knihovny, kterou používá program.

V libovolném počítači se systémem Windows můžete vyzkoušet tyto nástroje:

- SQL Server Management Studio (ssms.exe), který se připojuje pomocí ADO.NET
- `sqlcmd.exe`, který se připojuje pomocí [rozhraní ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Po připojení programu otestujte, zda funguje krátký dotaz SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostika: Kontrola otevřených portů

Pokud máte podezření, že pokusy o připojení se nezdaří z důvodu problémů s portem, můžete v počítači spustit nástroj, který je podřízen konfiguracím portů.

Na Linuxu mohou být užitečné následující nástroje:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Změňte ukázkovou hodnotu jako vaši IP adresu.

V systému Windows může být užitečný nástroj [PortQry.exe.](https://www.microsoft.com/download/details.aspx?id=17148) Zde je příklad spuštění, který dotazoval situaci portu na serveru SQL Database a který byl spuštěn v přenosném počítači:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostika: Protokolovat chyby

Občasný problém je někdy nejlépe diagnostikována detekce obecného vzoru v průběhu dnů nebo týdnů.

Váš klient může pomoci při diagnostice protokolováním všech chyb, se kterými se setká. Je možné, že budou možné korelovat položky protokolu s daty chyb, které sql database protokoluje sám interně.

Enterprise Library 6 (EntLib60) nabízí spravované třídy .NET, které vám pomohou s protokolováním. Další informace naleznete v [tématu 5 – stejně snadné jako pád z protokolu: Použijte blok aplikace protokolování](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostika: Zkontrolujte systémové protokoly na chyby

Zde jsou některé příkazy Transact-SQL SELECT, které dotaz udávají protokoly chyb a další informace.

| Dotaz protokolu | Popis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Zobrazení [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) nabízí informace o jednotlivých událostech, které zahrnují některé, které mohou způsobit přechodné chyby nebo selhání připojení.<br/><br/>V ideálním případě můžete korelovat **hodnoty start_time** nebo **end_time** s informacemi o tom, kdy došlo k problémům klientského programu.<br/><br/>Chcete-li tento dotaz spustit, musíte se připojit k *hlavní* databázi. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Zobrazení [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) nabízí agregované počty typů událostí pro další diagnostiku.<br/><br/>Chcete-li tento dotaz spustit, musíte se připojit k *hlavní* databázi. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostika: Hledání problémových událostí v protokolu databáze SQL

Můžete vyhledávat položky o problémových událostech v protokolu databáze SQL. Vyzkoušejte následující příkaz Transact-SQL SELECT v *hlavní* databázi:

```sql
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

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Několik vrácených řádků z sys.fn_xe_telemetry_blob_target_read_file

Následující příklad ukazuje, jak může vypadat vrácený řádek. Zobrazené hodnoty null často nejsou null v jiných řádcích.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Podniková knihovna 6

Enterprise Library 6 (EntLib60) je framework tříd .NET, který vám pomůže implementovat robustní klienty cloudových služeb, z nichž jedním je služba SQL Database. Chcete-li vyhledat témata vyhrazená pro každou oblast, ve které může EntLib60 pomoci, naleznete [v tématu Enterprise Library 6 – duben 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika opakování pro zpracování přechodných chyb je jedna oblast, ve které může entLib60 pomoci. Další informace naleznete v tématu [4 – Vytrvalost, tajemství všech triumfů: Použijte blok aplikace pro zpracování přechodných chyb](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Zdrojový kód entLib60 je k dispozici pro veřejné stažení v [centru pro stahování](https://go.microsoft.com/fwlink/p/?LinkID=290898). Společnost Microsoft nemá v plánu provádět další aktualizace funkcí nebo údržbu entlibu.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Třídy EntLib60 pro přechodné chyby a opakování

Následující třídy EntLib60 jsou zvláště užitečné pro logiku opakování. Všechny tyto třídy se nacházejí v oboru názvů nebo pod ním **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

V oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **Třída RetryPolicy**
  - **Metoda ExecuteAction**
- **Exponenciálníbackoff** třída
- **Třída SqlDatabaseTransientErrorDetectionStrategy**
- **Třída ReliableSqlConnection**
  - **Metoda ExecuteCommand**

V oboru názvů **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **Třída AlwaysTransientErrorDetectionStrategy**
- **Třída NeverTransientErrorDetectionStrategy**

Zde jsou některé odkazy na informace o EntLib60:

- Zdarma ke stažení knihy: [Příručka pro vývojáře microsoft enterprise library, 2.](https://www.microsoft.com/download/details.aspx?id=41145)
- Doporučené postupy: [Opakování obecné pokyny](../best-practices-retry-general.md) má vynikající podrobnou diskusi o logiku opakování.
- NuGet ke stažení: [Podniková knihovna - Přechodný blok aplikace pro zpracování chyb 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Blok protokolování

- Blok protokolování je vysoce flexibilní a konfigurovatelné řešení, které můžete použít k:
  - Vytvořte a uložte zprávy protokolu v široké škále umístění.
  - Kategorizovat a filtrovat zprávy.
  - Shromažďujte kontextové informace, které jsou užitečné pro ladění a trasování, jakož i pro požadavky na auditování a obecné protokolování.
- Blok protokolování abstrahuje funkci protokolování z cíle protokolu tak, aby kód aplikace byl konzistentní, bez ohledu na umístění a typ úložiště protokolování cíle.

Další informace naleznete v [tématu 5 – stejně snadné jako pád z protokolu: Použijte blok aplikace protokolování](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metoda zdrojový kód

Dále z **třídy SqlDatabaseTransientErrorDetectionStrategy** je zdrojový kód Jazyka C# pro metodu **IsTransient.** Zdrojový kód objasňuje, které chyby byly považovány za přechodné a hodné opakování od dubna 2013.

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

## <a name="next-steps"></a>Další kroky

- [Knihovny připojení pro databázi SQL a SQL Server](sql-database-libraries.md)
- [Sdružování připojení serveru SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Opakování* je apačská univerzální repromisní knihovna s licencí Apache 2.0, napsaná v Pythonu,](https://pypi.python.org/pypi/retrying) aby se zjednodušil úkol přidání chování při opakování téměř na cokoliv.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
