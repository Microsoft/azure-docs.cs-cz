---
title: Práce s přechodnými chybami
description: Naučte se řešit problémy, diagnostikovat a zabránit chybě připojení SQL nebo přechodné chybě při připojování k Azure SQL Database, spravované instanci Azure SQL a Azure synapse Analytics.
keywords: připojení SQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 9f2e755047910aefa89c2f187cda956aca608b98
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093753"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Řešení chyb přechodného připojení v SQL Database a spravované instanci SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Tento článek popisuje, jak předejít, řešit a zmírnit chyby připojení a přechodné chyby, ke kterým dojde v klientské aplikaci, když komunikuje s Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics. Naučte se konfigurovat logiku opakování, sestavit připojovací řetězec a upravit další nastavení připojení.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Přechodné chyby (přechodné chyby)

Přechodná chyba, známá také jako přechodná chyba, má základní příčinu, která se brzy vyřeší. Příležitostné příčiny přechodných chyb je, když systém Azure rychle posune hardwarové prostředky, aby lépe vyrovnal různé zatížení. Většina těchto událostí překonfigurace se dokončí za méně než 60 sekund. Během této doby rekonfigurace může dojít k problémům s připojením k databázi v SQL Database. Aplikace, které se připojují k vaší databázi, by měly být sestavené tak, aby byly tyto přechodné chyby očekávat Aby je bylo možné zpracovat, implementujte logiku opakování ve svém kódu namísto jejich zpřístupnění uživatelům jako chyby aplikace.

Pokud klientský program používá ADO.NET, váš program je o přechodné chybě vynásobený voláním **SqlException**.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Připojení vs. příkaz

Opakujte pokus o SQL Database a připojení spravované instance SQL nebo ho znovu vytvořte podle následujících pokynů:

- **Při pokusu o připojení dojde k přechodné chybě**

Po prodlevě několik sekund zkuste připojení znovu.

- **K přechodné chybě dojde během SQL Database a příkazu dotazu Managed instance SQL.**

Tento příkaz neprovádějte hned znovu. Místo toho se po zpoždění navažte navázání připojení. Pak zkuste příkaz zopakovat.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika opakování pro přechodné chyby

Klientské programy, které občas nastanou přechodnou chybu, jsou odolnější, pokud obsahují logiku opakování. Když váš program komunikuje s vaší databází v SQL Database prostřednictvím middlewaru třetí strany, požádejte dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principy pro opakování

- Pokud je chyba přechodný, zkuste otevřít připojení znovu.
- Neprovádějte přímé opakování SQL Database nebo příkazu spravované instance SQL `SELECT` , který se nezdařil, došlo k přechodné chybě. Místo toho navažte nové připojení a potom zkuste operaci zopakovat `SELECT` .
- Pokud se příkaz SQL Database nebo SQL Managed instance `UPDATE` nepovede s přechodovou chybou, vytvořte nové připojení ještě před opakováním aktualizace. Logika opakování musí zajistit, že se buď celá transakce databáze dokončí, nebo že se celá transakce vrátí zpět.

### <a name="other-considerations-for-retry"></a>Další požadavky pro opakování

- Dávkový program, který se automaticky spustí po pracovní době a skončí, než dokončí od ráno, může být velmi pacient s dlouhými časovými intervaly mezi pokusy o opakování.
- Program uživatelského rozhraní by měl mít za to, že by se měl přihlédnout po příliš dlouhé době čekání. Řešení se nesmí opakovat každých několik sekund, protože tato zásada může systém s požadavky vyplavit.

### <a name="interval-increase-between-retries"></a>Zvýšení intervalu mezi opakovanými pokusy

Doporučujeme, abyste počkali na 5 sekund před prvním opakováním. Opakuje se pokus po zpoždění kratším než 5 sekund při zahlcení cloudové služby. U každého následného opakování by prodleva měla exponenciálně růst, maximálně 60 sekund.

