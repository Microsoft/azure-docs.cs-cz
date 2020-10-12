---
title: Průvodce programovatelností u-SQL pro Azure Data Lake
description: Seznamte se se sadou služeb v Azure Data Lake Analytics, které vám umožní vytvořit cloudovou platformu pro velké objemy dat.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 1c22aa9fb91b0a86704b95586afc1779023e85b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288950"
---
# <a name="u-sql-programmability-guide"></a>Průvodce programovatelností U-SQL

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

Podrobnější informace najdete v [pokynech k registraci sestavení](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , která jsou podrobněji popsána v tomto tématu.


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

## <a name="use-user-defined-types-udt"></a>Použít uživatelsky definované typy: UDT
Uživatelsky definované typy nebo UDT jsou další funkcí programovatelnosti U-SQL. UDT typu U-SQL funguje jako běžný uživatelsky definovaný typ jazyka C#. C# je jazyk silného typu, který umožňuje použití vestavěných a vlastních uživatelsky definovaných typů.

U-SQL nemůže implicitně serializovat nebo deserializovat libovolný UDT při předávání UDT mezi vrcholy v sadách řádků. To znamená, že uživatel musí zadat explicitní formátovací modul pomocí rozhraní IFormatter. To poskytuje U-SQL s metodami serializace a deserializace pro UDT.

> [!NOTE]
> Vestavěné extraktory U-SQL a modul pro výstupy aktuálně nemůžou serializovat nebo deserializovat data UDT do souborů i ze IFormatter sady. Takže pokud píšete data UDT do souboru s příkazem OUTPUT nebo je přečtete pomocí extraktoru, je nutné jej předat jako řetězec nebo pole bajtů. Pak zavoláte kód serializace a deserializace (tj. metodu ToString () objektu UDT explicitně. Uživatelem definované extrakce a výstupy můžou na druhé straně číst a zapisovat UDT.

Pokud se pokusíte použít UDT v EXTRAKTORu nebo v předvýrobním programu (z předchozího výběru), jak je znázorněno zde:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Zobrazí se následující chyba:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Aby bylo možné pracovat se systémem UDT v modulu pro výstup, je nutné jej serializovat na řetězec pomocí metody ToString () nebo vytvořit vlastní modul pro výstup.

UDT se v tuto chvíli nedá použít v GROUP BY. Pokud je v GROUP BY použit parametr UDT, je vyvolána následující chyba:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Abychom definovali UDT, musíme:

* Přidejte následující obory názvů:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Přidat `Microsoft.Analytics.Interfaces` , který je požadován pro rozhraní UDT. Kromě toho `System.IO` může být potřeba definovat rozhraní IFormatter.

* Definujte použitý typ s atributem SqlUserDefinedType.

**SqlUserDefinedType** se používá k označení definice typu v sestavení jako uživatelem definovaný typ (UDT) v U-SQL. Vlastnosti atributu odpovídají fyzickým vlastnostem UDT. Tuto třídu nelze zdědit.

SqlUserDefinedType je vyžadovaný atribut pro definici UDT.

Konstruktor třídy:  

* SqlUserDefinedTypeAttribute (formátování typu)

* Typ formátování: povinný parametr pro definování formátovacího modulu UDT – konkrétně typ `IFormatter` rozhraní musí být předán zde.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typický parametr UDT také vyžaduje definici rozhraní IFormatter, jak je znázorněno v následujícím příkladu:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Rozhraní serializace a deserializace graf objektu s kořenovým typem \<typeparamref name="T"> .

\<typeparam name="T">Kořenový typ pro graf objektu pro serializaci a deserializaci.

* **Deserializace**: deserializovat data v zadaném datovém proudu a rekonstruovat graf objektů.

* **Serializace**: serializace objektu nebo grafu objektů s daným kořenem k poskytnutému datovému proudu.

`MyType` instance: instance typu.  
`IColumnWriter` zapisovač/ `IColumnReader` Čtenář: podkladový datový proud sloupce.  
`ISerializationContext` Context: Enum, který definuje sadu příznaků, které určují zdrojový nebo cílový kontext pro datový proud během serializace.

* **Intermediate**: Určuje, že zdrojový nebo cílový kontext není trvalé úložiště.

* **Persistence**: Určuje, zda je zdrojový nebo cílový kontext trvalým úložištěm.

Jako běžný typ C# může definice UDT U-SQL zahrnovat přepsání pro operátory, jako je +/= =/! =. Může také zahrnovat statické metody. Pokud například použijete tento typ UDT jako parametr pro agregační funkci U-SQL MIN, je nutné definovat < operátor override.

V předchozí části tohoto průvodce jsme ukázali příklad identifikace fiskálního období z konkrétního data ve formátu `Qn:Pn (Q1:P10)` . Následující příklad ukazuje, jak definovat vlastní typ pro hodnoty fiskálního období.

Následuje příklad oddílu kódu na pozadí s vlastním rozhraním UDT a IFormatter:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Definovaný typ obsahuje dvě čísla: čtvrtletí a měsíc. Operátory `==/!=/>/<` a statická metoda `ToString()` jsou zde definovány.

Jak bylo zmíněno dříve, je možné použít UDT ve výrazech SELECT, ale nelze je použít v nástroji pro výstup/EXTRAKCe bez vlastní serializace. Buď musí být serializován jako řetězec s použitím `ToString()` nebo s vlastním výstupem/extrakcí.

Teď se podíváme na použití UDT. V části s kódem na pozadí jsme změnili naši funkci GetFiscalPeriod na následující:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Jak vidíte, vrátí hodnotu našeho typu FiscalPeriod.

Zde uvádíme příklad, jak ho dál používat v základním skriptu U-SQL. Tento příklad ukazuje různé formy vyvolání UDT ze skriptu U-SQL.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Tady je příklad úplné části s kódem na pozadí:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Použít uživatelsky definované agregace: UDAGG
Uživatelsky definované agregace jsou jakékoli funkce související s agregací, které nejsou dodány předem pomocí U-SQL. Příkladem může být agregace pro provádění vlastních matematických výpočtů, zřetězení řetězců, manipulace s řetězci a tak dále.

Uživatelsky definovaná definice základní třídy agregace je následující:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** označuje, že typ by měl být registrován jako uživatelsky definovaná agregace. Tuto třídu nelze zdědit.

Atribut SqlUserDefinedType je pro definici UDAGG **volitelný** .


Základní třída umožňuje předat tři abstraktní parametry: dva jako vstupní parametry a jeden jako výsledek. Datové typy jsou proměnné a měly by být definovány během dědičnosti třídy.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** se vyvolá jednou pro každou skupinu během výpočtu. Poskytuje inicializační rutinu pro každou skupinu agregace.  
* **Shromažďování** je provedeno jednou pro každou hodnotu. Poskytuje hlavní funkce pro agregační algoritmus. Dá se použít k agregaci hodnot s různými datovými typy, které jsou definovány během dědičnosti třídy. Může přijmout dva parametry datových typů proměnných.
* **Ukončení** se provádí jednou na agregační skupinu na konci zpracování, aby se vytvářely výstup výsledku pro každou skupinu.

Chcete-li deklarovat správné vstupní a výstupní datové typy, použijte definici třídy následujícím způsobem:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: první parametr, který se má nashromáždit
* T2: druhý parametr ke shromáždění
* TResult: návratový typ ukončení

Například:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

nebo

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Použití UDAGG v U-SQL
Chcete-li použít UDAGG, nejprve ho definujte v kódu na pozadí nebo na něj odkázat z existující programovatelnosti knihovny DLL, jak je popsáno výše.

Pak použijte následující syntaxi:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Tady je příklad UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

A základní skript U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

V tomto scénáři použití jsou pro konkrétní uživatele zřetězeni identifikátory GUID tříd.

## <a name="use-user-defined-objects-udo"></a>Použít uživatelsky definované objekty: UDO
U-SQL umožňuje definovat vlastní programovatelné objekty, které se nazývají uživatelsky definované objekty nebo UDO.

Níže je seznam UDO v U-SQL:

* Uživatelem definované extraktory
    * Extrahovat řádek po řádku
    * Slouží k implementaci extrakce dat z vlastních strukturovaných souborů.

* Uživatelem definované výstupy
    * Výstup řádku podle řádku
    * Používá se pro výstup vlastních datových typů nebo vlastních formátů souborů.

* Uživatelsky definované procesory
    * Vezměte jeden řádek a vytvořte jeden řádek.
    * Slouží ke snížení počtu sloupců nebo vytvoření nových sloupců s hodnotami odvozenými z existující sady sloupců.

* Uživatelsky definované appliers
    * Vezměte jeden řádek a vytvořte 0 až n řádků.
    * Používá se s VNĚJŠÍm/PŘÍČNým použitím

* Uživatelsky definované kombinace
    * Kombinuje sady řádků – uživatelsky definované spojení

* Uživatelsky definované reduktorů
    * Vezměte n řádků a vytvořte jeden řádek.
    * Slouží ke snížení počtu řádků.

UDO se obvykle explicitně volá ve skriptu U-SQL jako součást následujících příkazů U-SQL:

* EXTRACT
* VÝKONEM
* PŘIHLÁŠENÍ
* SPOJEN
* ÚROVNĚ

> [!NOTE]  
> UDO je omezené na využívání paměti 0,5 GB.  Toto omezení paměti se nevztahuje na místní spuštění.

## <a name="use-user-defined-extractors"></a>Použít uživatelem definované extraktory
U-SQL umožňuje importovat externí data pomocí příkazu EXTRACT. Příkaz k EXTRAKCi může používat vestavěné extraktory UDO:  

* *Extraktory. text ()*: poskytuje extrakci z textových souborů s oddělovači různých kódování.

* *Extractors.Csv ()*: poskytuje extrakci ze souborů hodnot oddělených čárkami (CSV) různých kódování.

* *Extraktory. TSV ()*: poskytuje extrakci ze souborů hodnot oddělených tabulátory (TSV) různých kódování.

Může být užitečné pro vývoj vlastního extraktoru. To může být užitečné při importu dat, pokud chceme udělat některý z následujících úkolů:

* Upravte vstupní data rozdělením sloupců a úpravou jednotlivých hodnot. Funkce procesoru je lepší pro kombinování sloupců.
* Analyzujte nestrukturovaná data, jako jsou webové stránky a e-maily, nebo částečně nestrukturovaná data, jako je XML/JSON.
* Analyzovat data v nepodporovaném kódování.

Pro definování uživatelsky definovaného extraktoru nebo OUČIT musíme vytvořit `IExtractor` rozhraní. Všechny vstupní parametry pro extraktor, jako jsou například oddělovače sloupců nebo řádků a kódování, musí být definovány v konstruktoru třídy. `IExtractor`Rozhraní by mělo také obsahovat definici pro přepsání následujícím `IEnumerable<IRow>` způsobem:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Atribut **SqlUserDefinedExtractor** označuje, že typ by měl být registrován jako uživatelem definovaný extraktor. Tuto třídu nelze zdědit.

SqlUserDefinedExtractor je volitelný atribut pro definici OUČIT. Slouží k definování vlastnosti AtomicFileProcessing pro objekt OUČIT.

* bool AtomicFileProcessing   

* **true** = označuje, že tento extraktor vyžaduje atomické vstupní soubory (JSON, XML,...)
* **false** = znamená, že tento extraktor může pracovat s rozdělenými a distribuovanými soubory (CSV, SEQ,...)

Hlavními objekty programovatelnosti OUČIT jsou **vstupy** a **výstupy**. Vstupní objekt se používá k vytvoření výčtu vstupních dat jako `IUnstructuredReader` . Výstupní objekt se používá k nastavení výstupních dat v důsledku aktivity extraktoru.

Vstupní data jsou k dispozici prostřednictvím `System.IO.Stream` a `System.IO.StreamReader` .

Pro výčet vstupních sloupců nejprve rozdělí vstupní datový proud pomocí oddělovače řádků.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Pak dále rozdělte vstupní řádek na části sloupce.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

K nastavení výstupních dat používáme `output.Set` metodu.

Je důležité pochopit, že vlastní extraktor pouze výstupuje sloupce a hodnoty, které jsou definovány s výstupem. Nastavte volání metody.

```csharp
output.Set<string>(count, part);
```

Skutečný výstup extraktoru se aktivuje voláním `yield return output.AsReadOnly();` .

Následuje příklad extrakce:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

V tomto scénáři použití extraktor znovu vygeneruje GUID pro sloupec "GUID" a převede hodnoty sloupce "uživatel" na velká písmena. Vlastní extraktory můžou díky analýze vstupních dat a manipulaci s nimi způsobovat složitější výsledky.

Následuje základní skript U-SQL, který používá vlastní extraktor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Použití uživatelsky definovaných koncových výstupu
Uživatelem definovaný modul pro výstup je jiný UDO U-SQL, který umožňuje rozšířené integrované funkce U-SQL. Podobně jako u extraktoru existuje několik vestavěných výstupů.

* Výstupy *. text ()*: zapisuje data do textových souborů s oddělovači různých kódování.
* *Outputters.Csv ()*: zapisuje data do souborů hodnot oddělených čárkami (CSV) různých kódování.
* Výstupy *. TSV ()*: zapisuje data do souborů hodnot oddělených tabulátory (TSV) různých kódování.

Vlastní Provisioning umožňuje zapisovat data do vlastního definovaného formátu. To může být užitečné pro následující úlohy:

* Zápis dat do částečně strukturovaných nebo nestrukturovaných souborů.
* Zápis dat s nepodporovanými kódováními.
* Úprava výstupních dat nebo přidávání vlastních atributů.

Pro definování uživatelsky definovaného výstupu je potřeba vytvořit `IOutputter` rozhraní.

Následující je implementace základní `IOutputter` třídy:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Všechny vstupní parametry, jako jsou například oddělovače sloupců nebo řádků, kódování a tak dále, musí být definovány v konstruktoru třídy. `IOutputter`Rozhraní by mělo obsahovat také definici pro `void Output` přepsání. Atribut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` může být volitelně nastaven pro zpracování atomických souborů. Další informace najdete v následujících podrobnostech.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` je volána pro každý vstupní řádek. Vrátí `IUnstructuredWriter output` sadu řádků.
* Třída konstruktoru se používá k předání parametrů do uživatelsky definovaného výstupu.
* `Close` slouží k volitelnému přepsání pro vydání nákladného stavu nebo určení, kdy byl poslední řádek napsán.

Atribut **SqlUserDefinedOutputter** označuje, že typ by měl být zaregistrován jako uživatelsky definovaný modul pro registraci. Tuto třídu nelze zdědit.

SqlUserDefinedOutputter je volitelný atribut pro uživatelsky definovanou definici výstupu. Slouží k definování vlastnosti AtomicFileProcessing.

* bool AtomicFileProcessing   

* **true** = znamená, že tento dokument vyžaduje atomické výstupní soubory (JSON, XML,...).
* **false** = znamená, že tento soubor k tomuto výstupu může pracovat s rozdělenými a distribuovanými soubory (CSV, SEQ,...)

Hlavními objekty programovatelnosti jsou **řádek** a **výstup**. Objekt **Row** se používá k zobrazení výčtu výstupních dat jako `IRow` rozhraní. **Výstup** se používá k nastavení výstupních dat do cílového souboru.

Výstupní data jsou k dispozici prostřednictvím `IRow` rozhraní. Výstupní data jsou úspěšně předána řádku.

Jednotlivé hodnoty jsou vyčísleny voláním metody Get rozhraní IRow:

```csharp
row.Get<string>("column_name")
```

Jednotlivé názvy sloupců lze určit voláním `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Tento přístup umožňuje vytvořit flexibilní výstup pro jakékoli schéma metadat.

Výstupní data jsou zapsána do souboru pomocí `System.IO.StreamWriter` . Parametr Stream je nastaven `output.BaseStream` jako součást `IUnstructuredWriter output` .

Všimněte si, že pro každou iteraci řádku je důležité vyprázdnit vyrovnávací paměť dat do souboru. Kromě toho `StreamWriter` musí být objekt použit s povoleným atributem na jedno použití (výchozí) a s klíčovým slovem **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

V opačném případě volání metody Flush () explicitně po každé iteraci. V následujícím příkladu to ukážeme.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Nastavení hlaviček a zápatí pro uživatelem definovaný výstup
Chcete-li nastavit hlavičku, použijte tok spuštění s jednou iterací.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Kód v prvním `if (isHeaderRow)` bloku je proveden pouze jednou.

Pro zápatí použijte odkaz na instanci `System.IO.Stream` objektu ( `output.BaseStream` ). Zapsat zápatí v metodě Close () `IOutputter` rozhraní.  (Další informace najdete v následujícím příkladu.)

Následuje příklad uživatelsky definovaného výstupu:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

A základní skript U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Toto je výstup HTML, který vytvoří soubor HTML s daty tabulky.

### <a name="call-outputter-from-u-sql-base-script"></a>Volání výstupu z základního skriptu U-SQL
Chcete-li volat vlastní modul pro výstup z základního skriptu U-SQL, je nutné vytvořit novou instanci objektu provýstupu.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby nedošlo k vytváření instancí objektu v základním skriptu, můžeme vytvořit obálku funkce, jak je znázorněno v našem příkladu:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

V takovém případě původní volání vypadá takto:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Použití uživatelsky definovaných procesorů
Uživatelsky definovaný procesor nebo UDP je typ U-SQL UDO, který umožňuje zpracovat příchozí řádky pomocí funkcí programovatelnosti. Protokol UDP umožňuje kombinovat sloupce, upravit hodnoty a v případě potřeby přidávat nové sloupce. V podstatě pomáhá zpracovat sadu řádků a vytvořit požadované datové prvky.

Abychom mohli definovat UDP, musíme vytvořit `IProcessor` rozhraní s `SqlUserDefinedProcessor` atributem, který je volitelný pro UDP.

Toto rozhraní by mělo obsahovat definici `IRow` přepsání sady řádků rozhraní, jak je znázorněno v následujícím příkladu:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** označuje, že typ by měl být registrován jako uživatelsky definovaný procesor. Tuto třídu nelze zdědit.

Atribut SqlUserDefinedProcessor je pro definici protokolu UDP **volitelný** .

Hlavními objekty programovatelnosti jsou **vstup** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupních sloupců a výstupu a k nastavení výstupních dat v důsledku aktivity procesoru.

Pro výčet vstupních sloupců používáme `input.Get` metodu.

```csharp
string column_name = input.Get<string>("column_name");
```

Parametr pro `input.Get` metodu je sloupec, který je předán jako součást `PRODUCE` klauzule `PROCESS` příkazu základního skriptu U-SQL. V tomto případě musíme použít správný datový typ.

Pro výstup použijte `output.Set` metodu.

Je důležité si uvědomit, že vlastní producent pouze výstupy sloupců a hodnot, které jsou definovány pomocí `output.Set` volání metody.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Skutečný výstup procesoru je aktivován voláním `return output.AsReadOnly();` .

Následuje příklad procesoru:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

V tomto scénáři použití vygeneruje procesor nový sloupec s názvem "full_description" tak, že kombinuje existující sloupce – v tomto případě "User" v případě velkých písmen a "des". Také znovu vygeneruje identifikátor GUID a vrátí původní a nové hodnoty GUID.

Jak vidíte v předchozím příkladu, můžete volat metody jazyka C# během `output.Set` volání metody.

Následuje příklad základního skriptu U-SQL, který používá vlastní procesor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Použít uživatelsky definované appliers
Uživatelsky definované Applier U-SQL vám umožní vyvolat vlastní funkci jazyka C# pro každý řádek, který je vrácený výrazem vnější tabulky dotazu. Správný vstup je vyhodnocen pro každý řádek od levého vstupu a řádky, které jsou vytvořeny, jsou zkombinovány pro konečný výstup. Seznam sloupců, které jsou vytvořeny pomocí operátoru APPLy, je kombinací sady sloupců vlevo a správného vstupu.

Uživatelem definované Applier je vyvoláno jako součást výrazu SELECT USQL.

Typické volání uživatelsky definovaného Applier vypadá takto:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Další informace o použití appliers ve výrazu SELECT najdete v části [U-SQL vyberte možnost vybrat z křížového a vnějšího aplikování](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Uživatelsky definovaná definice základní třídy Applier je následující:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Pro definování uživatelsky definovaného Applier musíme vytvořit `IApplier` rozhraní s `SqlUserDefinedApplier` atributem [], který je volitelný pro uživatelsky definovanou definici Applier.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Pro každý řádek vnější tabulky je volána metoda Apply. Vrátí `IUpdatableRow` výstupní sadu řádků.
* Třída konstruktoru se používá k předání parametrů uživatelsky definovanému Applier.

**SqlUserDefinedApplier** označuje, že typ by měl být zaregistrován jako uživatelsky definovaný Applier. Tuto třídu nelze zdědit.

**SqlUserDefinedApplier** je **volitelná** pro uživatelsky definovanou definici Applier.


Hlavními objekty programovatelnosti jsou tyto:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Vstupní sady řádků jsou předány jako `IRow` vstup. Výstupní řádky jsou generovány jako `IUpdatableRow` výstupní rozhraní.

Jednotlivé názvy sloupců lze určit voláním `IRow` metody schématu.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

K získání skutečných hodnot dat z příchozího `IRow` používání používáme metodu get () `IRow` rozhraní.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Nebo používáme název sloupce schématu:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Výstupní hodnoty musí být nastaveny s `IUpdatableRow` výstupem:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Je důležité pochopit, že vlastní appliers výstupní sloupce a hodnoty, které jsou definovány pomocí `output.Set` volání metody.

Skutečný výstup je aktivován voláním `yield return output.AsReadOnly();` .

Uživatelsky definované parametry Applier lze předat konstruktoru. Applier může vrátit proměnný počet sloupců, které je třeba definovat během volání Applier v základní skriptu U-SQL.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Tady je příklad uživatelsky definovaného Applier:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Následuje základní skript U-SQL pro tento uživatelsky definovaný Applier:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

V tomto scénáři použití uživatelsky definované Applier funguje jako analyzátor hodnot oddělených čárkami pro vlastnosti loďstva auta. Řádky vstupních souborů vypadají jako následující:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jedná se o typický soubor TSV s oddělovači se sloupcem vlastností, který obsahuje vlastnosti auta, jako je například značka a model. Tyto vlastnosti musí být analyzovány do sloupců tabulky. Applier, který je k dispozici, také umožňuje generovat dynamický počet vlastností ve výsledné sadě řádků na základě předaného parametru. Můžete vygenerovat jenom všechny vlastnosti nebo konkrétní sadu vlastností.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

Uživatelsky definované Applier lze volat jako novou instanci objektu Applier:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Nebo s voláním metody vytváření obálky:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Použít uživatelsky definované kombinace
Uživatelsky definovaná kombinovaná nebo UDC umožňuje kombinovat řádky z levé a pravé sady řádků na základě vlastní logiky. V kombinaci s výrazem kombinovat se používá uživatelsky definovaný kombinace.

Kombinování je vyvoláno pomocí výrazu kombinace, který poskytuje potřebné informace o vstupních sadách řádků, sloupcích seskupení, očekávaném schématu výsledků a dalších informacích.

Pro volání kombinovaného skriptu U-SQL používáme následující syntaxi:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Další informace najdete v tématu [kombinování výrazu (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Pro definování uživatelsky definované kombinace musíme vytvořit `ICombiner` rozhraní s `SqlUserDefinedCombiner` atributem [], který je volitelný pro uživatelsky definovanou definici kombinování.

`ICombiner`Definice základní třídy:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Vlastní implementace `ICombiner` rozhraní by měla obsahovat definici pro `IEnumerable<IRow>` přepsání kombinování.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Atribut **SqlUserDefinedCombiner** označuje, že typ by měl být registrován jako uživatelsky definovaný modul pro kombinování. Tuto třídu nelze zdědit.

**SqlUserDefinedCombiner** se používá k definování vlastnosti kombinovaného režimu. Je to volitelný atribut pro uživatelsky definovanou definici kombinování.

CombinerMode režim

Výčet CombinerMode může mít následující hodnoty:

* Úplné (0) každý výstupní řádek potenciálně závisí na všech vstupních řádcích zleva a vpravo se stejnou hodnotou klíče.

* Left (1) každý výstupní řádek závisí na jednom vstupním řádku zleva (a potenciálně všechny řádky napravo se stejnou hodnotou klíče).

* Right (2) každý výstupní řádek závisí na jednom vstupním řádku zprava (a potenciálně všechny řádky nalevo se stejnou hodnotou klíče).

* Vnitřní (3) každý výstupní řádek závisí na jednom vstupním řádku vlevo a vpravo se stejnou hodnotou.

Příklad: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


Hlavními objekty programovatelnosti jsou:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Vstupní sady řádků jsou předány jako **levý** a **pravý** `IRowset` typ rozhraní. Pro zpracování se musí vytvořit výčet obou sad řádků. Každé rozhraní můžete vytvořit pouze jednou, takže je nutné v případě potřeby vytvořit výčet a Uložit do mezipaměti.

Pro účely ukládání do mezipaměti můžeme vytvořit seznam \<T\> struktury paměti jako výsledek provedení dotazu LINQ, konkrétně seznam<`IRow`>. Anonymní datový typ lze použít také při výčtu.

Další informace o dotazech LINQ a [ \<T\> rozhraní IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1) pro další informace o rozhraní IEnumerable naleznete v tématu [Úvod do dotazů LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) \<T\> .

K získání skutečných hodnot dat z příchozího `IRowset` používání používáme metodu get () `IRow` rozhraní.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Jednotlivé názvy sloupců lze určit voláním `IRow` metody schématu.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Nebo použijte název sloupce schématu:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

Obecný výčet s LINQ vypadá takto:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po vytvoření výčtu obou sad řádků projdeme smyčkou všemi řádky. Pro každý řádek v levé sadě řádků budeme najít všechny řádky, které splňují podmínku našeho kombinovaného doplňku.

Výstupní hodnoty musí být nastaveny s `IUpdatableRow` výstupem.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Skutečný výstup je aktivován voláním metody `yield return output.AsReadOnly();` .

Následuje příklad kombinovaného:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

V tomto scénáři použití je sestavování analytické sestavy pro prodejce. Cílem je najít všechny produkty, které jsou vyšší než $20 000 a které procházejí webem rychleji než prostřednictvím pravidelného maloobchodníka v určitém časovém období.

Tady je základní skript U-SQL. Logiku můžete porovnat mezi pravidelným SPOJENÍm a kombinací:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Uživatelsky definovaný kombiner lze volat jako novou instanci objektu Applier:

```csharp
USING new MyNameSpace.MyCombiner();
```


Nebo s voláním metody vytváření obálky:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Použít uživatelsky definované reduktorů

U-SQL umožňuje napsat vlastní sadu řádků reduktorů v jazyce C# pomocí rozhraní rozšiřitelnosti uživatelsky definovaného operátoru a implementací rozhraní IReducer.

Uživatelsky definované zpomalení nebo UDR lze použít k eliminaci zbytečných řádků při extrakci dat (import). Dá se taky použít k manipulaci a vyhodnocení řádků a sloupců. Na základě logiky programovatelnosti může také definovat, které řádky je třeba extrahovat.

Pro definování třídy UDR musíme vytvořit `IReducer` rozhraní s nepovinným `SqlUserDefinedReducer` atributem.

Toto rozhraní třídy by mělo obsahovat definici pro `IEnumerable` přepsání rozhraní sady řádků.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Atribut **SqlUserDefinedReducer** označuje, že typ by měl být registrován jako uživatelsky definovaným omezením. Tuto třídu nelze zdědit.
**SqlUserDefinedReducer** je volitelný atribut pro definici zúžení definované uživatelem. Slouží k definování rekurzivní vlastnosti.

* logická hodnota je nerekurzivní    
* **true**  = určuje, zda je tento redukce asociativní a komutativní

Hlavními objekty programovatelnosti jsou **vstup** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupních řádků. Výstup se používá k nastavení výstupních řádků v důsledku snížení aktivity.

Pro výčet vstupních řádků používáme `Row.Get` metodu.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr pro `Row.Get` metodu je sloupec, který se předává jako součást `PRODUCE` třídy `REDUCE` příkazu základního skriptu U-SQL. Je potřeba použít taky správný datový typ.

Pro výstup použijte `output.Set` metodu.

Je důležité pochopit, že vlastní redukce má jenom hodnoty, které jsou definované pomocí `output.Set` volání metody.

```csharp
output.Set<string>("mycolumn", guid);
```

Skutečný výstup pro redukci se aktivuje voláním metody `yield return output.AsReadOnly();` .

Následuje příklad pro snížení:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

V tomto scénáři použití vynechává zpomalení řádků prázdné uživatelské jméno. Pro každý řádek v sadě řádků načte všechny požadované sloupce a pak vyhodnotí délku uživatelského jména. Vlastní řádek vypíše pouze v případě, že je hodnota délka uživatelského jména větší než 0.

Následuje základní skript U-SQL, který používá vlastní redukci:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
