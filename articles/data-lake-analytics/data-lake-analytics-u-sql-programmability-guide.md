---
title: Průvodci programovatelnosti U-SQL Azure Data Lake | Microsoft Docs
description: Další informace o sadu služeb v Azure Data Lake, které vám umožní vytvořit cloudové velkých objemů dat platformu.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: saveenr
ms.openlocfilehash: 400057b5ce79cdcf6c7651462e9f497bf647e930
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="u-sql-programmability-guide"></a>Průvodce programovatelnosti U-SQL

U-SQL je dotazovací jazyk, který je určený pro velkých datových typů úloh. Mezi funkce jedinečné jazykem U-SQL se rozumí kombinace jazyka SQL jako deklarativní s rozšiřitelnosti a programovatelnosti, který je zadán v jazyce C#. V této příručce budeme soustředit na rozšiřitelnost a programovatelnosti jazyka U-SQL, který je povolený v jazyce C#.

## <a name="requirements"></a>Požadavky

Stáhněte a nainstalujte [nástrojů Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

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

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy C# a výrazy ve skriptu U-SQL

Výraz U-SQL je výraz jazyka C# v kombinaci s logických operací U-SQL, `AND`, `OR`, a `NOT`. Výrazy U-SQL lze použít s příkazem SELECT, EXTRAHOVÁNÍ, kde s, Seskupit podle a DEKLAROVAT. Například následující skript analyzuje řetězec jako hodnotu data a času.

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

### <a name="use-c-expressions-for-data-type-conversions"></a>Použití jazyka C# výrazů pro konverze datových typů

Následující příklad ukazuje, jak můžete provést převod dat data a času pomocí výrazy jazyka C#. V tomto scénáři konkrétní data řetězce data a času jsou převedeny na standardní datetime s půlnoc 00:00:00 čas zápis.

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

### <a name="use-c-expressions-for-todays-date"></a>Použití jazyka C# výrazů pro dnešní datum

Vyžádání dnešní datum, můžeme použít následující výraz jazyka C#: `DateTime.Now.ToString("M/d/yyyy")`

Tady je příklad toho, jak používat tento výraz ve skriptu:

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

Model rozšiřitelnosti U-SQL založena na možnost Přidat vlastního kódu ze sestavení rozhraní .NET. 

### <a name="register-a-net-assembly"></a>Registrace sestavení rozhraní .NET

Použití `CREATE ASSEMBLY` příkaz umístit sestavení .NET do databáze U-SQL. Později, můžete použít tyto sestavení pomocí skriptů U-SQL `REFERENCE ASSEMBLY` příkaz. 

Následující kód ukazuje, jak registrovat sestavení:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Následující kód ukazuje, jak odkazovat na sestavení:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Obrátit [pokyny pro registraci sestavení](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , která se vztahuje toto téma podrobněji.


### <a name="use-assembly-versioning"></a>Pomocí správy verzí sestavení
U-SQL v současné době používá rozhraní .NET Framework verze 4.5. Proto zajistěte, aby byly kompatibilní s danou verzi modulu runtime vlastního sestavení.

Jak už bylo zmíněno dříve, spustí kód U-SQL ve formátu 64bitovou (x 64). Proto se ujistěte, že je zkompilovaný kód pro spuštění na x64. V opačném případě se zobrazí správný formát chyba, uvedena výše.

Každý odeslán sestavení knihoven DLL a souboru prostředků, jako je jiný modul runtime, nativní sestavení nebo konfiguračním souboru může být maximálně 400 MB. Celková velikost nasazené prostředky, prostřednictvím nasazení prostředků nebo prostřednictvím odkazů na sestavení a další soubory, nemůže být delší než 3 GB.

Nakonec si všimněte, že každý U-SQL databáze může obsahovat pouze jednu verzi žádné zadaného sestavení. Například pokud budete potřebovat verze 7 a 8 verzi knihovny NewtonSoft Json.Net, budete muset registraci je ve dvou různých databází. Kromě toho každý skript může odkazovat pouze na jednu verzi zadaného sestavení knihovny DLL. V tomto ohledu následuje U-SQL C# sestavení správy a správa verzí sémantiky.

## <a name="use-user-defined-functions-udf"></a>Použití uživatelem definované funkce: UDF
Uživatelem definované funkce U-SQL nebo UDF, jsou programovacích rutin, které přijímají parametry, provedení akce (například komplexní výpočet) a vrátit výsledek této akce jako hodnotu. Návratová hodnota UDF lze pouze jednu skalární hodnota. UDF U-SQL je možné volat v základní skript U-SQL jako všechny ostatní C# skalární funkce.

Doporučujeme, abyste inicializaci U-SQL uživatelsky definované funkce jako **veřejné** a **statické**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

První Podíváme se na jednoduchý příklad vytvoření uživatelem definovanou FUNKCI.

V tomto scénáři případ použití musíme určit fiskální období, včetně fiskální čtvrtletí a fiskální měsíc prvního sign-in pro konkrétního uživatele. První měsíc fiskálního roku v našem scénáři je června.

K výpočtu fiskální období, zavedeme následující funkce jazyka C#:

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

Jednoduše vypočítá fiskální měsíc a čtvrtletí a vrátí hodnotu řetězce. Pro červen prvního měsíce od první fiskální čtvrtletí používáme "Q1:P1". Pro červenec můžeme použít "Q1:P2" a tak dále.

To je normální C# funkce budeme používat v našem projekt U-SQL.

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

Teď přidáme volání této funkce ze základní skriptu U-SQL. K tomuto účelu máme zadejte plně kvalifikovaný název funkce, včetně oboru názvů, který je v tomto případě NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Toto je skutečný základní skript U-SQL:

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

Toto je výstupní soubor spuštění skriptu:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Tento příklad ukazuje jednoduchý využití vložené UDF v U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachovat stav mezi UDF volání
U-SQL C# programovatelnosti objekty může být více pokročilé, využitím interaktivity prostřednictvím globální proměnné kódu. Podívejme se na následující scénář případu použití firmy.

Ve velkých organizacích mohou uživatelé přepínat mezi typy interních aplikací. Může jít o Microsoft Dynamics CRM, PowerBI a tak dále. Zákazníci chtít použít analýzu telemetrii jak uživatelé přepínat mezi různými aplikacemi, jaké jsou trendy využití, a tak dále. Cílem pro firmu je za účelem optimalizace využití aplikace. Také může chtějí kombinovat různé aplikace nebo konkrétní rutiny přihlášení.

K dosažení tohoto cíle, musíme určit ID relace a prodlevy mezi poslední relace, který došlo k chybě.

Potřebujeme najít předchozí přihlášení a pak mu přiřaďte tento přihlášení do všech relací, ke kterým dochází na stejné aplikaci. První výzva je, že základní skript U-SQL nepodporuje nám umožňují použít výpočty již počítaného sloupce se funkce LAG. Druhá výzva je, že se musí zachovat konkrétní relace pro všechny relace v rámci stejné časové období.

Chcete-li tento problém vyřešit, používáme globální proměnné v části kódu: `static public string globalSession;`.

Tato globální proměnná se použije pro celý řádků při našich provádění skriptu.

Tady je části kódu programu naše U-SQL:

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

Tento příklad ukazuje globální proměnná `static public string globalSession;` použít uvnitř `getStampUserSession` funkce a získávání inicializace pokaždé, když se změní parametr relace.

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

Funkce `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` se nazývá zde během druhé sady řádků výpočtu paměti. Pak předá `UserSessionTimestamp` sloupce a vrátí hodnotu, dokud `UserSessionTimestamp` došlo ke změně.

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

Tento příklad ukazuje složitější scénář případu použití používáme globální proměnné v části kódu, který se použije pro celý paměti řádků.

## <a name="use-user-defined-types-udt"></a>Uživatelem definované typy použití: UDT
Uživatelem definované typy nebo UDT, je jiná funkcí programovatelnosti U-SQL. U-SQL UDT funguje jako regulární C# definovaný uživatelem typem. C# je jazyk silného typu, který umožňuje použít předdefinované a vlastní uživatelem definované typy.

U-SQL nelze implicitně serializaci nebo libovolný uživatelsky definovaný typ deserializovat, když UDT předána mezi vrcholy v sady řádků. To znamená, že uživatel musí zadat explicitní formátovací modul používá rozhraní IFormatter. To poskytuje U-SQL serializace a deserializovat metody pro UDT.

> [!NOTE]
> Předdefinované extraktory U-SQL a outputters aktuálně nelze serializovat nebo deserializovat UDT data do nebo ze souborů i sadou IFormatter. Proto při zápisu do souboru s příkazem výstupní UDT data, nebo jeho čtení s extrakci, budete muset předat jako řetězec nebo bajtové pole. Potom volání serializace a deserializace explicitně kódu (to znamená, metodu ToString() UDT). Uživatelem definované extraktory a outputters, na druhé straně může číst a zapisovat UDT.

Pokud jsme zkuste použít UDT v EXTRAKTOR nebo OUTPUTTER (mimo předchozí vyberte), jak je vidět tady:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Nemůžeme zobrazit následující chyba:

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

Pro práci s UDT v outputter, máme buď ho na řetězec pomocí metody ToString() nebo vytvořit vlastní outputter serializovat.

UDT aktuálně nelze použít v GROUP BY. Pokud UDT se používá v GROUP BY, je vyvolána k následující chybě:

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

Pokud chcete definovat typ definovaný uživatelem, musíme:

* Přidání následujících oborů názvů:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Přidat `Microsoft.Analytics.Interfaces`, což je vyžadováno pro UDT rozhraní. Kromě toho `System.IO` mohou být potřebné k definování rozhraní IFormatter.

* Definování typu používá definované atributem SqlUserDefinedType.

**SqlUserDefinedType** slouží k označení definice typu v sestavení jako uživatelem definovaný typ (UDT) v U-SQL. Vlastnosti v atributu projeví fyzické charakteristiky UDT. Tato třída nelze dědí.

SqlUserDefinedType je povinný atribut pro UDT definici.

V konstruktoru třídy:  

* SqlUserDefinedTypeAttribute (formátovací modul typu)

* Formátovací modul typu: požadovaný parametr k definování formátování UDT – konkrétně typ `IFormatter` rozhraní musí být předán sem.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typické UDT taky vyžaduje definice IFormatter rozhraní, jak je znázorněno v následujícím příkladu:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` Rozhraní serializuje a poté serializuje grafu objektu s kořenový typ \<typeparamref name = "T" >.

\<typeparam name = "T" > kořenový typ grafu objektů serializovat a deserializovat.

* **Deserializovat**: zrušte serializuje data na zadaný datový proud a reconstitutes grafu objektů.

* **Serializovat**: serializuje objektu, nebo grafu objektů s danou kořenovou do zadaného datového proudu.

`MyType` instance: Instance typu.  
`IColumnWriter` Zapisovač / `IColumnReader` čtečky: základního datového proudu sloupce.  
`ISerializationContext` kontext: výčet, který definuje sadu příznaky, která určuje zdrojový nebo cílový kontext pro datový proud během serializace.

* **Zprostředkující**: Určuje, že kontext zdrojové nebo cílové není trvalého úložiště.

* **Trvalost**: Určuje, že zdrojový nebo cílový kontext je trvalé úložiště.

Jako regulární C# typ definici UDT U-SQL můžete zahrnout přepsání pro operátory, jako +/ == /! =. Může také obsahovat statické metody. Například pokud jsme se bude používat toto UDT jako parametr pro agregační funkci MIN U-SQL, se musí definovat < operátor přepsání.

Dříve v tomto průvodci jsme ukázán příklad pro fiskálním období identifikaci od určitého data ve formátu `Qn:Pn (Q1:P10)`. Následující příklad ukazuje, jak definovat vlastní typ pro hodnoty fiskálním období.

Tady je příklad oddílu kódu s vlastní UDT a IFormatter rozhraní:

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

Definovaného typu zahrnuje dvě čísla: čtvrtletí a měsíce. Operátory `==/!=/>/<` a statickou metodu `ToString()` jsou definovány v tomto poli.

Jak už bylo zmíněno dříve, můžete použít ve výrazech vyberte UDT, ale nejde použít v OUTPUTTER/EXTRAKTOR bez vlastní serializace. Buď musí být serializován jako řetězec s `ToString()` nebo používat s vlastní OUTPUTTER/EXTRAKTOR.

Nyní probereme využití UDT. V části kódu jsme změnili naše funkce GetFiscalPeriod takto:

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

Jak vidíte, vrátí hodnotu typu naše FiscalPeriod.

Zde jsme uveďte příklad toho, jak ho další použít základní skript U-SQL. Tento příklad ukazuje různé formy UDT volání ze skriptu U-SQL.

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
Uživatelem definované agregace jsou funkce související s agregace, které jsou součástí není out-of-the-box se U-SQL. V příkladu může být agregace k provedení vlastní matematické výpočty, zřetězení řetězců, manipulace s řetězci a tak dále.

Definice uživatelem definované agregace základní třídy je následující:

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

**SqlUserDefinedAggregate** označuje, že typ by měl být registrován jako uživatelem definovaná agregace. Tato třída nelze dědí.

Atribut SqlUserDefinedType je **volitelné** pro UDAGG definici.


Základní třída umožňuje předat abstraktní tři parametry: dvě jako vstupní parametry a jako výsledek. Datové typy jsou proměnné a by měl být definován během dědičnosti tříd.

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

* **Init –** vyvolá jednou pro každou skupinu během výpočtu. Poskytuje rutiny inicializace pro každou skupinu agregace.  
* **Accumulate** se spustí jednou pro každou hodnotu. Poskytuje hlavní funkce algoritmu agregace. Může sloužit k agregované hodnoty s různými typy dat, které jsou definovány během dědičnosti tříd. Může přijímat dva parametry proměnné datové typy.
* **Ukončit** se spustí jednou na skupinu agregace na konci zpracování k vypsání výsledků pro každou skupinu.

Deklarovat správný vstupní a výstupní datové typy, použijte definici třídy následujícím způsobem:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Accumulate první parametr
* T2: Accumulate první parametr
* TResult: Návratový typ ukončit

Příklad:

```
public class GuidAggregate : IAggregate<string, int, int>
```

nebo

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Použití UDAGG v U-SQL
Chcete-li použít UDAGG, nejprve definování v kódu nebo odkazovat z existující programovatelnosti knihovny DLL, jak je popsáno výše.

Pak použijte následující syntaxi:

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

V tomto scénáři případ použití jsme řetězení třída identifikátory GUID pro konkrétní uživatele.

## <a name="use-user-defined-objects-udo"></a>Uživatelem definované objekty použít: UDO
U-SQL umožňuje definovat vlastní programovatelnosti objekty, které se nazývají uživatelem definované objekty nebo UDO.

Následuje seznam UDO v U-SQL:

* Uživatelem definované extraktory
    * Extrakce řádek po řádku
    * Použít k implementaci extrakce dat z vlastních strukturovaných souborů

* Uživatelem definované outputters
    * Výstup po řádcích
    * Použít k výstupu vlastní datové typy nebo vlastního souboru formáty

* Uživatelem definované procesorů
    * Vezme jeden řádek a vytvoří jeden řádek
    * Umožňuje snížit počet sloupců nebo vytvořit nové sloupce s hodnotami, které jsou odvozené z existující sady sloupců

* Uživatelem definované appliers
    * Vezme jeden řádek a vytvoří 0 n řádků
    * Použít s vnější/mezi použít

* Uživatelem definované combiners
    * Kombinuje sady řádků – uživatelem definované spojení

* Uživatelem definované přechodky
    * Vezme n řádků a vytvoří jeden řádek
    * Se používá ke snížení počtu řádků

UDO se obvykle nazývá explicitně ve skriptu U-SQL v rámci následující příkazy U-SQL:

* EXTRAKCE
* VÝSTUP
* PROCES
* KOMBINOVÁNÍ
* SNÍŽENÍ

> [!NOTE]  
> UDO na jsou omezené využívat 0,5 Gb paměti.  Toto omezení paměti se nevztahuje na místní spuštění.

## <a name="use-user-defined-extractors"></a>Použití uživatelem definované extraktory
U-SQL umožňuje importovat externí data pomocí příkazu EXTRAKCE. Příkaz EXTRAKCE můžete použít předdefinované extraktory UDO:  

* *Extractors.Text()*: poskytuje extrakci z textových souborů s oddělovači různá kódování.

* *Extractors.Csv()*: poskytuje extrakci z hodnot oddělených čárkami (CSV) soubory různá kódování.

* *Extractors.Tsv()*: poskytuje extrakci z hodnoty oddělené tabulátory (TSV) soubory různá kódování.

Může být užitečné pro vývoj vlastních Extraktor. To může být užitečné při importu dat Pokud nám chcete provádět žádnou z následujících úloh:

* Upravte vstupní data tak, že rozdělení sloupců a změnou jednotlivé hodnoty. Funkce procesoru je lepší pro kombinace sloupců.
* Analyzovat Nestrukturovaná data, jako jsou webové stránky a e-mailů nebo polovičním nestrukturovaných dat, jako je například XML nebo JSON.
* Analyzovat data v kódování nepodporovaný.

K definování vlastní Extraktor nebo OUČIT, je potřeba vytvořit `IExtractor` rozhraní. Všechny vstupní parametry pro extrakci, jako je například sloupce či řádku oddělovače a kódování, musí být definované v konstruktoru třídy. `IExtractor` Rozhraní musí také obsahovat definice pro `IEnumerable<IRow>` přepsat následujícím způsobem:

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

**SqlUserDefinedExtractor** atribut uvádí, že typ by měl být registrován jako Extraktor se uživatelem definované. Tato třída nelze dědí.

SqlUserDefinedExtractor je volitelný atribut pro OUČIT definici. Ji používá k definování vlastností AtomicFileProcessing OUČIT objektu.

* BOOL AtomicFileProcessing   

* **Hodnota TRUE,** = označuje, že tento Extraktor vyžaduje atomic vstupní soubory (JSON, XML,...)
* **false** = označuje, že tento Extraktor můžete řešit rozdělení / distribuované soubory (sdíleného svazku clusteru, SEQ,...)

Hlavní objekty programovatelnosti OUČIT **vstupní** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupní data jako `IUnstructuredReader`. Objekt výstup se používá nastavit výstupní data v důsledku Extraktor aktivity.

Vstupní data přistupuje prostřednictvím `System.IO.Stream` a `System.IO.StreamReader`.

Pro vstupní sloupce výčet nám nejdřív rozdělení vstupního datového proudu pomocí oddělovač řádků.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Další rozdělte pak vstupní řádek na části sloupce.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Pokud chcete nastavit výstupní data, použijeme `output.Set` metoda.

Je důležité si uvědomit, že vlastní Extraktor pouze výstupy sloupců a hodnot, které jsou definovány s výstup. Nastavit volání metody.

```
output.Set<string>(count, part);
```

Skutečné Extraktor výstup se aktivuje při volání metody `yield return output.AsReadOnly();`.

Následuje příklad Extraktor:

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

V tomto scénáři případ použití Extraktor regeneruje identifikátor GUID pro sloupec "guid" a převede k hodnotám sloupce "user" na velká písmena. Vlastní extraktory může vytvářet složitější výsledky analýzou vstupní data a manipulaci.

Toto je základní skript U-SQL, která používá vlastní Extraktor:

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

## <a name="use-user-defined-outputters"></a>Použít outputters uživatelem definované
Uživatelem definované outputter je jiný UDO U-SQL, který vám umožní rozšiřovat integrovanou funkci U-SQL. Podobně jako Extraktor, existuje několik předdefinovaných outputters.

* *Outputters.Text()*: zapisuje data do textových souborů s oddělovači různých kódování.
* *Outputters.Csv()*: zapisuje data do různých kódování na soubory textový soubor s oddělovači (CSV).
* *Outputters.Tsv()*: zapisuje data do hodnoty oddělené tabulátory (TSV) soubory různá kódování.

Vlastní outputter umožňuje zapisovat data ve vlastním formátu definované. To může být užitečné pro následující úkoly:

* Zápis dat do částečně strukturovaných nebo nestrukturovaných souborů.
* Zápis dat není podporováno kódování.
* Úprava výstupní data nebo přidání vlastních atributů.

K definování outputter definovaný uživatelem, je potřeba vytvořit `IOutputter` rozhraní.

Toto je základní `IOutputter` implementaci třídy:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Všechny vstupní parametry outputter, jako jsou sloupce či řádku oddělovače, kódování a tak dále, musí být definované v konstruktoru třídy. `IOutputter` Rozhraní musí také obsahovat definice pro `void Output` přepsat. Atribut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` můžete volitelně můžete nastavit pro zpracování atomic souboru. Další informace najdete v tématu následující podrobnosti.

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
* Konstruktor třída se používá k předat parametry outputter definovaný uživatelem.
* `Close` slouží k volitelně přepsat verzi nákladné stavu nebo zjistit, kdy byla zapsána poslední řádek.

**SqlUserDefinedOutputter** atribut uvádí, že typ by měl být registrován jako outputter se uživatelem definované. Tato třída nelze dědí.

SqlUserDefinedOutputter je volitelný atribut pro definici outputter definovaný uživatelem. Slouží k definování vlastností AtomicFileProcessing.

* BOOL AtomicFileProcessing   

* **Hodnota TRUE,** = označuje, že tento outputter vyžaduje atomic výstupní soubory (JSON, XML,...)
* **false** = označuje, že tento outputter můžete řešit rozdělení / distribuované soubory (sdíleného svazku clusteru, SEQ,...)

Objekty hlavního programovatelnosti **řádek** a **výstup**. **Řádek** objekt se používá k vytvoření výčtu výstupní data jako `IRow` rozhraní. **Výstup** slouží k nastavení výstupní data k cílovému souboru.

Výstupní data přistupuje prostřednictvím `IRow` rozhraní. Výstupní data předávána řádek najednou.

Jednotlivé hodnoty jsou uvedené voláním metody Get IRow rozhraní:

```
row.Get<string>("column_name")
```

Názvy jednotlivých sloupců se dá určit pomocí volání `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Tento přístup umožňuje vytvořit flexibilní outputter pro žádné schéma metadat.

Výstupní data je zapsán do souboru pomocí `System.IO.StreamWriter`. Parametr datový proud je nastaven na `output.BaseStrea` jako součást `IUnstructuredWriter output`.

Všimněte si, že je důležité, abyste po každé iteraci řádek vyprázdní vyrovnávací paměť dat do souboru. Kromě toho `StreamWriter` objekt musí použít s povoleno uvolnitelná atribut (výchozí) a **pomocí** – klíčové slovo:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Jinak volejte metodu Flush() explicitně po každé iteraci. Nemůžeme zobrazit v následujícím příkladu.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Nastavte záhlaví a zápatí pro uživatelem definované outputter
Pokud chcete nastavit hlavičku, použijte jednu iterace provádění toku.

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

Kód v prvním `if (isHeaderRow)` bloku se spustí jenom jednou.

Pro zápatí stránky, použijte odkaz na instanci `System.IO.Stream` objektu (`output.BaseStream`). Zápis zápatí v metodě Close() `IOutputter` rozhraní.  (Další informace najdete v následujícím příkladu.)

Následuje příklad uživatelem definované outputter:

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

Toto je outputter HTML, který vytvoří soubor HTML s dat v tabulce.

### <a name="call-outputter-from-u-sql-base-script"></a>Volání outputter ze základní skriptu U-SQL
Vlastní outputter volat z základní skript U-SQL, je potřeba vytvořit novou instanci objektu outputter.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Abyste se vyhnuli, vytvoření instance objektu v základní skriptu, můžeme vytvořit funkce obálku, jak je znázorněno v našem příkladu starší:

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

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Používají procesory uživatelem definované
Uživatelem definované procesoru nebo UDP, je typ UDO U-SQL, která umožňuje použití funkcí programovatelnosti zpracování příchozí řádky. UDP umožňuje sloučení sloupců, upravte hodnoty a v případě potřeby přidejte nové sloupce. V podstatě je dobré se zpracovat sadu řádků k vytvoření požadované datové prvky.

K definování UDP, je potřeba vytvořit `IProcessor` rozhraní s `SqlUserDefinedProcessor` atribut, který je pro UDP volitelné.

Toto rozhraní by měl obsahovat definici `IRow` rozhraní řádků přepsat, jak je znázorněno v následujícím příkladu:

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

**SqlUserDefinedProcessor** označuje, že typ by měl být registrován jako procesor uživatelem definované. Tato třída nelze dědí.

Je atribut SqlUserDefinedProcessor **volitelné** pro definici UDP.

Objekty hlavního programovatelnosti **vstupní** a **výstup**. Vstupní objekt se používá výčet vstupní sloupce a výstup a nastavit výstupní data v důsledku činnosti procesoru.

Pro vstupní sloupce výčet používáme `input.Get` metoda.

```
string column_name = input.Get<string>("column_name");
```

Parametr pro `input.Get` metoda je sloupec, který se předá jako součást `PRODUCE` klauzuli `PROCESS` prohlášení o základní skript U-SQL. Je potřeba použít na správný typ. zde.

Pro výstup, použijte `output.Set` metoda.

Je důležité si uvědomit, že vlastní výrobce pouze výstupy sloupců a hodnot, které jsou definovány pomocí `output.Set` volání metody.

```
output.Set<string>("mycolumn", mycolumn);
```

Skutečné procesoru výstup se aktivuje při volání metody `return output.AsReadOnly();`.

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

V tomto scénáři případ použití procesor generuje nový sloupec s názvem "full_description" kombinací existujícího sloupce – v tomto případě "user" velká a "des". Také znovu vytvoří identifikátor GUID a vrátí původní a nové hodnoty identifikátor GUID.

Jak vidíte z předchozího příkladu, můžete volat metody C# během `output.Set` volání metody.

Tady je příklad základní skriptu U-SQL, který používá vlastního procesoru:

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

## <a name="use-user-defined-appliers"></a>Použít appliers uživatelem definované
Uživatelem definované applier U-SQL umožňuje vyvolání vlastní C# funkce pro každý řádek, který je vrácen vnější tabulky výraz dotazu. Správný vstup je vyhodnotit pro každý řádek z levého vstup a řádky, které vytváří spojují pro finální výstup. Seznam sloupců, které jsou vytvářeny v operátor APPLY jsou kombinace sadu sloupců v vlevo a vpravo vstup.

Uživatelem definované applier je volaná jako součást výrazu USQL vyberte.

Typické volání applier uživatelem definované vypadá takto:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Další informace o používání appliers ve výrazu SELECT, najdete v části [U-SQL vyberte výběr z křížové použít a vnější použít](https://msdn.microsoft.com/library/azure/mt621307.aspx).

Definice uživatelem definované applier základní třídy je následující:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

K definování applier se definovaný uživatelem, je potřeba vytvořit `IApplier` rozhraní s [`SqlUserDefinedApplier`] atribut, který je pro uživatelem definované applier definice volitelné.

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

* Použít je volána pro každý řádek vnější tabulky. Vrátí `IUpdatableRow` výstup sady řádků.
* Konstruktor třída se používá k předat parametry applier definovaný uživatelem.

**SqlUserDefinedApplier** označuje, že typ by měl být registrován jako applier se uživatelem definované. Tato třída nelze dědí.

**SqlUserDefinedApplier** je **volitelné** pro definici applier definovaný uživatelem.


Objekty hlavního programovatelnosti jsou následující:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Vstupní sady řádků se předávají jako `IRow` vstupní. Výstup řádky jsou generovány jako `IUpdatableRow` rozhraní výstup.

Názvy jednotlivých sloupců se dá určit pomocí volání `IRow` metoda schématu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Chcete-li získat skutečný datových hodnot z příchozích `IRow`, jsme použijte metodu Get() `IRow` rozhraní.

```
mycolumn = row.Get<int>("mycolumn")
```

Nebo používáme schématu název sloupce:

```
row.Get<int>(row.Schema[0].Name)
```

Výstupní hodnoty musí být nastavena s `IUpdatableRow` výstup:

```
output.Set<int>("mycolumn", mycolumn)
```

Je důležité si uvědomit, vlastní appliers pouze výstup sloupců a hodnot, které jsou definovány s `output.Set` volání metody.

Skutečný výstup se aktivuje při volání metody `yield return output.AsReadOnly();`.

Uživatelem definované applier parametry mohou být předaný konstruktoru. Applier může vrátit proměnný počet sloupců, které musí být definované během applier volání v základní skript U-SQL.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Tady je příklad applier uživatelem definované:

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

Toto je základní skript U-SQL pro tento uživatelem definované applier:

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

V tomto scénáři případu použití uživatelem definované applier funguje jako analyzátor oddělených čárkou pro vlastnosti loďstva Auto. Vstupní soubor řádky vypadat následovně:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Je typické oddělený tabulátory TSV soubor s vlastností sloupec, který obsahuje vlastnosti car například výrobce a model. Tyto vlastnosti musí být analyzován na sloupce tabulky. Applier, který je k dispozici také umožňuje vygenerovat dynamické počet vlastností v dané sadě řádků výsledek, na základě parametru, který je předán. Můžete vygenerovat buď všechny vlastnosti, nebo konkrétní sadu pouze vlastnosti.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Uživatelem definované applier lze volat jako novou instanci třídy applier objektu:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Nebo pomocí volání metody vytváření obálky:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Použít combiners uživatelem definované
Uživatelem definované kombinační nebo UDC, můžete kombinovat řádky z levé a pravé sady řádků, na základě vlastní logiky. Uživatelem definované kombinační se používá s KOMBINAČNÍ výraz.

Kombinační je volaná s KOMBINAČNÍ výraz, který obsahuje potřebné informace o jak vstupní sady řádků, sloupců seskupení, očekávaný výsledek schématu a další informace.

Volat kombinační v základní skript U-SQL, můžeme použít následující syntaxi:

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

Další informace najdete v tématu [KOMBINOVAT výrazu (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

K definování kombinační se definovaný uživatelem, je potřeba vytvořit `ICombiner` rozhraní s [`SqlUserDefinedCombiner`] atribut, který je pro definici uživatelem definované kombinační volitelné.

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

**SqlUserDefinedCombiner** atribut uvádí, že typ by měl být registrován jako kombinační se uživatelem definované. Tato třída nelze dědí.

**SqlUserDefinedCombiner** se používá k definování vlastnost kombinační režimu. Je volitelný atribut pro definici kombinační definovaný uživatelem.

Režim CombinerMode

Výčet CombinerMode provést následující hodnoty:

* Úplné (0), každý řádek výstupu potenciálně závisí na všechny vstupní řádky z levé a pravé se stejnou hodnotou klíče.

* Doleva (1) každý řádek výstupu závisí na jeden vstupní řádek z doleva (a potenciálně všechny řádky z pravé se stejnou hodnotou klíče).

* Práva (2) každý řádek výstupu závisí na jeden vstupní řádek z vpravo (a potenciálně všechny řádky z levé straně se stejnou hodnotou klíče).

* Vnitřní (3), každý řádek výstupu závisí na jeden řádek vstupu z doleva a doprava a mají stejnou hodnotu.

Příklad: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Objekty hlavního programovatelnosti jsou:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Vstupní sady řádků se předávají jako **levém** a **správné** `IRowset` typu rozhraní. Obě sady řádků musí být ve výčtu objevit pro zpracování. Každé rozhraní můžete pouze výčet jednou, abychom měli vytvořit výčet a uložení do mezipaměti v případě potřeby.

Pro ukládání do mezipaměti pro účely, můžeme vytvořit seznam\<T\> typ konstrukce paměti v důsledku LINQ spuštění dotazu, konkrétně seznam <`IRow`>. Anonymní datový typ lze během výčtu také.

V tématu [Úvod do dotazů LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx) Další informace o dotazech LINQ a [rozhraní IEnumerable\<T\> rozhraní](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) Další informace o rozhraní IEnumerable\<T\> rozhraní.

Chcete-li získat skutečný datových hodnot z příchozích `IRowset`, jsme použijte metodu Get() `IRow` rozhraní.

```
mycolumn = row.Get<int>("mycolumn")
```

Názvy jednotlivých sloupců se dá určit pomocí volání `IRow` metoda schématu.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Nebo pomocí názvu sloupce schématu:

```
c# row.Get<int>(row.Schema[0].Name)
```

Obecné výčtu s dotazy LINQ vypadá takto:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po vytvoření výčtu obě sady řádků, přidáme můžete procházet všechny řádky. Pro každý řádek v levém řádků přidáme najít všechny řádky, které splňují podmínku naše kombinační.

Výstupní hodnoty musí být nastavena s `IUpdatableRow` výstup.

```
output.Set<int>("mycolumn", mycolumn)
```

Skutečný výstup se aktivuje při volání do `yield return output.AsReadOnly();`.

Následuje příklad kombinační:

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

V tomto scénáři případ použití jsme se vytváření zprávu o analýzy, které prodejce. Cílem je najít všechny produkty, které náklady více než 20 000 $ a který prodeje prostřednictvím webu rychleji než pomocí regulárních prodejce v určitém časovém rámci.

Zde je základní skript U-SQL. Můžete porovnat logiku mezi regulárního spojení a kombinační:

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

Uživatelem definované kombinační lze volat jako novou instanci třídy applier objektu:

```
USING new MyNameSpace.MyCombiner();
```


Nebo pomocí volání metody vytváření obálky:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Použít přechodky uživatelem definované

U-SQL umožňuje psát vlastní řádků přechodky v jazyce C# s využitím rozhraní rozšiřitelnosti uživatelem definovaný operátor a implementace rozhraní IReducer.

Uživatelem definované reduktorem nebo UDR, slouží k vyloučení nepotřebných řádků při extrakci dat (Importovat). Je také slouží k manipulaci a vyhodnotit, řádků a sloupců. Na základě programovatelnosti logiky, ho můžete také definovat řádky musí být rozbalena.

Můžete definovat třídu UDR, je potřeba vytvořit `IReducer` rozhraní s volitelný `SqlUserDefinedReducer` atribut.

Tato třída rozhraní by měl obsahovat definici `IEnumerable` přepsat rozhraní sady řádků.

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

**SqlUserDefinedReducer** atribut uvádí, že typ by měl být registrován jako reduktorem se uživatelem definované. Tato třída nelze dědí.
**SqlUserDefinedReducer** je volitelný atribut pro definici reduktorem definovaný uživatelem. Slouží k definování IsRecursive vlastnost.

* BOOL IsRecursive    
* **Hodnota TRUE,** = označuje, zda je tento reduktorem asociativní a komutativní

Objekty hlavního programovatelnosti **vstupní** a **výstup**. Vstupní objekt se používá k vytvoření výčtu vstupní řádky. Výstup se používá k nastavení výstupní řádků v důsledku omezení aktivity.

Pro vstupní řádky výčet používáme `Row.Get` metoda.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr pro `Row.Get` metoda je sloupec, který se předá jako součást `PRODUCE` třídu `REDUCE` prohlášení o základní skript U-SQL. Je potřeba použít na správný typ. zde také.

Pro výstup, použijte `output.Set` metoda.

Je důležité si uvědomit, že vlastní reduktorem výstupy pouze hodnoty, které jsou definovány pomocí `output.Set` volání metody.

```
output.Set<string>("mycolumn", guid);
```

Skutečné reduktorem výstup se aktivuje při volání metody `yield return output.AsReadOnly();`.

Následuje příklad reduktorem:

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

V tomto scénáři případ použití reduktorem přeskakuje řádky s prázdné jméno. Pro každý řádek v sadě řádků přečte každý požadovaný sloupec, následně vyhodnocuje délka uživatelského jména. Skutečný řádek vyprodukuje pouze v případě, že hodnota délka jména uživatele je větší než 0.

Toto je základní skript U-SQL, která používá vlastní reduktorem:

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