Diskuzi o době blokování klientů, kteří používají ADO.NET, najdete v tématu [sdružování připojení (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

Můžete také chtít nastavit maximální počet opakovaných pokusů, než se program ukončí sami.

### <a name="code-samples-with-retry-logic"></a>Ukázky kódu s logikou opakování

Příklady kódu s logikou opakování jsou k dispozici v:

- [Připojte se odolně k Azure SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Odolné připojení k Azure SQL pomocí PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testování logiky opakování

Chcete-li otestovat logiku opakování, je nutné simulovat nebo způsobit chybu, která může být opravena v době, kdy program stále běží.

#### <a name="test-by-disconnecting-from-the-network"></a>Otestujte odpojením od sítě.

Jedním ze způsobů, jak otestovat logiku opakování, je odpojit klientský počítač od sítě v době, kdy je program spuštěný. Tato chyba je:

- **SqlException. Number** = 11001
- Zpráva: "žádný takový hostitel není známý"

V rámci prvního pokusu je možné znovu připojit klientský počítač k síti a pak se pokusit připojit.

Aby byl tento test praktický, odpojte počítač od sítě před spuštěním programu. Pak program rozpozná parametr runtime, který způsobí, že program:

- Dočasně přidejte 11001 do seznamu chyb, které je třeba zvážit jako přechodné.
- Pokuste se o první připojení obvyklým způsobem.
- Po zaznamenání chyby odeberte ze seznamu 11001.
- Zobrazí zprávu, která upozorní uživatele, aby počítač vypojuje do sítě.
- Další provádění můžete pozastavit pomocí metody **Console. ReadLine** nebo dialogového okna s tlačítkem OK. Uživatel stiskne klávesu ENTER po zapojení počítače do sítě.
- Pokuste se o připojení znovu a očekávat úspěch.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Testovat po chybném pravopisu uživatelského jména při připojování

V programu může být uživatelské jméno před prvním pokusem o připojení záměrně napředné. Tato chyba je:

- **SqlException. Number** = 18456
- Zpráva: přihlášení uživatele WRONG_MyUserName se nezdařilo.

V rámci prvního pokusu může program opravit chybu a potom se pokusit o připojení.

Aby byl tento test praktický, váš program rozpozná parametr modulu runtime, který způsobí, že program provede následující:

- Dočasně přidejte 18456 do seznamu chyb, které je třeba zvážit jako přechodné.
- Účelně přidejte ' WRONG_ ' k uživatelskému jménu.
- Po zaznamenání chyby odeberte ze seznamu 18456.
- Z uživatelského jména odeberte ' WRONG_ '.
- Pokuste se o připojení znovu a očekávat úspěch.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry .NET SqlConnection pro opakování připojení

Pokud se klientský program připojuje k vaší databázi v SQL Database pomocí .NET Framework třídy **System. data. SqlClient. SqlConnection**, použijte .NET 4.6.1 nebo novější (nebo .NET Core), abyste mohli použít funkci opakování připojení. Další informace o této funkci naleznete v tématu [vlastnost SqlConnection. ConnectionString](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Při sestavování [připojovacího řetězce](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) pro objekt **SqlConnection** koordinovat hodnoty mezi následujícími parametry:

- **Atributu ConnectRetryCount**: &nbsp; &nbsp; Výchozí hodnota je 1. Rozsah je od 0 do 255.
- **Atributu ConnectRetryInterval**: &nbsp; &nbsp; Výchozí hodnota je 10 sekund. Rozsah je 1 až 60.
- **Časový limit připojení**: &nbsp; &nbsp; Výchozí hodnota je 15 sekund. Rozsah je od 0 do 2147483647.

Konkrétně by vaše zvolené hodnoty měly platit z následujících rovností: časový limit připojení = atributu ConnectRetryCount * ConnectionRetryInterval

Pokud se například počet rovná 3 a interval se rovná 10 sekund, časový limit jenom 29 sekund neposkytne systému dostatek času pro svůj třetí a konečný pokus o připojení: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Připojení vs. příkaz

Parametry **atributu ConnectRetryCount** a **atributu ConnectRetryInterval** umožňují, aby se váš objekt **SqlConnection** znovu opakoval, aniž by bylo nutné sdělit nebo přestane svůj program, jako je například vrácení řízení vašemu programu. Opakování se může vyskytnout v následujících situacích:

- SqlConnection. Open – volání metody
- SqlConnection.Exevolání metody roztomilá

Existuje Subtlety. Pokud při provádění *dotazu* dojde k přechodné chybě, váš objekt **SqlConnection** neopakuje operaci připojení. V takovém případě to neopakuje dotaz. Nicméně **SqlConnection** velmi rychle zkontroluje připojení před odesláním dotazu ke spuštění. Pokud rychlá kontroly zjistí problém s připojením, **SqlConnection** opakuje operaci připojení. Pokud je opakování úspěšné, odešle se dotaz k provedení.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Má atributu ConnectRetryCount být kombinován s logikou opakování aplikace

Předpokládejme, že vaše aplikace má robustní vlastní logiku opakování. Je možné, že operaci připojení čtyřikrát znovu vyprší. Pokud do připojovacího řetězce přidáte **atributu ConnectRetryInterval** a **atributu ConnectRetryCount** = 3, zvýší se počet opakování na 4 * 3 = 12 opakování. Je možné, že nebudete chtít mít takový vysoký počet opakovaných pokusů.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Připojení k databázi v SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Připojení: připojovací řetězec

Připojovací řetězec, který je nezbytný pro připojení k vaší databázi, se mírně liší od řetězce použitého pro připojení k SQL Server. Připojovací řetězec pro databázi můžete zkopírovat z [Azure Portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Připojení: IP adresa

Musíte nakonfigurovat SQL Database pro příjem komunikace z IP adresy počítače, který hostuje klientský program. Chcete-li nastavit tuto konfiguraci, upravte nastavení brány firewall pomocí [Azure Portal](https://portal.azure.com/).

Pokud zapomenete nakonfigurovat IP adresu, váš program se nezdařil s užitečnou chybovou zprávou, která uvádí nezbytnou IP adresu.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Další informace najdete v tématu [Konfigurace nastavení brány firewall v SQL Database](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Připojení: porty

Obvykle je nutné zajistit, aby byl pro odchozí komunikaci v počítači, který je hostitelem klientského programu, otevřen pouze port 1433.

Pokud je například klientský program hostovaný v počítači se systémem Windows, můžete k otevření portu 1433 použít bránu Windows Firewall na hostiteli.

1. Otevřete okno Ovládací panely.
2. Vybrat **všechny položky Ovládacích panelů**  >  nastavení nastavení odchozí pravidla **brány Windows Firewall**  >  **Upřesnit nastavení**  >    >  **Akce**  >  **nové pravidlo**.

Pokud je váš klientský program hostovaný na virtuálním počítači Azure (VM), přečtěte si [porty, které přesahují 1433 pro ADO.NET 4,5 a SQL Database](adonet-v12-develop-direct-route-ports.md).

Základní informace o konfiguraci portů a IP adres ve vaší databázi najdete v tématu [Azure SQL Database firewall](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Připojení: ADO.NET 4.6.2 nebo novější

Pokud váš program používá ADO.NET třídy, jako je **System. data. SqlClient. SqlConnection** , abyste se mohli připojit k SQL Database, doporučujeme používat .NET Framework verze 4.6.2 nebo novější.

#### <a name="starting-with-adonet-462"></a>Počínaje ADO.NET 4.6.2

- Pokus o otevření připojení je okamžitě opakován pro Azure SQL, což vylepšuje výkon aplikací s podporou cloudu.

#### <a name="starting-with-adonet-461"></a>Od ADO.NET 4.6.1

- V případě SQL Database se zlepší spolehlivost při otevření připojení pomocí metody **SqlConnection. Open** . **Open** Method nyní zahrnuje osvědčené mechanismy opakování v reakci na přechodná selhání v průběhu časového limitu připojení.
- Sdružování připojení je podporováno, což zahrnuje efektivní ověření, že objekt připojení, který zajišťuje váš program, funguje.

Když použijete objekt připojení z fondu připojení, doporučujeme, aby program dočasně zavírá připojení, když se hned nepoužívá. Opětovné otevření připojení není nákladné, ale je potřeba vytvořit nové připojení.

Pokud používáte ADO.NET 4,0 nebo starší, doporučujeme upgradovat na nejnovější ADO.NET. Od srpna 2018 si můžete [stáhnout ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostika

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostika: testování, zda se mohou nástroje připojit

Pokud se programu nemůžete připojit k vaší databázi v SQL Database, jednou z možností diagnostiky je pokus o připojení pomocí programového programu. V ideálním případě se nástroj připojuje pomocí stejné knihovny, kterou program používá.

Na jakémkoli počítači s Windows můžete vyzkoušet tyto nástroje:

- SQL Server Management Studio (ssms.exe), který se připojuje pomocí ADO.NET
- `sqlcmd.exe`, který se připojuje pomocí [rozhraní ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)

Po připojení programu otestujte, jestli funguje krátký dotaz SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostika: Ověřte otevřené porty.

Pokud se domníváte, že pokusy o připojení selžou kvůli problémům s porty, můžete na svém počítači spustit nástroj, který hlásí konfigurace portů.

V systému Linux mohou být užitečné následující nástroje:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Změňte ukázkovou hodnotu na vaši IP adresu.

V systému Windows může být užitečný nástroj [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) . Tady je příklad spuštění s dotazem na situaci, kdy se port nachází v databázi v SQL Database a který byl spuštěný na přenosném počítači:

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

### <a name="diagnostics-log-your-errors"></a>Diagnostika: protokolování chyb

Občasné problémy se někdy nejlépe diagnostikují detekcí obecného vzoru během dnů nebo týdnů.

Váš klient může pomoci diagnostikovat tím, že zaznamená všechny chyby, ke kterým dojde. Je možné, že budete moci korelaci položek protokolu s daty o chybách, které se samy SQL Database protokolují interně.

Enterprise Library 6 (EntLib60) nabízí pro pomoc s protokolováním spravované třídy .NET. Další informace najdete v části [5 – stejně snadné jako v případě, že se nachází v protokolu: použití bloku aplikace protokolování](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostika: Vyhledejte chyby v systémových protokolech.

Tady jsou některé příkazy SELECT jazyka Transact-SQL, které dotazují protokoly chyb a další informace.

| Dotaz na protokol | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Zobrazení [Sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) nabízí informace o jednotlivých událostech, které obsahují některé, které mohou způsobit přechodné chyby nebo selhání připojení.<br/><br/>V ideálním případě můžete sladit **start_time** nebo **end_time** hodnoty informacemi o tom, kdy došlo k potížím s klientským programem.<br/><br/>Chcete-li spustit tento dotaz, je nutné se připojit k *Hlavní* databázi. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Zobrazení [Sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database) nabízí agregované počty typů událostí pro další diagnostiku.<br/><br/>Chcete-li spustit tento dotaz, je nutné se připojit k *Hlavní* databázi. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostika: hledání událostí problému v protokolu SQL Database

Můžete vyhledat položky o událostech problémů v protokolu SQL Database. V *Hlavní* databázi zkuste použít následující příkaz Transact-SQL SELECT:

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

Následující příklad ukazuje, jak vrácený řádek může vypadat. Zobrazené hodnoty null nejsou v jiných řádcích často null.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) je architektura tříd .NET, která pomáhá implementovat robustní klienty Cloud Services, z nichž jeden je SQL Database. Témata vyhrazená pro jednotlivé oblasti, ve kterých může EntLib60 pomoct, najdete v tématu [Enterprise Library 6. dubna 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10)).

Logika opakování pro zpracování přechodných chyb je jednou z oblastí, ve které může EntLib60 pomoct. Další informace najdete v tématu [4 – Perseverance, tajný kód všech triumphs: použijte blok aplikace pro zpracování přechodného selhání](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> Zdrojový kód pro EntLib60 je k dispozici pro veřejné stažení ze [služby Stažení softwaru](https://github.com/MicrosoftArchive/enterprise-library). Microsoft nemá žádné plány na další aktualizace funkcí ani aktualizace údržby EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 třídy pro přechodné chyby a zkuste to znovu.

Následující třídy EntLib60 jsou zvláště užitečné pro logiku opakování. Všechny tyto třídy se nacházejí v oboru názvů **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling** nebo pod ním.

V oboru názvů **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**:

- **RetryPolicy** – třída
  - Metoda **ExecuteAction**
- **ExponentialBackoff** – třída
- **SqlDatabaseTransientErrorDetectionStrategy** – třída
- **ReliableSqlConnection** – třída
  - Metoda **ExecuteCommand**

V oboru názvů **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling. TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** – třída
- **NeverTransientErrorDetectionStrategy** – třída

Tady jsou některé odkazy na informace o EntLib60:

- Stažení bezplatné knihy: [Příručka pro vývojáře k Microsoft Enterprise Library, druhá edice](https://www.microsoft.com/download/details.aspx?id=41145).
- Osvědčené postupy: Podrobnější [pokyny pro opakování](/azure/architecture/best-practices/transient-faults) mají vynikající podrobná diskuzi o logice opakování.
- Stažení NuGet: [Podniková knihovna – blok aplikace pro zpracování přechodných chyb 6,0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: blok protokolování

- Blok protokolování je vysoce flexibilní a konfigurovatelné řešení, které můžete použít k těmto akcím:
  - Vytvářejte a neukládejte zprávy protokolu v nejrůznějších umístěních.
  - Kategorizujte a filtrujte zprávy.
  - Shromažďování kontextových informací, které jsou užitečné pro ladění a trasování, a také pro auditování a obecné požadavky na protokolování.
- Blok protokolování vyabstrakce protokolovací funkce z cílového umístění protokolu, takže kód aplikace je konzistentní bez ohledu na umístění a typ cílového úložiště protokolování.

Další informace najdete v části [5 – stejně snadné jako v případě, že se nachází v protokolu: použití bloku aplikace protokolování](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Zdrojový kód EntLib60-přechodný způsob

Dále z třídy **SqlDatabaseTransientErrorDetectionStrategy** je zdrojový kód C# pro metodu s příznakem "- **přechodné** ". Zdrojový kód upřesňuje, které chyby byly považovány za přechodné a důvěryhodného opakování, od dubna 2013.

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

- [Knihovny připojení pro SQL Database a SQL Server](connect-query-content-reference-guide.md#libraries)
- [Sdružování připojení (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Opakovaným pokusem* je knihovna Apache 2,0 s licencí pro obecné účely, která je napsaná v Pythonu,](https://pypi.python.org/pypi/retrying) aby zjednodušila úlohu přidávání chování opakování do stejného množství.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php