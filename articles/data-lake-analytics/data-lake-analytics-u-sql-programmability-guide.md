---
title: Průvodce programovatelností u-SQL pro Azure Data Lake
description: Seznamte se s přehledem U-SQL a programovatelností systému souborů UDF v Azure Data Lake Analytics, abyste mohli vytvořit dobrý skript USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510858"
---
# <a name="u-sql-programmability-guide-overview"></a>Přehled Průvodce programovatelnosti U-SQL

U-SQL je dotazovací jazyk, který je navržený pro velké objemy dat. Jedna z jedinečných funkcí U-SQL je kombinací deklarativního jazyka podobného SQL s rozšiřitelnou a programovatelností poskytovanou jazykem C#. V tomto průvodci se zaměříme na rozšiřitelnost a programovatelnost jazyka U-SQL, který je povolený v jazyce C#.

## <a name="requirements"></a>Požadavky

Stáhněte a nainstalujte [nástroje Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Začínáme s U-SQL  

Podívejte se na následující skript U-SQL:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Tento skript definuje dvě sady řádků: `@a` a `@results` . Sada řádků `@results` je definována z `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy a výrazy jazyka C# ve skriptu U-SQL

Výraz U-SQL je výraz jazyka C# v kombinaci s logickými operacemi U-SQL, jako jsou, `AND` `OR` a `NOT` . Výrazy U-SQL se dají použít s příkazy SELECT, EXTRACT, WHERE, GROUP BY a DECLARE. Například následující skript analyzuje řetězec jako hodnotu data a času.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Následující fragment kódu analyzuje řetězec jako hodnotu DateTime v příkazu DECLARE.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Použití výrazů jazyka C# pro převody datových typů

Následující příklad ukazuje, jak lze provést převod dat typu DateTime pomocí výrazů jazyka C#. V tomto konkrétním scénáři jsou řetězcová data typu DateTime převedena na standardní datum a čas půlnoci 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Pro dnešní datum používat výrazy jazyka C#

Aby bylo možné načíst dnešní datum, můžeme použít následující výraz jazyka C#: `DateTime.Now.ToString("M/d/yyyy")`

Tady je příklad použití tohoto výrazu ve skriptu:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Použití sestavení .NET

Model rozšiřitelnosti U-SQL spoléhá na silně schopnost přidat vlastní kód ze sestavení .NET. 

### <a name="register-a-net-assembly"></a>Registrace sestavení .NET

Použijte `CREATE ASSEMBLY` příkaz k umístění sestavení .NET do u-SQL Database. Poté mohou skripty U-SQL použít tato sestavení pomocí `REFERENCE ASSEMBLY` příkazu. 

Následující kód ukazuje, jak zaregistrovat sestavení:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Následující kód ukazuje, jak odkazovat na sestavení:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Podrobnější informace najdete v [pokynech k registraci sestavení](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) , která jsou podrobněji popsána v tomto tématu.


### <a name="use-assembly-versioning"></a>Použití správy verzí sestavení
V současné době používá U-SQL .NET Framework 4.7.2 verze. Ujistěte se, že vaše vlastní sestavení jsou kompatibilní s touto verzí modulu runtime.

Jak bylo zmíněno dříve, U-SQL spouští kód ve formátu 64-bit (x64). Proto se ujistěte, že je váš kód zkompilován pro běh na platformě x64. V opačném případě se zobrazí chyba nesprávného formátu v předchozím příkladu.

Každá nahraná knihovna DLL sestavení a soubor prostředků, jako je například jiný modul runtime, nativní sestavení nebo konfigurační soubor, mohou být nejvýše 400 MB. Celková velikost nasazených prostředků, buď prostřednictvím nasazení prostředků, nebo prostřednictvím odkazů na sestavení a jejich dalších souborů, nesmí překročit 3 GB.

Nakonec si všimněte, že každá databáze U-SQL může obsahovat pouze jednu verzi daného sestavení. Například pokud potřebujete obě verze 7 i verze 8 knihovny NewtonSoft Json.NET, je nutné je zaregistrovat ve dvou různých databázích. Kromě toho každý skript může odkazovat pouze na jednu verzi dané knihovny DLL sestavení. V tomto ohledu postup U-SQL následuje jako správa sestavení v jazyce C# a sémantika správy verzí.

## <a name="use-user-defined-functions-udf"></a>Použití uživatelem definovaných funkcí: UDF
Uživatelsky definované funkce U-SQL nebo UDF jsou programovací rutiny, které přijímají parametry, provádějí akci (například složitý výpočet) a vracejí výsledek této akce jako hodnotu. Návratová hodnota UDF může být jenom jedna skalární hodnota. U-SQL UDF se dá volat v základním skriptu U-SQL jako jakékoli jiné skalární funkce C#.

Doporučujeme inicializovat uživatelsky definované funkce U-SQL jako **veřejné** a **statické**.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Nejdřív se podíváme na jednoduchý příklad vytvoření systému souborů UDF.

V tomto scénáři použití je potřeba určit fiskální období, včetně fiskálního čtvrtletí a fiskálního měsíce prvního přihlášení pro konkrétního uživatele. První fiskální měsíc roku v našem scénáři je červen.

Pro výpočet fiskálního období zavádíme následující funkci jazyka C#:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Jednoduše vypočítá fiskální měsíc a čtvrtletí a vrátí hodnotu řetězce. V červnu první měsíc prvního fiskálního čtvrtletí používáme "Q1: P1". Pro červenec používáme "Q1: P2" atd.

Toto je běžná funkce jazyka C#, kterou budeme používat v našem projektu U-SQL.

Tady je postup, jak vypadá oddíl Code-na pozadí v tomto scénáři:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Nyní budeme volat tuto funkci ze základního skriptu U-SQL. Pro tuto operaci je nutné zadat plně kvalifikovaný název funkce, včetně oboru názvů, který v tomto případě je obor názvů. Class. Function (parametr).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Následuje skutečný základní skript U-SQL:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Toto je výstupní soubor provádění skriptu:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Tento příklad ukazuje jednoduché použití vloženého systému souborů UDF v U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachovat stav mezi voláními systému souborů UDF
Objekty programovatelnost U-SQL C# můžou být propracovanější a využívají interaktivitu prostřednictvím globálních proměnných kódu na pozadí. Pojďme se podívat na následující scénář obchodních případů použití.

Ve velkých organizacích můžou uživatelé přepínat mezi různými variantami interních aplikací. Můžou sem patřit Microsoft Dynamics CRM, Power BI a tak dále. Zákazníci můžou chtít použít analýzu telemetrie, jak uživatelé přepínají mezi různými aplikacemi, co jsou trendy využití a tak dále. Cílem pro firmu je optimalizace využití aplikace. Můžou taky chtít kombinovat různé aplikace nebo konkrétní rutiny přihlašování.

Abychom dosáhli tohoto cíle, musíme určit ID relací a prodlevu mezi poslední relací, ke které došlo.

Musíme najít předchozí přihlášení a pak přiřadit toto přihlášení ke všem relacím, které se generují do stejné aplikace. První výzvou je, že základní skript U-SQL neumožňuje použít výpočty nad již počítanými sloupci s funkcí LAG. Druhá výzva je, že musíme pro všechny relace v rámci stejného časového období zachovat konkrétní relaci.

Chcete-li tento problém vyřešit, používáme globální proměnnou v části kódu na pozadí: `static public string globalSession;` .

Tato globální proměnná je použita na celou sadu řádků během provádění skriptu.

Tady je část s kódem na pozadí našeho programu U-SQL:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Tento příklad ukazuje globální proměnnou `static public string globalSession;` použitou uvnitř `getStampUserSession` funkce a při každém změně parametru relace probíhá opětovná inicializace.

Základní skript U-SQL je následující:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Funkce `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` je volána zde během výpočtu druhé paměťové sady řádků. Předá `UserSessionTimestamp` sloupec a vrátí hodnotu, dokud `UserSessionTimestamp` se nezmění.

Výstupní soubor je následující:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Tento příklad ukazuje složitější scénář použití, ve kterém používáme globální proměnnou v oddílu kódu na pozadí, který se používá pro celou sadu řádků paměti.

## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Průvodce programovatelností U-SQL – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)