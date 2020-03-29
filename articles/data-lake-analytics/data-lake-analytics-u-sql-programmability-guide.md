---
title: Průvodce programovatelností U-SQL pro Azure Data Lake
description: Seznamte se s sadou služeb v Azure Data Lake Analytics, které vám umožní vytvořit cloudovou platformu pro velké objemy dat.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162578"
---
# <a name="u-sql-programmability-guide"></a>Průvodce programovatelností U-SQL

U-SQL je dotazovací jazyk, který je určen pro velké objemové daty typu úloh. Jednou z jedinečných vlastností U-SQL je kombinace deklarativního jazyka podobného SQL s rozšiřitelností a programovatelností, kterou poskytuje jazyk C#. V této příručce se soustředíme na rozšiřitelnost a programovatelnost jazyka U-SQL, který je povolen jazykem C#.

## <a name="requirements"></a>Požadavky

Stáhněte a nainstalujte [nástroje Azure Data Lake Tools pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Začínáme s U-SQL  

Podívejte se na následující U-SQL skript:

```
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

Tento skript definuje dvě `@a` sady `@results`řádků: a . Sada `@results` řádků je `@a`definována z .

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy a výrazy jazyka C# ve skriptu U-SQL

Výraz U-SQL je výraz Jazyka C# kombinovaný s `AND`logickými operacemi U-SQL, jako jsou , `OR`a `NOT`. U-SQL výrazy lze použít s SELECT, EXTRACT, KDE, MÍT, GROUP BY a DECLARE. Například následující skript analyzuje řetězec jako datetime hodnotu.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Následující výstřižek analyzuje řetězec jako DateTime hodnotu v příkazu DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Použití výrazů Jazyka C# pro převody datových typů

Následující příklad ukazuje, jak můžete provést převod dat datetime pomocí výrazů C#. V tomto konkrétním scénáři jsou data datetime řetězce převedena na standardní datetime s zápisem času půlnoci 00:00:00.

```
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

### <a name="use-c-expressions-for-todays-date"></a>Použití výrazů jazyka C# pro dnešní datum

Chcete-li vytáhnout dnešní datum, můžeme použít následující výraz C#:`DateTime.Now.ToString("M/d/yyyy")`

Zde je příklad použití tohoto výrazu ve skriptu:

```
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
## <a name="using-net-assemblies"></a>Použití sestavení rozhraní .NET

Model rozšiřitelnosti u-SQL do značné míry závisí na možnosti přidat vlastní kód ze sestavení .NET. 

### <a name="register-a-net-assembly"></a>Registrace sestavení .NET

Pomocí `CREATE ASSEMBLY` příkazu umístěte sestavení .NET do databáze U-SQL. Potom U-SQL skripty můžete použít tato `REFERENCE ASSEMBLY` sestavení pomocí příkazu. 

Následující kód ukazuje, jak zaregistrovat sestavení:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Následující kód ukazuje, jak odkazovat na sestavení:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Podrobněji naleznete [v pokynech k registraci sestavy,](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) které se na toto téma vztahují.


### <a name="use-assembly-versioning"></a>Použití správy verzí sestavení
V současné době U-SQL používá rozhraní .NET Framework verze 4.5. Takže ujistěte se, že vaše vlastní sestavení jsou kompatibilní s tou verzí runtime.

Jak již bylo zmíněno dříve, U-SQL spustí kód ve formátu 64bit (x64). Takže se ujistěte, že váš kód je zkompilován ke spuštění na x64. V opačném případě se zobrazí chyba nesprávného formátu zobrazená dříve.

Každý nahraný soubor DLL sestavení a soubor prostředků, například jiný runtime, nativní sestavení nebo konfigurační soubor, může mít maximálně 400 MB. Celková velikost nasazených prostředků, buď prostřednictvím nasazení prostředků nebo prostřednictvím odkazů na sestavení a jejich další soubory, nesmí překročit 3 GB.

Nakonec si všimněte, že každá databáze U-SQL může obsahovat pouze jednu verzi daného sestavení. Například, pokud potřebujete jak verzi 7, tak verzi 8 knihovny NewtonSoft Json.NET, musíte je zaregistrovat ve dvou různých databázích. Kromě toho každý skript může odkazovat pouze na jednu verzi daného sestavení DLL. V tomto ohledu U-SQL následuje správu sestavení jazyka C# a sémantiku správu verzí.

## <a name="use-user-defined-functions-udf"></a>Použití uživatelem definovaných funkcí: UDF
Uživatelem definované funkce U-SQL nebo UDF jsou programovací rutiny, které přijímají parametry, provádějí akci (například složitý výpočet) a vracejí výsledek této akce jako hodnotu. Vrácená hodnota UDF může být pouze jeden skalár. U-SQL UDF lze volat v U-SQL základní skript jako všechny ostatní c# skalární funkce.

Doporučujeme inicializovat uživatelem definované funkce U-SQL jako **veřejné** a **statické**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Nejprve se podívejme na jednoduchý příklad vytvoření UDF.

V tomto případě použití musíme určit fiskální období, včetně fiskálního čtvrtletí a fiskální měsíc první přihlášení pro konkrétního uživatele. První fiskální měsíc v roce v našem scénáři je červen.

Pro výpočet fiskálního období zavádíme následující funkci C#:

```
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

Jednoduše vypočítá fiskální měsíc a čtvrtletí a vrátí hodnotu řetězce. Pro červen, první měsíc prvního fiskálního čtvrtletí, používáme "Q1:P1". Pro červenec používáme "Q1:P2" a tak dále.

Toto je běžná funkce jazyka C#, kterou budeme používat v našem projektu U-SQL.

Zde je, jak vypadá oddíl s kódem na pozadí v tomto scénáři:

```
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

Nyní budeme volat tuto funkci ze základního u-SQL skriptu. Chcete-li to provést, musíme zadat plně kvalifikovaný název pro funkci, včetně oboru názvů, který v tomto případě je NameSpace.Class.Function(parametr).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Následuje skutečný základní skript U-SQL:

```
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

Následuje výstupní soubor spuštění skriptu:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Tento příklad ukazuje jednoduché použití včleněného udf v U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachovat stav mezi vyvoláními UDF
U-SQL C# programovatelnost objekty může být sofistikovanější, s využitím interaktivity prostřednictvím kódu na pozadí globální proměnné. Podívejme se na následující obchodní scénář použití.

Ve velkých organizacích mohou uživatelé přepínat mezi různými vnitřními aplikacemi. Mezi ně mohou patřit aplikace Microsoft Dynamics CRM, PowerBI a tak dále. Zákazníci mohou chtít použít telemetrickou analýzu toho, jak uživatelé přepínají mezi různými aplikacemi, jaké jsou trendy využití a tak dále. Cílem firmy je optimalizovat využití aplikací. Mohou také chtít kombinovat různé aplikace nebo specifické přihlašovací rutiny.

K dosažení tohoto cíle, musíme určit ID relace a prodleva mezi poslední relace, ke které došlo.

Musíme najít předchozí přihlášení a pak přiřadit toto přihlášení ke všem relacím, které jsou generovány do stejné aplikace. První výzvou je, že základní skript U-SQL nám neumožňuje použít výpočty na již vypočítané sloupce s funkcí LAG. Druhou výzvou je, že musíme zachovat konkrétní zasedání pro všechny relace ve stejném časovém období.

Chcete-li tento problém vyřešit, použijeme globální `static public string globalSession;`proměnnou uvnitř sekce s kódem na pozadí: .

Tato globální proměnná se použije na celou sadu řádků během našeho spuštění skriptu.

Zde je kód-na pozadí části našeho U-SQL programu:

```
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

Tento příklad ukazuje `static public string globalSession;` globální proměnnou použitou `getStampUserSession` uvnitř funkce a získání reinitialized při každé změně session parametr.

Základní skript U-SQL je následující:

```
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

Funkce `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` je volána zde během výpočtu druhé sady řádků paměti. Předá `UserSessionTimestamp` sloupec a vrátí `UserSessionTimestamp` hodnotu, dokud se nezmění.

Výstupní soubor je následující:

```
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

Tento příklad ukazuje složitější scénář použití, ve kterém používáme globální proměnnou uvnitř oddílu s kódem na pozadí, který je použit pro celou sadu řádků paměti.

## <a name="use-user-defined-types-udt"></a>Použití uživatelem definovaných typů: UDT
Uživatelem definované typy nebo UDT je další funkce programovatelnosti U-SQL. U-SQL UDT funguje jako běžný typ definovaný uživatelem jazyka C#. C# je jazyk silného typu, který umožňuje použití předdefinovaných a vlastních typů definovaných uživatelem.

U-SQL nelze implicitně serializovat nebo de-serializovat libovolné UDTs při UDT je předán mezi vrcholy v sadách řádků. To znamená, že uživatel musí poskytnout explicitní modul pro dostovka pomocí rozhraní IFormatter. To poskytuje U-SQL s serializovat a de-serializovat metody pro UDT.

> [!NOTE]
> Vestavěné extraktory a výstupy u-SQL v současné době nelze serializovat nebo deserializovat udt data do nebo ze souborů i s IFormatter set. Takže když píšete data UDT do souboru s příkazem OUTPUT nebo čtete je pomocí extraktoru, musíte je předat jako řetězec nebo byte pole. Potom volat serializace a deserializace kód (to znamená, UDT ToString() metoda) explicitně. Uživatelem definované extraktory a výstupy, na druhé straně, může číst a zapisovat UDTs.

Pokud se pokusíme použít UDT v EXTRACTOR nebo OUTPUTTER (z předchozího SELECT), jak je znázorněno zde:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Zobrazuje se následující chyba:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Chcete-li pracovat s UDT v outputter, musíme buď serializovat řetězec s ToString() metoda nebo vytvořit vlastní outputter.

UDT s aktuálně nelze použít ve skupině GROUP BY. Pokud UDT se používá v GROUP BY, je vyvolána následující chyba:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Chcete-li definovat UDT, musíme:

* Přidejte následující obory názvů:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Add `Microsoft.Analytics.Interfaces`, který je vyžadován pro rozhraní UDT. Kromě toho `System.IO` může být potřeba definovat rozhraní IFormatter.

* Definujte použitý typ s atributem SqlUserDefinedType.

**SqlUserDefinedType** se používá k označení definice typu v sestavení jako uživatelem definovaný typ (UDT) v U-SQL. Vlastnosti atributu odrážejí fyzikální vlastnosti UDT. Tuto třídu nelze zdědit.

SqlUserDefinedType je povinný atribut pro definici UDT.

Konstruktor třídy:  

* SqlUserDefinedTypeAttribute (formát typu)

* Modul pro formátování typu: Povinný parametr pro definování formovnice UDT – konkrétně typ `IFormatter` rozhraní musí být předán zde.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typické UDT také vyžaduje definici rozhraní IFormatter, jak je znázorněno v následujícím příkladu:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Rozhraní `IFormatter` serializuje a deserializuje objektový graf s kořenovým \<typem type mač typem typeparamref name="T">.

\<typeparam name="T">Kořenový typ objektového grafu pro serializaci a deserializaci.

* **Deserializovat**: Deserializuje data v zadaný datový proud a rekonstituuje graf objektů.

* **Serializovat**: Serializuje objekt nebo graf objektů s daným kořenem do daného datového proudu.

`MyType`instance: Instance typu.  
`IColumnWriter`zapisovatel / `IColumnReader` čtenář: Základní sloupec proudu.  
`ISerializationContext`kontext: Výčtu, která definuje sadu příznaků, která určuje zdrojový nebo cílový kontext datového proudu během serializace.

* **Zprostředkuje**: Určuje, že zdrojový nebo cílový kontext není trvalé úložiště.

* **Trvalost**: Určuje, že zdrojový nebo cílový kontext je trvalé úložiště.

Jako běžný typ Jazyka C# může definice U-SQL UDT obsahovat lokální změny pro operátory, například +/==/!=. Může také obsahovat statické metody. Například pokud budeme používat tento UDT jako parametr pro Agregační funkci U-SQL MIN, musíme definovat < přepsání operátoru.

Dříve v této příručce jsme ukázali příklad pro identifikaci `Qn:Pn (Q1:P10)`fiskálního období od konkrétního data ve formátu . Následující příklad ukazuje, jak definovat vlastní typ pro hodnoty fiskálního období.

Následuje příklad oddílu s kódem na pozadí s vlastním rozhraním UDT a IFormatter:

```
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

Definovaný typ obsahuje dvě čísla: čtvrtletí a měsíc. Zde `==/!=/>/<` jsou `ToString()` definovány operátory a statická metoda.

Jak již bylo zmíněno dříve, UDT lze použít ve výrazech SELECT, ale nelze jej použít v OUTPUTTER/EXTRACTOR bez vlastní serializace. Buď musí být serializován jako `ToString()` řetězec s nebo použít s vlastní OUTPUTTER/EXTRACTOR.

Nyní pojďme diskutovat o použití UDT. V části s kódem na pozadí jsme změnili naši funkci GetFiscalPeriod na následující:

```
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

Jak můžete vidět, vrátí hodnotu našeho typu FiscalPeriod.

Zde uvádíme příklad, jak jej dále používat v u-SQL základní skript. Tento příklad ukazuje různé formy vyvolání UDT z U-SQL skriptu.

```
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

```
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
        }



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

## <a name="use-user-defined-aggregates-udagg"></a>Použití uživatelem definovaných agregátů: UDAGG
Uživatelem definované agregace jsou všechny funkce související s agregací, které nejsou dodávány předem s U-SQL. Příkladem může být agregace k provádění vlastních matematických výpočtů, zřetězení řetězců, manipulací s řetězci a tak dále.

Uživatelem definovaná agregovaná definice základní třídy je následující:

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

**SqlUserDefinedAggregate** označuje, že typ by měl být registrován jako uživatelem definovaný agregace. Tuto třídu nelze zdědit.

SqlUserDefinedType atribut je **volitelný** pro definici UDAGG.


Základní třída umožňuje předat tři abstraktní parametry: dva jako vstupní parametry a jeden jako výsledek. Datové typy jsou proměnné a měly by být definovány během dědičnosti třídy.

```
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

* **Init** vyvolá jednou pro každou skupinu během výpočtu. Poskytuje inicializační rutinu pro každou skupinu agregace.  
* **Akumulace** se provádí jednou pro každou hodnotu. Poskytuje hlavní funkce pro algoritmus agregace. Lze jej použít k agregaci hodnot s různými datovými typy, které jsou definovány během dědičnosti třídy. Může přijímat dva parametry proměnných datových typů.
* **Terminate** se provádí jednou za skupinu agregace na konci zpracování k výstupu výsledek pro každou skupinu.

Chcete-li deklarovat správné vstupní a výstupní datové typy, použijte definici třídy následujícím způsobem:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: První parametr, který se akumuluje
* T2: Druhý parametr, který se hromadí
* TResult: Návratový typ ukončení

Například:

```
public class GuidAggregate : IAggregate<string, int, int>
```

– nebo –

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Použití UDAGG v U-SQL
Chcete-li použít UDAGG, nejprve jej definujte v kódu na pozadí nebo jej odkazujte z existující dll programovatelnosti, jak je popsáno výše.

Potom použijte následující syntaxi:

```
AGG<UDAGG_functionname>(param1,param2)
```

Zde je příklad UDAGG:

```
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

A základní U-SQL skript:

```
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

V tomto scénáři použití zřetězíme identifikátory GUID třídy pro konkrétní uživatele.

## <a name="use-user-defined-objects-udo"></a>Použití uživatelem definovaných objektů: UDO
U-SQL umožňuje definovat vlastní programovatelnost objekty, které se nazývají objekty definované uživatelem nebo UDO.

Následuje seznam udo v U-SQL:

* Uživatelem definované extraktory
    * Extrahovat řádek po řádku
    * Slouží k implementaci extrakce dat z vlastních strukturovaných souborů.

* Uživatelem definované výstupy
    * Výstupní řádek po řádku
    * Slouží k výstupu vlastních datových typů nebo vlastních formátů souborů

* Uživatelem definované procesory
    * Vezměte jeden řádek a vyrobte jeden řádek
    * Slouží ke snížení počtu sloupců nebo k vytvoření nových sloupců s hodnotami odvozenými z existující sady sloupců.

* Uživatelem definované appliery
    * Vezměte jeden řádek a vyrobte 0 až n řádků
    * Používá se s OUTER/CROSS APPLY

* Uživatelem definované sdružení
    * Kombinuje sady řádků – uživatelem definované vstupy JOIN

* Uživatelem definované reduktory
    * Vezměte n řádků a vyrobte jeden řádek
    * Slouží ke snížení počtu řádků

UDO se obvykle nazývá explicitně ve skriptu U-SQL jako součást následujících příkazů U-SQL:

* EXTRACT
* Výstup
* Proces
* Kombinovat
* Snížit

> [!NOTE]  
> UDO jsou omezeny na spotřebovávají 0,5 Gb paměti.  Toto omezení paměti se nevztahuje na místní spuštění.

## <a name="use-user-defined-extractors"></a>Použití uživatelem definovaných extraktorů
U-SQL umožňuje importovat externí data pomocí příkazu EXTRACT. Příkaz EXTRACT můžete použít vestavěné extraktory UDO:  

* *Extractors.Text()*: Poskytuje extrakci z oddělených textových souborů různých kódování.

* *Extractors.Csv()*: Poskytuje extrakci ze souborů csv (oddělené od čárek) různých kódování.

* *Extractors.Tsv()*: Poskytuje extrakci ze souborů tabulátorů oddělených hodnot (TSV) různých kódování.

To může být užitečné pro vývoj vlastní extraktor. To může být užitečné při importu dat, pokud chceme provést některý z následujících úkolů:

* Upravte vstupní data rozdělením sloupců a úpravou jednotlivých hodnot. Funkce procesoru je lepší pro kombinování sloupců.
* Analyzujte nestrukturovaná data, jako jsou webové stránky a e-maily, nebo polostrukturovaná data, například XML/JSON.
* Analyzovat data v nepodporovanékódování.

Chcete-li definovat uživatelem definovaný extraktor nebo UDE, musíme vytvořit `IExtractor` rozhraní. Všechny vstupní parametry extraktoru, jako jsou oddělovače sloupců/řádků a kódování, musí být definovány v konstruktoru třídy. Rozhraní `IExtractor` by také mělo obsahovat definici `IEnumerable<IRow>` přepsání takto:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** atribut označuje, že typ by měl být registrován jako uživatelem definované extraktor. Tuto třídu nelze zdědit.

SqlUserDefinedExtractor je volitelný atribut pro definici UDE. Používá se k definování vlastnosti AtomicFileProcessing pro objekt UDE.

* bool AtomicFileProcessing   

* **true** = Označuje, že tento extraktor vyžaduje atomické vstupní soubory (JSON, XML, ...)
* **false** = Označuje, že tento extraktor může řešit rozdělené / distribuované soubory (CSV, SEQ, ...)

Hlavními objekty programovatelnosti UDE jsou **vstupní** a **výstupní**objekty . Vstupní objekt se používá k výčtu `IUnstructuredReader`vstupních dat jako . Výstupní objekt se používá k nastavení výstupních dat v důsledku aktivity extraktoru.

Vstupní data jsou přístupná prostřednictvím `System.IO.Stream` a `System.IO.StreamReader`.

Pro výčet vstupních sloupců nejprve rozdělíme vstupní datový proud pomocí oddělovače řádků.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Potom dále rozdělit vstupní řádek na části sloupce.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Pro nastavení výstupních dat `output.Set` používáme metodu.

Je důležité si uvědomit, že vlastní extraktor pouze výstupy sloupce a hodnoty, které jsou definovány s výstupem. Nastavit volání metody.

```
output.Set<string>(count, part);
```

Skutečný výstup extraktoru se `yield return output.AsReadOnly();`spouští voláním .

Následuje příklad extraktoru:

```
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

V tomto scénáři použití regeneruje extraktor guid pro sloupec "guid" a převede hodnoty sloupce "uživatel" na velká písmena. Vlastní extraktory mohou vytvářet složitější výsledky analýzou vstupních dat a manipulací s nimi.

Následuje základní U-SQL skript, který používá vlastní extraktor:

```
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

## <a name="use-user-defined-outputters"></a>Použití uživatelem definovaných výstupních terců
Uživatelem definovaný výstup je další udo u-sql, který umožňuje rozšířit vestavěnou funkci U-SQL. Podobně jako extraktor existuje několik vestavěných výstupů.

* *Outputters.Text()*: Zapisuje data do oddělených textových souborů různých kódování.
* *Outputters.Csv()*: Zapisuje data do souborů csv oddělených čárkami s různými kódováními.
* *Outputters.Tsv()*: Zapisuje data do souborů tsv (tsv) oddělených tabulátorů s různými kódováními.

Vlastní výstup umožňuje zapisovat data ve vlastním definovaném formátu. To může být užitečné pro následující úkoly:

* Zápis dat do částečně strukturovaných nebo nestrukturovaných souborů.
* Zápis dat není podporován kódování.
* Úprava výstupních dat nebo přidání vlastních atributů.

Chcete-li definovat uživatelem definované outputter, musíme vytvořit `IOutputter` rozhraní.

Následuje implementace `IOutputter` základní třídy:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Všechny vstupní parametry pro outputter, jako jsou oddělovače sloupců/řádků, kódování a tak dále, musí být definovány v konstruktoru třídy. Rozhraní `IOutputter` by také mělo `void Output` obsahovat definici pro přepsání. Atribut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` lze volitelně nastavit pro atomické zpracování souborů. Další informace naleznete v následujících podrobnostech.

```
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

* `Output`je volána pro každý vstupní řádek. Vrátí sadu `IUnstructuredWriter output` řádků.
* Třída Konstruktor se používá k předání parametrů uživatelem definovanému výstupnímu terci.
* `Close`se používá volitelně přepsat uvolnit nákladné stavu nebo určit, kdy byl zapsán poslední řádek.

**SqlUserDefinedOutputter** atribut označuje, že typ by měl být registrován jako uživatelem definované outputter. Tuto třídu nelze zdědit.

SqlUserDefinedOutputter je volitelný atribut pro uživatelem definovanou definici výstupního terce. Používá se k definování AtomicFileProcessing vlastnost.

* bool AtomicFileProcessing   

* **true** = Označuje, že tento výstupní výstup vyžaduje atomické výstupní soubory (JSON, XML, ...)
* **false** = Označuje, že tento outputter může řešit rozdělené / distribuované soubory (CSV, SEQ, ...)

Hlavními programovatelnostmi jsou **řádkové** a **výstupní**objekty . Objekt **řádku** se používá k výčtu `IRow` výstupních dat jako rozhraní. **Výstup** se používá k nastavení výstupních dat do cílového souboru.

Výstupní data jsou přístupná `IRow` prostřednictvím rozhraní. Výstupní data jsou předávána řádku najednou.

Jednotlivé hodnoty jsou uvedeny voláním Get metoda rozhraní IRow:

```
row.Get<string>("column_name")
```

Názvy jednotlivých sloupců `row.Schema`lze určit voláním :

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Tento přístup umožňuje vytvořit flexibilní výstupní pro všechny schématu metadat.

Výstupní data jsou zapsána `System.IO.StreamWriter`do souboru pomocí . Parametr datového proudu `output.BaseStream` je `IUnstructuredWriter output`nastaven jako součást .

Všimněte si, že je důležité vyprázdnění vyrovnávací paměti dat do souboru po každém řádku iterace. Kromě toho `StreamWriter` musí být objekt použit s atributem Jednorázový (výchozí) a s klíčovým slovem **using:**

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

V opačném případě volání Metody Flush() explicitně po každé iteraci. Ukážeme to v následujícím příkladu.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Nastavení záhlaví a zápatí pro uživatelem definovaný výstup
Chcete-li nastavit záhlaví, použijte tok spuštění jedné iterace.

```
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

Pro zápatí použijte odkaz na `System.IO.Stream` instanci objektu (`output.BaseStream`). Zapatařte do metody Close() `IOutputter` rozhraní.  (Další informace naleznete v následujícím příkladu.)

Následuje příklad uživatelem definovaného výstupního terče:

```
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

A U-SQL základní skript:

```
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

Jedná se o výstupní modul HTML, který vytváří soubor HTML s daty tabulky.

### <a name="call-outputter-from-u-sql-base-script"></a>Výstupvolání ze základního skriptu U-SQL
Chcete-li volat vlastní outputter ze základního skriptu U-SQL, musí být vytvořena nová instance objektu outputter.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Abychom se vyhnuli vytvoření instance objektu v základním skriptu, můžeme vytvořit obálku funkce, jak je znázorněno v našem předchozím příkladu:

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

V tomto případě původní volání vypadá takto:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Použití uživatelem definovaných procesorů
Uživatelem definovaný procesor neboli UDP je typ udo u-sql, který umožňuje zpracovávat příchozí řádky použitím funkcí programovatelnosti. UDP umožňuje kombinovat sloupce, upravovat hodnoty a v případě potřeby přidávat nové sloupce. V podstatě pomáhá zpracovat sadu řádků k vytvoření požadovaných datových prvků.

Chcete-li definovat UDP, musíme `IProcessor` vytvořit `SqlUserDefinedProcessor` rozhraní s atributem, který je volitelný pro UDP.

Toto rozhraní by mělo `IRow` obsahovat definici přepsání sady řádků rozhraní, jak je znázorněno v následujícím příkladu:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** označuje, že typ by měl být registrován jako uživatelem definovaný procesor. Tuto třídu nelze zdědit.

Atribut SqlUserDefinedProcessor je **volitelný** pro definici UDP.

Hlavními programovatelnostmi objekty jsou **vstup** a **výstup**. Vstupní objekt se používá k výčtu vstupních sloupců a výstupu a k nastavení výstupních dat v důsledku aktivity procesoru.

Pro výčet vstupních sloupců `input.Get` používáme metodu.

```
string column_name = input.Get<string>("column_name");
```

Parametr pro `input.Get` metodu je sloupec, který je `PRODUCE` předán `PROCESS` jako součást klauzule příkazu základního skriptu U-SQL. Musíme použít správný datový typ zde.

Pro výstup použijte `output.Set` metodu.

Je důležité si uvědomit, že vlastní výrobce pouze výstupy `output.Set` sloupce a hodnoty, které jsou definovány s voláním metody.

```
output.Set<string>("mycolumn", mycolumn);
```

Skutečný výstup procesoru se `return output.AsReadOnly();`aktivuje voláním .

Následuje příklad procesoru:

```
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

V tomto scénáři použití procesoru generuje nový sloupec s názvem "full_description" kombinací existující sloupce -- v tomto případě "uživatel" velkými písmeny a "des". Také regeneruje identifikátor GUID a vrátí původní a nové hodnoty GUID.

Jak můžete vidět z předchozího příkladu, můžete `output.Set` volat metody C# během volání metody.

Následuje příklad základního skriptu U-SQL, který používá vlastní procesor:

```
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

## <a name="use-user-defined-appliers"></a>Použití uživatelem definovaných appliers
Uživatelem definované rozhraní U-SQL umožňuje vyvolat vlastní funkci Jazyka C# pro každý řádek, který je vrácen výrazem vnější tabulky dotazu. Pravý vstup je vyhodnocen pro každý řádek z levého vstupu a řádky, které jsou vytvořeny jsou kombinovány pro konečný výstup. Seznam sloupců, které jsou vytvořeny operátorem APPLY, je kombinací sady sloupců v levém a pravém vstupu.

Uživatelem definované applier je vyvolána jako součást výrazu USQL SELECT.

Typické volání uživatelem definovaného applieru vypadá takto:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Další informace o použití applyrů ve výrazu SELECT naleznete v [tématech Výběr u-SQL select z funkce POUŽÍT KŘÍŽ a VNĚJŠÍ POUŽÍT](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Definice základní třídy applier definovaná uživatelem je následující:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Chcete-li definovat uživatelem definované applier, `IApplier` musíme`SqlUserDefinedApplier`vytvořit rozhraní s atributem [ ], který je volitelný pro uživatelem definovanou definici applier.

```
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

* Použít se nazývá pro každý řádek vnější tabulky. Vrátí `IUpdatableRow` výstupní sadu řádků.
* Třída Konstruktor se používá k předání parametrů uživatelem definovanému applieru.

**SqlUserDefinedApplier** označuje, že typ by měl být registrován jako uživatelem definovaný applier. Tuto třídu nelze zdědit.

**SqlUserDefinedApplier** je **volitelný** pro uživatelem definovanou definici applier.


Hlavní programovatelnost objekty jsou následující:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Vstupní sady řádků `IRow` jsou předány jako vstup. Výstupní řádky jsou generovány jako `IUpdatableRow` výstupní rozhraní.

Názvy jednotlivých sloupců `IRow` lze určit voláním metody Schématu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Chcete-li získat skutečné hodnoty `IRow`dat z příchozích , `IRow` používáme Get() metodu rozhraní.

```
mycolumn = row.Get<int>("mycolumn")
```

Nebo použijeme název sloupce schématu:

```
row.Get<int>(row.Schema[0].Name)
```

Výstupní hodnoty musí být `IUpdatableRow` nastaveny s výstupem:

```
output.Set<int>("mycolumn", mycolumn)
```

Je důležité si uvědomit, že vlastní appliers pouze `output.Set` výstupní sloupce a hodnoty, které jsou definovány s voláním metody.

Skutečný výstup se aktivuje `yield return output.AsReadOnly();`voláním .

Uživatelem definované parametry applier mohou být předány konstruktoru. Applier můžete vrátit proměnný počet sloupců, které je třeba definovat během volání applier v základní U-SQL Script.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Zde je uživatelem definovaný příklad applier:

```
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

Následuje základní skript U-SQL pro tento uživatelem definovaný applier:

```
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

V tomto scénáři použití případ uživatelem funguje jako analyzátor hodnot oddělených čárkami pro vlastnosti vozového parku. Řádky vstupního souboru vypadají takto:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jedná se o typický soubor TSV oddělený tabulátorem se sloupcem vlastností, který obsahuje vlastnosti vozu, jako je například make a model. Tyto vlastnosti musí být analyzovány do sloupců tabulky. Applier, který je k dispozici také umožňuje generovat dynamický počet vlastností v sadě řádků výsledků, na základě parametru, který je předán. Můžete generovat buď všechny vlastnosti nebo pouze určitou sadu vlastností.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Uživatelem definované applier lze volat jako novou instanci applier objektu:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Nebo s vyvoláním metody továrny na obálky:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Použití uživatelem definovaných sničemů
Uživatelem definovaný kombinátor nebo UDC umožňuje kombinovat řádky z levé a pravé sady řádků na základě vlastní logiky. Uživatelem definovaný kombinátor se používá s výrazem COMBINE.

Slučovací objekt je vyvolán s combine výraz, který poskytuje potřebné informace o obou vstupních sad řádků, seskupení sloupce, schéma očekávaného výsledku a další informace.

Chcete-li volat kombinátor v základním u-SQL skriptu, použijeme následující syntaxi:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Další informace naleznete [v tématu COMBINE Expression (U-SQL).](/u-sql/statements-and-expressions/combine-expression)

Chcete-li definovat uživatelem definovaný kombinátor, musíme vytvořit `ICombiner` rozhraní s atributem [`SqlUserDefinedCombiner`], který je volitelný pro uživatelem definovanou definici kombinátoru.

Definice `ICombiner` základní třídy:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Vlastní implementace `ICombiner` rozhraní by měla obsahovat `IEnumerable<IRow>` definici přepsání kombinovat.

```
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

**SqlUserDefinedCombiner** atribut označuje, že typ by měl být registrován jako uživatelem definovaný kombinátor. Tuto třídu nelze zdědit.

**SqlUserDefinedCombiner** se používá k definování vlastnosti režimu Combiner. Jedná se o volitelný atribut pro uživatelem definovanou definici kombinátoru.

Režim combinermode

CombinerMode výčtu může trvat následující hodnoty:

* Plný (0) Každý výstupní řádek potenciálně závisí na všech vstupních řádcích zleva a vpravo se stejnou hodnotou klíče.

* Vlevo (1) Každý výstupní řádek závisí na jednom vstupním řádku zleva (a potenciálně všechny řádky zprava se stejnou hodnotou klíče).

* Vpravo (2) Každý výstupní řádek závisí na jednom vstupním řádku zprava (a potenciálně všechny řádky zleva se stejnou hodnotou klíče).

* Vnitřní (3) Každý výstupní řádek závisí na jednom vstupním řádku zleva a vpravo se stejnou hodnotou.

Příklad:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[ ]


Hlavní programovatelnost objekty jsou:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Vstupní sady řádků jsou předány jako **levý** a **pravý** `IRowset` typ rozhraní. Obě sady řádků musí být uvedeny pro zpracování. Můžete vytvořit výčet pouze jednou, takže musíme výčet a cache v případě potřeby.

Pro účely ukládání do mezipaměti\<můžeme\> vytvořit seznam T typ paměti struktury v důsledku `IRow` spuštění dotazu LINQ, konkrétně Seznam<>. Anonymní datový typ lze použít také během výčtu.

Další informace o dotazech LINQ [a\<\> ](/dotnet/api/system.collections.generic.ienumerable-1) další informace o rozhraní\<T s výjimkou rozhraní LinQ naleznete v\> [tématu Úvod k dotazům LINQ (C#).](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)

Chcete-li získat skutečné hodnoty `IRowset`dat z příchozích , `IRow` používáme Get() metodu rozhraní.

```
mycolumn = row.Get<int>("mycolumn")
```

Názvy jednotlivých sloupců `IRow` lze určit voláním metody Schématu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Nebo pomocí názvu sloupce schématu:

```
c# row.Get<int>(row.Schema[0].Name)
```

Obecný výčet s LINQ vypadá takto:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po výčtu obou sad řádků budeme procházet všechny řádky. Pro každý řádek v levé sadě řádků najdeme všechny řádky, které splňují stav našeho kombinátoru.

Výstupní hodnoty musí být `IUpdatableRow` nastaveny s výstupem.

```
output.Set<int>("mycolumn", mycolumn)
```

Skutečný výstup se aktivuje `yield return output.AsReadOnly();`voláním do .

Následuje příklad kombinátoru:

```
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

V tomto případě použití vytváříme analytickou sestavu pro maloobchodníka. Cílem je najít všechny produkty, které stojí více než 20.000 dolarů a které prodávají prostřednictvím webových stránek rychleji než prostřednictvím běžného prodejce v určitém časovém rámci.

Zde je základní U-SQL skript. Můžete porovnat logiku mezi běžným spojením a kombinátorem:

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

Uživatelem definovaný kombinátor lze volat jako novou instanci objektu applier:

```
USING new MyNameSpace.MyCombiner();
```


Nebo s vyvoláním metody továrny na obálky:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Použití uživatelem definovaných reduktorů

U-SQL umožňuje psát vlastní reduktory sady řádků v C# pomocí uživatelem definovaného rozhraní rozšiřitelnosti operátoru a implementací rozhraní IReducer.

Uživatelem definovaný reduktor nebo UDR lze použít k odstranění nepotřebných řádků během extrakce dat (importu). Lze jej také použít k manipulaci a vyhodnocení řádků a sloupců. Na základě logiky programovatelnosti může také definovat, které řádky je třeba extrahovat.

Chcete-li definovat třídu UDR, `IReducer` musíme `SqlUserDefinedReducer` vytvořit rozhraní s volitelným atributem.

Toto rozhraní třídy by `IEnumerable` mělo obsahovat definici pro přepsání sady řádků rozhraní.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** atribut označuje, že typ by měl být registrován jako uživatelem definované reduktoru. Tuto třídu nelze zdědit.
**SqlUserDefinedReducer** je volitelný atribut pro uživatelem definovanou definici reduktoru. Používá se k definování IsRecursive vlastnost.

* bool IsRecursive    
* **true** = Označuje, zda je tento reduktor asociativní a komutativní

Hlavními programovatelnostmi objekty jsou **vstup** a **výstup**. Vstupní objekt se používá k výčtu vstupních řádků. Výstup se používá k nastavení výstupních řádků v důsledku snížení aktivity.

Pro výčet vstupních řádků `Row.Get` používáme metodu.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr pro `Row.Get` metodu je sloupec, který je `PRODUCE` předán jako `REDUCE` součást třídy příkazu základního skriptu U-SQL. Musíme použít správný datový typ i zde.

Pro výstup použijte `output.Set` metodu.

Je důležité si uvědomit, že vlastní reduktor `output.Set` pouze výstupy hodnoty, které jsou definovány s voláním metody.

```
output.Set<string>("mycolumn", guid);
```

Skutečný výstup reduktoru `yield return output.AsReadOnly();`se spouští voláním .

Následuje příklad reduktoru:

```
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

V tomto případě použití je reduktor přeskočení řádky s prázdným uživatelským jménem. Pro každý řádek v sadě řádků přečte každý požadovaný sloupec a vyhodnotí délku uživatelského jména. Vyprovodí skutečný řádek pouze v případě, že délka hodnoty uživatelského jména je větší než 0.

Následuje základní u-SQL skript, který používá vlastní reduktor:

```
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
