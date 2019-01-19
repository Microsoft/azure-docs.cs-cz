---
title: Průvodce programovatelností U-SQL pro Azure Data Lake
description: Další informace o sadu služeb v Azure Data Lake Analytics, která vám umožní vytvořit velké objemy dat založenému na cloudové platformě.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 9ff75cbd0a4915cdf7045be9a45d11075dda15bd
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402315"
---
# <a name="u-sql-programmability-guide"></a>Průvodce programovatelností U-SQL

U-SQL je dotazovací jazyk, který je určený pro velké objemy dat – typ úlohy. Jeden jedinečné funkce U-SQL je kombinací identifikátoru deklarativní jazyce podobném SQL s rozšiřitelnost a programování, která je k dispozici v jazyce C#. V této příručce se budeme soustředit na rozšiřitelnost a programovací jazyk U-SQL, který je povolen v jazyce C#.

## <a name="requirements"></a>Požadavky

Stáhněte a nainstalujte [Azure Data Lake Tools pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Začínáme s jazykem U-SQL  

Podívejte se na následující skript U-SQL:

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

Tento skript definuje dvě sady řádků: `@a` a `@results`. Sada řádků `@results` je definována z `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C# typy a výrazy ve skriptu U-SQL

Výraz, který U-SQL je výraz C# v kombinaci s logické operace U-SQL například `AND`, `OR`, a `NOT`. Výrazy U-SQL můžete použít s SELECT, EXTRACT, kde, s, Seskupit podle a DEKLAROVAT. Například následující skript analyzuje řetězec jako hodnotu data a času.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Následující fragment kódu analyzuje řetězec jako hodnotu data a času v příkazu DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Používat výrazy jazyka C# pro převody typů dat

Následující příklad ukazuje, jak můžete provést převod dat data a času pomocí výrazy jazyka C#. V našem konkrétním scénáři data řetězce data a času jsou převedeny na standardní data a času v zápisu času 00:00:00 půlnoc.

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

### <a name="use-c-expressions-for-todays-date"></a>Používat výrazy jazyka C# pro dnešní datum

Přetahování dnešní datum, můžeme použít následující výraz jazyka C#: `DateTime.Now.ToString("M/d/yyyy")`

Tady je příklad toho, jak použít tento výraz ve skriptu:

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
## <a name="using-net-assemblies"></a>Sestavení .NET

Model rozšíření U-SQL spoléhá na schopnost přidejte vlastní kód ze sestavení .NET. 

### <a name="register-a-net-assembly"></a>Registrace sestavení .NET

Použití `CREATE ASSEMBLY` příkaz umístí sestavení .NET do databáze U-SQL. Později, můžete použít tato sestavení pomocí skriptů U-SQL `REFERENCE ASSEMBLY` příkazu. 

Následující kód ukazuje, jak zaregistrovat sestavení:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Následující kód ukazuje, jak odkazovat na sestavení:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Poraďte [pokyny k registraci sestavení](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , který popisuje Toto téma podrobněji.


### <a name="use-assembly-versioning"></a>Použití Správa verzí sestavení
V současné době U-SQL pomocí rozhraní .NET Framework verze 4.5. Zajistěte proto, že vaše vlastní sestavení nejsou kompatibilní s touto verzí modulu runtime.

Jak bylo zmíněno dříve, spustí kód U-SQL ve formátu 64bitovou (x 64). Proto se ujistěte, že váš kód je zkompilován pro spuštění na x64. Jinak zobrazí chyby nesprávný formát je uvedeno výše.

Každý nahraný sestavení knihovny DLL a soubor prostředků, jako je jiný modul runtime, nativní sestavení nebo konfigurační soubor může obsahovat nejvýše 400 MB. Celková velikost nasazené prostředky prostřednictvím nasazení prostředků nebo přes odkazy na sestavení a další soubory, nemůže být delší než 3 GB.

Nakonec si všimněte, že každou databázi U-SQL může obsahovat pouze jednu verzi jakékoli dané sestavení. Například pokud potřebujete verze 7 a 8 verzi knihovny NewtonSoft Json.Net, musíte k registraci ve dvou různých databázích. Kromě toho každý skript mohou odkazovat pouze na jednu verzi dané sestavení knihovny DLL. V tomto ohledu následuje U-SQL C# sestavení správy a správy verzí sémantiku.

## <a name="use-user-defined-functions-udf"></a>Použití uživatelem definovaných funkcí: UDF
Uživatelem definované funkce U-SQL nebo UDF, jsou programovací postupy, které přijímají parametry, provedení akce (například složité výpočty) a vrátí výsledek akce jako hodnotu. Návratová hodnota UDF lze pouze jednu skalární hodnota. UDF U-SQL je možné volat v základní skript U-SQL, stejně jako jakékoli jiné funkce jazyka C# skalární.

Doporučujeme, abyste inicializaci U-SQL uživatelsky definovaných funkcí jako **veřejné** a **statické**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

První Podívejme se na jednoduchý příklad vytvoření uživatelem definovanou FUNKCI.

V tomto scénáři případ použití musíme určit fiskální období, včetně fiskální čtvrtletí a fiskální měsíc prvního přihlášení pro konkrétního uživatele. První fiskálního měsíce v roce v našem scénáři je dne.

Pro výpočet fiskální období zavedeme následující funkce jazyka C#:

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

Jednoduše vypočítá fiskálního měsíce, čtvrtletí a vrátí hodnotu řetězce. Dne prvního měsíce prvního fiskální čtvrtletí používáme "Q1:P1". Za červenec můžeme použít "Q1:P2" a tak dále.

To je normální C# funkce, které budeme používat v našem projektu U-SQL.

Zde je, jak vypadá v části kódu v tomto scénáři:

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

Teď se zaměříme na voláním této funkce ze základní skriptu U-SQL. Provedete to tak, musíme zadejte plně kvalifikovaný název funkce včetně oboru názvů, který v tomto případě je NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Toto je skutečná základní skript U-SQL:

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

Toto je výstupní soubor provádění skriptu:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Tento příklad ukazuje, jednoduché použití vložených systému souborů UDF v U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachovat stavu mezi volání UDF
Objekty programovatelností U-SQL C# může být více pokročilé, využívat interaktivitu prostřednictvím použití modelu code-behind globální proměnné. Podívejme se na následující obchodního scénáře případu použití.

Ve velkých organizacích mohou uživatelé přepínat mezi typy prvků interních aplikací. Může jít o Microsoft Dynamics CRM, Power BI a tak dále. Zákazníci pravděpodobně chtějí použít k analýze telemetrických dat o tom, jak uživatelé přepínat mezi různými aplikacemi, jaké jsou trendy využití, a tak dále. Cílem pro firmy je k optimalizaci využití aplikací. Také může být vhodné kombinovat různé aplikace nebo konkrétní rutiny přihlašování.

K dosažení tohoto cíle, musíme určit ID relace a prodlevy mezi poslední relace, ke které došlo.

Musíme najít předchozí přihlášení a pak přiřaďte všechny relace, které se generují pro stejnou aplikaci toto přihlášení. Prvním problémem je, že základní skript U-SQL neumožňuje nám výpočty přes již počítané sloupce pomocí funkce LAG. Druhá před obrovskou výzvou je, že máme zachovat konkrétní relaci na všechny relace v rámci stejné časové období.

Chcete-li tento problém vyřešit, používáme v části použití modelu code-behind globální proměnné: `static public string globalSession;`.

Tato globální proměnná se použije pro celou sadu řádků během naší provádění skriptu.

Tady je použití modelu code-behind část našich programů U-SQL:

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

Tento příklad ukazuje globální proměnná `static public string globalSession;` použít uvnitř `getStampUserSession` funkce a získávání znovu inicializována pokaždé, když se změní parametr relace.

Základní skript U-SQL je následujícím způsobem:

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

Funkce `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` nazývá zde během druhé sady řádků výpočtu paměti. Pak předá `UserSessionTimestamp` sloupce a vrátí hodnotu, dokud `UserSessionTimestamp` došlo ke změně.

Výstupní soubor je následujícím způsobem:

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

Tento příklad ukazuje složitější scénář případu použití používáme globální proměnné v modelu code-behind oddíl, který se použije pro celou paměť sady řádků.

## <a name="use-user-defined-types-udt"></a>Použití uživatelem definovaných typů: UDT
Uživatelem definované typy nebo UDT, je další funkcí programovatelnosti U-SQL. U-SQL UDT funguje jako regulární C# uživatelského typu. C# je jazyk silného typu, který umožňuje použití předdefinované a vlastní uživatelem definované typy.

U-SQL nelze implicitně serializovat nebo deserializovat libovolný uživatelsky definovaný typ UDT je předána mezi vrcholy v sady řádků. To znamená, že uživatel musí zadat explicitní formátovací modul používá rozhraní IFormatter. To poskytuje U-SQL serializace a deserializujeme metody pro UDT.

> [!NOTE]
> Integrované extraktory a výstupní moduly U-SQL nyní nelze serializovat nebo deserializovat UDT dat do nebo z soubory i se sadou IFormatter. Proto při zápisu do souboru s příkazem výstup UDT data, nebo čtení s extraktoru, musíte předat jako pole string nebo byte. Potom volání serializace a deserializace kódu (to znamená, UDT metodu ToString()) explicitně. Uživatelem definované extraktory a výstupní moduly, na druhé straně může číst a zapisovat uživatelsky definované typy.

Pokud se snažíme se použít UDT EXTRAKTOR nebo OUTPUTTER (mimo předchozí výběr), jak je znázorněno zde:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Jsme zobrazí následující chyba:

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

Pro práci v outputter UDT, máme buď ho na řetězec pomocí metody ToString() nebo vytvořit vlastní outputter serializovat.

Uživatelsky definovaný typ aktuálně nelze používat v GROUP BY. Pokud UDT se používá v GROUP BY, je vyvolána následující chybu:

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

Pokud chcete definovat UDT, musíme:

* Přidejte následující obory názvů:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Přidat `Microsoft.Analytics.Interfaces`, které jsou požadovány pro UDT rozhraní. Kromě toho `System.IO` může být zapotřebí k definování rozhraní IFormatter.

* Definování typu použít definované atributem SqlUserDefinedType.

**SqlUserDefinedType** slouží k označení definice typu v sestavení jako uživatelem definovaný typ (UDT) v U-SQL. Vlastnosti v atributu odrážet fyzické vlastnosti UDT. Tato třída nemůže dědit.

SqlUserDefinedType je povinný atribut pro definici UDT.

Konstruktor třídy:  

* SqlUserDefinedTypeAttribute (formátovací modul typu)

* Formátovací modul typu: Povinný parametr pro definování formátování UDT – konkrétně typ `IFormatter` rozhraní musí být předán tady.

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

`IFormatter` Rozhraní serializuje a deserializuje grafu objektu s typem kořenového \<typeparamref name = "T" >.

\<typeparam name = "T" > typ kořenového objektu grafu serializovat a deserializovat.

* **Deserializovat**: Deserializuje data na zadaný datový proud a reconstitutes grafu objektů.

* **Serializace**: Serializuje objekt nebo grafu objektů s danou kořenového adresáře zadaného datového proudu.

`MyType` Instance: Instance daného typu.  
`IColumnWriter` Zapisovač / `IColumnReader` reader: Základní stream pro sloupec.  
`ISerializationContext` Kontext: Výčet, který definuje sadu příznaků, která určuje zdrojový nebo cílový kontext pro datový proud během serializace.

* **Zprostředkující**: Určuje, že zdrojový nebo cílový kontext není trvalému úložišti.

* **Trvalost**: Určuje, že je zdrojový nebo cílový kontext trvalému úložišti.

Jako regulární C# typ, definici UDT U-SQL může obsahovat přepsání pro operátory, jako +/ == nebo! =. Může také obsahovat statické metody. Například, pokud budeme používat tento UDT jako parametr pro agregační funkci MIN U-SQL, musíme definovat < – operátor přepsání.

Dříve v tomto průvodci, jsme prokázali příklad fiskální identifikaci období od určitého data ve formátu `Qn:Pn (Q1:P10)`. Následující příklad ukazuje, jak definovat vlastní typ pro hodnoty fiskální období.

Následuje příklad použití modelu code-behind oddílu s vlastní UDT a IFormatter rozhraní:

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

Definovanému typu obsahuje dvě čísla: čtvrtletí a měsíc. Operátory `==/!=/>/<` a statická metoda `ToString()` jsou zde definovány.

Jak už bylo zmíněno dříve, můžete použít ve výrazech vyberte UDT, ale nelze použít v OUTPUTTER/EXTRAKTOR bez vlastní serializace. Buď musí být serializován jako řetězec s `ToString()` nebo použít ve vlastních OUTPUTTER/EXTRAKTOR.

Nyní Pojďme využití UDT. V části použití modelu code-behind jsme změnili naše GetFiscalPeriod funkce takto:

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

Jak je vidět, vrátí hodnotu typu naše FiscalPeriod.

Tady vám nabízíme příklad, jak používat dále v základní skript U-SQL. Tento příklad ukazuje různé způsoby volání UDT ze skriptu U-SQL.

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

Tady je příklad oddílu úplné kódu:

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

## <a name="use-user-defined-aggregates-udagg"></a>Použití uživatelem definovaných agregacích: UDAGG
Uživatelem definované agregace jsou funkce související s agregace, které nejsou dodávané současně out-of-the-box pomocí U-SQL. V příkladu může být agregace provádět vlastní matematické výpočty, zřetězení řetězců, manipulace s řetězci a tak dále.

Definice uživatelem definované agregace základní třídy je následujícím způsobem:

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

**SqlUserDefinedAggregate** označuje, že typ by měl být zaregistrován jako uživatelem definovaná agregace. Tato třída nemůže dědit.

Atribut SqlUserDefinedType **volitelné** UDAGG definice.


Základní třída umožňuje předat abstraktní tři parametry: dva vstupní parametry a jako výsledek. Datové typy, které jsou proměnné a musí být definován během dědičnost tříd.

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

* **Init** vyvolá jednou pro každou skupinu při výpočtu. Inicializační rutina poskytuje pro každou skupinu agregace.  
* **Accumulate** provádí jednou pro každou hodnotu. Poskytuje hlavní funkce pro algoritmus agregace. Je možné můžete agregovat hodnoty s různými typy dat, které jsou definovány během dědičnost tříd. Může přijmout dva parametry typu data proměnných.
* **Ukončit** provádí jednou na skupinu agregace na konci zpracování do výstupu výsledek pro každou skupinu.

Chcete-li deklarovat správný vstup a výstup datové typy, použijte definici třídy následujícím způsobem:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: První parametr k shromažďování
* T2: První parametr k shromažďování
* TResult: Návratový typ ukončení

Příklad:

```
public class GuidAggregate : IAggregate<string, int, int>
```

nebo

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Použití UDAGG v U-SQL
Použití UDAGG, nejprve jej definovat na použití modelu code-behind nebo odkazovat z existující programování knihovny DLL, jak je uvedeno výše.

Potom použijte následující syntaxi:

```
AGG<UDAGG_functionname>(param1,param2)
```

Tady je příklad UDAGG:

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

A základní skript U-SQL:

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

V tomto scénáři použití jsme zřetězit třídy identifikátory GUID pro konkrétní uživatele.

## <a name="use-user-defined-objects-udo"></a>Použití uživatelem definované objekty: UDO
U-SQL vám umožní definovat vlastní programovatelnosti objekty, které jsou označovány jako uživatelem definované objekty nebo UDO.

Následuje seznam UDO v U-SQL:

* Uživatelem definované – extraktory
    * Extrakce řádek po řádku
    * Používaný k implementaci extrakce dat z vlastní soubory

* Uživatelem definované výstupní moduly
    * Výstup řádek po řádku
    * Použít vlastní datové typy výstupu nebo vlastní soubor formáty

* Uživatelem definované procesorů
    * Využijte jeden řádek a vytvoří jeden řádek
    * Používá ke snížení počtu sloupců nebo vytvářet nové sloupce s hodnotami, které jsou odvozeny z existující sady sloupců

* Uživatelem definované appliers
    * Využijte jeden řádek a vytvářet 0, n řádků
    * Použít s vnější a křížové použít

* Uživatelem definované combiners
    * Kombinuje sady řádků – uživatelský spojení

* Uživatelem definované reduktorů
    * Využijte n řádků a vytvoří jeden řádek
    * Používá ke snížení počtu řádků

UDO je obvykle volána explicitně ve skriptu U-SQL jako součást následující příkazy U-SQL:

* EXTRAKCE
* VÝSTUP
* PROCES
* KOMBINOVÁNÍ
* SNÍŽENÍ

> [!NOTE]  
> Společnosti UDO jsou omezené využívání 0,5 Gb paměti.  Toto omezení paměti se nevztahují na místní spuštění.

## <a name="use-user-defined-extractors"></a>Použití uživatelem definované – extraktory
U-SQL umožňuje import externích dat pomocí příkazu EXTRAKCE. Příkaz EXTRAKCE můžete použít předdefinované – extraktory UDO:  

* *Extractors.Text()*: Poskytuje extrakce z textových souborů s oddělovači jiné kódování.

* *Extractors.Csv()*: Poskytuje extrakce z hodnot oddělených čárkami (CSV) soubory jiné kódování.

* *Extractors.Tsv()*: Poskytuje extrakce z hodnoty oddělené tabulátorem (TSV) soubory jiné kódování.

Může být užitečné pro vývoj vlastní Extraktor. To může být užitečné při importu dat pokud chceme proveďte jednu z následujících úloh:

* Rozdělování sloupců a jednotlivé hodnoty úpravou upravte vstupní data. Funkce procesoru je lepší pro kombinování sloupce.
* Analyzovat nestrukturovaných dat jako jsou webové stránky a e-mailů nebo částečně nestrukturovaných dat jako jsou XML nebo JSON.
* Analyzovat data v nepodporované kódování.

Definovat uživatelem definované Extraktor, nebo zahrnout, potřebujeme vytvořit `IExtractor` rozhraní. Všechny vstupní parametry pro extrakci, jako je například oddělovače řádku/sloupce a kódování, musíte je definovat v konstruktoru třídy. `IExtractor` Rozhraní by měl také obsahovat definici `IEnumerable<IRow>` přepsat následujícím způsobem:

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

**SqlUserDefinedExtractor** atribut označuje, že typ by měl být zaregistrován jako Extraktor se definovaný uživatelem. Tato třída nemůže dědit.

SqlUserDefinedExtractor je volitelný atribut pro definici zahrnout. To slouží k definování AtomicFileProcessing vlastností pro objekt zahrnout.

* BOOL AtomicFileProcessing   

* **Hodnota TRUE** = označuje, že tento Extraktor vyžaduje atomic vstupních souborů (JSON, XML,...)
* **false** = označuje, že tento Extraktor můžete vyřešit rozdělit / distribuovaných souborů (sdílený svazek clusteru, SEQ,...)

Hlavní objekty programovatelnosti zahrnout **vstupní** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupní data jako `IUnstructuredReader`. Výstupní objekt se používá k nastavení výstupní data v důsledku Extraktor aktivity.

Vstupní data se přistupuje přes `System.IO.Stream` a `System.IO.StreamReader`.

Pro výčet vstupní sloupce jsme nejprve rozdělení vstupního datového proudu pomocí oddělovač řádků.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Pak dále rozdělte vstupní řádek části sloupce.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Pokud chcete nastavit výstupní data, používáme `output.Set` metody.

Je důležité pochopit, že vlastní Extraktor pouze výstupů sloupců a hodnot, které jsou definovány s výstupem. Nastavit volání metody.

```
output.Set<string>(count, part);
```

Skutečné Extraktor výstup se aktivuje pomocí volání `yield return output.AsReadOnly();`.

Tady je příklad Extraktor:

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

V tomto scénáři použití extraktoru znovu vygeneruje identifikátor GUID pro sloupec "guid" a převádí hodnoty sloupce "user" na velká písmena. Vlastních extraktorů výsledky složitější analýzu vstupních dat a manipulaci.

Toto je základní skript U-SQL, který používá vlastní Extraktor:

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

## <a name="use-user-defined-outputters"></a>Použití uživatelem definované výstupní moduly
Uživatelem definované outputter je jiný UDO U-SQL, který umožňuje rozšířit vestavěné funkce U-SQL. Podobně jako extraktoru, existuje několik předdefinovaných výstupní moduly.

* *Outputters.Text()*: Zapíše data do textových souborů s oddělovači jiné kódování.
* *Outputters.Csv()*: Zapíše data do různých kódování na soubory hodnotami oddělenými čárkami (CSV).
* *Outputters.Tsv()*: Zapíše data do hodnoty oddělené tabulátorem (TSV) soubory jiné kódování.

Vlastní outputter umožňuje zapisovat data ve vlastním formátu definovaný. To může být užitečné pro následující úlohy:

* Zápis dat do částečně strukturovaná nebo nestrukturovaná soubory.
* Zápis dat není podporováno kódování.
* Změna výstupní data nebo přidávání vlastních atributů.

Chcete-li definovat uživatelem definované outputter, potřebujeme vytvořit `IOutputter` rozhraní.

Toto je základní `IOutputter` implementace třídy:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Všechny vstupní parametry outputter, jako je například oddělovače sloupců a řádků, kódování a tak dále, musíte je definovat v konstruktoru třídy. `IOutputter` Rozhraní by měl také obsahovat definici `void Output` přepsat. Atribut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` můžete volitelně nastavit pro zpracování souboru atomické. Další informace najdete v tématu následující podrobnosti.

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

* `Output` je volána pro každý řádek vstupu. Vrátí `IUnstructuredWriter output` sady řádků.
* Konstruktor třídy se používá k předání parametrů do outputter definovaný uživatelem.
* `Close` umožňuje volitelně přepsání nastavení za účelem uvolnění nákladné stavu nebo určit, kdy byla zapsána poslední řádek.

**SqlUserDefinedOutputter** atribut označuje, že typ by měl být zaregistrován jako outputter se definovaný uživatelem. Tato třída nemůže dědit.

SqlUserDefinedOutputter je volitelný atribut pro definici outputter definovaný uživatelem. Používá se k definování vlastností AtomicFileProcessing.

* BOOL AtomicFileProcessing   

* **Hodnota TRUE** = označuje, že tento outputter vyžaduje atomic výstupní soubory (JSON, XML,...)
* **false** = označuje, že tento outputter můžete vyřešit rozdělit / distribuovaných souborů (sdílený svazek clusteru, SEQ,...)

Programovatelnost hlavní objekty jsou **řádek** a **výstup**. **Řádek** objektu se používá k vytvoření výčtu výstupní data jako `IRow` rozhraní. **Výstup** slouží k nastavení výstupní data k cílovému souboru.

Výstupní data se přistupuje přes `IRow` rozhraní. Výstupní data se předá řádek v čase.

Jednotlivé hodnoty výčtu ve volání metody Get rozhraní IRow:

```
row.Get<string>("column_name")
```

Názvy jednotlivých sloupců se dají určit pomocí volání `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Tento přístup umožňuje vytvářet flexibilní outputter pro žádné schéma metadat.

Výstupní data se zapisují do souboru s použitím `System.IO.StreamWriter`. Parametr datový proud je nastaven na `output.BaseStream` jako součást `IUnstructuredWriter output`.

Všimněte si, že je důležité, abyste po každé iteraci řádek vyprázdní vyrovnávací paměť dat do souboru. Kromě toho `StreamWriter` objekt musí použít s uvolnitelné atribut povoleno (výchozí) a **pomocí** – klíčové slovo:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

V opačném případě metoda vyprázdnění() explicitně volejte po každé iteraci. Ukážeme to v následujícím příkladu.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Nastavte záhlaví a zápatí pro uživatelem definované outputter
Pokud chcete nastavit hlavičku, pomocí jedné iterace spuštění toku.

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

Kód v prvním `if (isHeaderRow)` bloku je provedeno pouze jednou.

V zápatí je uvedené, použijte odkaz na instanci `System.IO.Stream` objektu (`output.BaseStream`). Zápis v zápatí je uvedené v metodě Close() `IOutputter` rozhraní.  (Další informace viz následující příklad.)

Tady je příklad outputter se definovaný uživatelem:

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

A základní skript U-SQL:

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

Toto je outputter HTML, který vytvoří soubor ve formátu HTML se data v tabulce.

### <a name="call-outputter-from-u-sql-base-script"></a>Volání outputter ze základní skriptu U-SQL
Chcete-li zavolat vlastní outputter základní skript U-SQL, je potřeba vytvořit novou instanci objektu outputter.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Vyhnout se vytváření instance objektu v základní skript, můžeme vytvořit funkce obálky, jak je znázorněno v našich předchozího příkladu:

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

V takovém případě původního příkazu vypadá takto:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Použití uživatelem definované procesorů
Uživatelem definované procesoru, nebo UDP, je typ UDO U-SQL, který umožňuje zpracovávat příchozí řádky s použitím funkcí programovatelnosti. UDP umožňuje kombinovat sloupce, změnit hodnoty a v případě potřeby přidávat nové sloupce. V podstatě pomáhá zpracování sady řádků pro produkci prvků požadovaná data.

K definování UDP, potřebujeme vytvořit `IProcessor` rozhraní se službou `SqlUserDefinedProcessor` atribut, který je volitelný pro protokol UDP.

Toto rozhraní může obsahovat definici `IRow` rozhraní sady řádků přepsat, jak je znázorněno v následujícím příkladu:

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

**SqlUserDefinedProcessor** označuje, že typ by měl být zaregistrován jako zpracovatel definovaný uživatelem. Tato třída nemůže dědit.

Atribut SqlUserDefinedProcessor **volitelné** UDP definice.

Programovatelnost hlavní objekty jsou **vstupní** a **výstup**. Vstupní objekt se používá výčet vstupní sloupce a výstup a nastavit výstupní data v důsledku činnosti procesoru.

Výčet vstupní sloupce se používá `input.Get` metody.

```
string column_name = input.Get<string>("column_name");
```

Parametr `input.Get` metoda je sloupec, který je předán jako součást `PRODUCE` klauzuli `PROCESS` příkaz základní skript U-SQL. Musíme zde použít správného datového typu.

Pro výstup, použijte `output.Set` metody.

Je důležité si uvědomit, že vlastní výrobce pouze výstupů sloupců a hodnot, které jsou definovány pomocí `output.Set` volání metody.

```
output.Set<string>("mycolumn", mycolumn);
```

Skutečné procesoru výstupu se aktivuje pomocí volání `return output.AsReadOnly();`.

Tady je příklad procesoru:

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

V tomto scénáři použití procesor generuje nový sloupec s názvem "full_description" kombinací existujícího sloupce – v tomto případě "user" na velká písmena a "des". Také znovu vygeneruje identifikátor GUID a vrací hodnoty původní a nové GUID.

Jak je vidět z předchozího příkladu, můžete volat metody jazyka C# během `output.Set` volání metody.

Tady je příklad základní skript U-SQL, který používá vlastního procesoru:

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

## <a name="use-user-defined-appliers"></a>Použití uživatelem definované appliers
Applier definované uživatelem U-SQL umožňuje vyvolat vlastní funkce C# pro každý řádek, který je vrácený výraz vnější tabulky z dotazu. Správný vstup je vyhodnocen pro každý řádek z levým vstupem a řádky, které jsou vytvářeny zkombinují konečného výstupu. Seznam sloupců, které jsou vytvářeny pomocí operátoru APPLY jsou kombinací sadu sloupců v vlevo a vpravo vstup.

Uživatelem definované applier je vyvolávána jako součást výrazu USQL vyberte.

Typické volání uživatelem definované applier vypadá takto:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Další informace o používání appliers ve výrazu SELECT, naleznete v tématu [U-SQL vyberte výběrem z CROSS APPLY a operátoru OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx).

Definice uživatelem definované applier základní třídy je následujícím způsobem:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

K definování applier se definovaný uživatelem, potřebujeme vytvořit `IApplier` rozhraní se službou [`SqlUserDefinedApplier`] atribut, který je volitelné uživatelem definované applier definici.

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

* Platí se volá pro každý řádek vnější tabulky. Vrátí `IUpdatableRow` výstupní sada řádků.
* Konstruktor třídy se používá k předání parametrů do applier definovaný uživatelem.

**SqlUserDefinedApplier** označuje, že typ by měl být zaregistrován jako applier se definovaný uživatelem. Tato třída nemůže dědit.

**SqlUserDefinedApplier** je **volitelné** applier definice definovaný uživatelem.


Programovatelnost hlavní objekty jsou následující:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Vstupní sady řádků jsou předány jako `IRow` vstupu. Výstupní řádky jsou generovány jako `IUpdatableRow` výstup rozhraní.

Názvy jednotlivých sloupců se dají určit pomocí volání `IRow` metoda schématu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

K získání hodnot skutečná data z příchozí `IRow`, můžeme použít metodu Get() `IRow` rozhraní.

```
mycolumn = row.Get<int>("mycolumn")
```

Nebo používáme sloupec název schématu:

```
row.Get<int>(row.Schema[0].Name)
```

Výstupní hodnoty musí být nastaveno s `IUpdatableRow` výstup:

```
output.Set<int>("mycolumn", mycolumn)
```

Je důležité pochopit, vlastní appliers pouze výstupních sloupců a hodnot, které jsou definovány pomocí `output.Set` volání metody.

Aktuální výstup se aktivuje pomocí volání `yield return output.AsReadOnly();`.

Uživatelem definované applier parametrů může být předán konstruktoru. Applier může vrátit proměnný počet sloupců, které je potřeba určit během applier volání v základní skript U-SQL.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Tady je příklad applier definovaný uživatelem:

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

Toto je základní skript U-SQL pro tento uživatelský applier:

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

V tomto scénáři použití uživatelem definované applier funguje jako analyzátor hodnot oddělených čárkou pro vlastnosti vozového parku automobilu. Řádky vstupní soubor vypadat nějak takto:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

To je typické oddělený tabulátory TSV soubor s vlastností sloupec, který obsahuje vlastnosti car a modelu. Tyto vlastnosti musí být analyzovány na sloupce tabulky. Applier, která je k dispozici také umožňuje generovat dynamické počet vlastností v dané sadě řádků výsledek, na základě parametru, který je předán. Můžete generovat všechny vlastnosti nebo konkrétní sadu pouze vlastnosti.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Uživatelem definované applier lze volat jako novou instanci objektu applier:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Nebo pomocí volání metody obálky objekt pro vytváření:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Použití uživatelem definované combiners
Uživatelem definované kombinační nebo UDC, umožňují kombinovat řádky z levé a pravé sady řádků na základě vlastní logiky. Uživatelem definované kombinační se používá u výrazu KOMBINOVAT.

Kombinační je vyvolávána s KOMBINOVÁNÍ výraz, který obsahuje nezbytné informace o vstupní sady řádků, sloupců seskupení, očekávaný výsledek schématu i další informace.

Volání kombinační v základní skript U-SQL, jsme použijte následující syntaxi:

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

Další informace najdete v tématu [KOMBINOVÁNÍ výrazů (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

K definování kombinační se definovaný uživatelem, potřebujeme vytvořit `ICombiner` rozhraní se službou [`SqlUserDefinedCombiner`] atribut, který je volitelný pro definici kombinační definovaný uživatelem.

Základní `ICombiner` definici třídy:

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

Vlastní implementace `ICombiner` rozhraní by měl obsahovat definici `IEnumerable<IRow>` kombinovat přepsání.

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

**SqlUserDefinedCombiner** atribut označuje, že typ by měl být zaregistrován jako kombinační se definovaný uživatelem. Tato třída nemůže dědit.

**SqlUserDefinedCombiner** se používá k definování vlastností režim kombinační. Je volitelný atribut pro definici kombinační definovaný uživatelem.

Režim CombinerMode

Výčet CombinerMode může mít následující hodnoty:

* Úplné (0), každý řádek výstupu potenciálně závisí na všechny vstupní řádky z levé straně a hned se stejnou hodnotou klíče.

* Vlevo (1) každého řádku výstupu závisí na jeden vstupní řádek vlevo (a potenciálně všechny řádky z pravé strany se stejnou hodnotou klíče).

* Práva (2) každý řádek výstupu závisí na jeden vstupní řádek z pravé straně (a potenciálně všechny řádky z levé strany se stejnou hodnotou klíče).

* Vnitřní (3) každý řádek výstupu závisí na jeden vstupní řádek z vlevo a vpravo se stejnou hodnotou.

Příklad: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Programovatelnost hlavní objekty jsou:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Vstupní sady řádků jsou předány jako **levé** a **správné** `IRowset` typu rozhraní. Obě sady řádků musí být výčtový pro zpracování. Můžete jenom zobrazit výčet každé rozhraní jednou, takže musíme vytvořit výčet a nabídnout ho do mezipaměti v případě potřeby.

Pro účely mezipaměti, můžeme vytvořit seznam\<T\> typ struktury paměti v důsledku LINQ provádění dotazu, konkrétně seznam <`IRow`>. Anonymní typ. je možné během výčtu také.

Zobrazit [Úvod do dotazů LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx) pro další informace o dotazech LINQ, a [IEnumerable\<T\> rozhraní](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) Další informace o rozhraní IEnumerable\<T\> rozhraní.

K získání hodnot skutečná data z příchozí `IRowset`, můžeme použít metodu Get() `IRow` rozhraní.

```
mycolumn = row.Get<int>("mycolumn")
```

Názvy jednotlivých sloupců se dají určit pomocí volání `IRow` metoda schématu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Nebo s použitím sloupce název schématu:

```
c# row.Get<int>(row.Schema[0].Name)
```

Obecné výčet s dotazy LINQ vypadá takto:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po vytvoření výčtu obě sady řádků, budeme k prosmyčkování všech řádků. Pro každý řádek v levém řádků budeme najít všechny řádky, které splňují zadanou podmínku naše kombinační.

Výstupní hodnoty musí být nastaveno s `IUpdatableRow` výstup.

```
output.Set<int>("mycolumn", mycolumn)
```

Aktuální výstup se aktivuje při volání k `yield return output.AsReadOnly();`.

Tady je příklad kombinační:

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

V tomto scénáři použití vytváříme analytická sestava pro prodejce. Cílem je vyhledání všech produktů, které stojí více než 20 000 a který prodávat na webových stránkách rychlejší než prostřednictvím pravidelné prodejce v určitém časovém rámci.

Tady je základní skript U-SQL. Můžete porovnat logika mezi regulárního spojení a kombinační:

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

Nelze volat kombinační uživatelem definované jako novou instanci objektu applier:

```
USING new MyNameSpace.MyCombiner();
```


Nebo pomocí volání metody obálky objekt pro vytváření:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Použití uživatelem definované reduktorů

U-SQL umožňuje psát vlastní sady řádků reduktorů v jazyce C# pomocí rozhraní rozšiřitelnosti uživatelem definovaný operátor a implementace rozhraní IReducer.

Uživatelem definované redukční funkci nebo uživatelem definovaná TRASA, je možné eliminovat zbytečné řádků během extrakce dat (import). To také umožňuje manipulaci a vyhodnocovat u nich řádků a sloupců. Na základě programovatelnosti logiky, může také definovat řádky, které je potřeba extrahovat.

Definování třídy uživatelem definovaná TRASA, potřebujeme vytvořit `IReducer` rozhraní s volitelným `SqlUserDefinedReducer` atribut.

Toto rozhraní třídy by měl obsahovat definici `IEnumerable` přepsat rozhraní sady řádků.

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

**SqlUserDefinedReducer** atribut označuje, že typ by měl být zaregistrován jako redukční se definovaný uživatelem. Tato třída nemůže dědit.
**SqlUserDefinedReducer** je volitelný atribut pro definici uživatelem definované redukční funkci. Používá se k definování IsRecursive vlastnost.

* BOOL IsRecursive    
* **Hodnota TRUE** = označuje, zda je tento redukční funkci asociativních a komutativní

Programovatelnost hlavní objekty jsou **vstupní** a **výstup**. Vstupní objekt slouží k vytvoření výčtu vstupní řádky. Výstup se používá k nastavení výstupní řádky v důsledku snížení aktivity.

Výčet vstupní řádky se používá `Row.Get` metody.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr `Row.Get` metoda je sloupec, který je předán jako součást `PRODUCE` třídu `REDUCE` příkaz základní skript U-SQL. Musíme použít správného datového typu tady také.

Pro výstup, použijte `output.Set` metody.

Je důležité pochopit, tento vlastní redukční funkci pouze vypíše hodnoty, které jsou definovány `output.Set` volání metody.

```
output.Set<string>("mycolumn", guid);
```

Výstup skutečné redukční funkci se aktivuje pomocí volání `yield return output.AsReadOnly();`.

Tady je příklad redukční funkci:

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

V tomto scénáři případem použití redukční funkci přeskakuje řádky s prázdnou uživatelské jméno. Pro každý řádek v sadě řádků %{Rowset/ čte každý povinný sloupec a pak vyhodnotí délka uživatelského jména. Skutečné řádek výstupu pouze v případě, že hodnota délka jména uživatele je větší než 0.

Toto je základní skript U-SQL, který používá vlastní redukční funkci:

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
