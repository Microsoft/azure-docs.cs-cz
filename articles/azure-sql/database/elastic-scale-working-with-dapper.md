---
title: Použití klientské knihovny elastické databáze s Dapperem
description: Použití klientské knihovny elastické databáze s Dapperem.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793360"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Použití klientské knihovny elastické databáze s Dapperem
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento dokument je určen vývojářům, kteří se spoléhají na Dapperem k vytváření aplikací, ale také chtějí využít [elastické databázové nástroje](elastic-scale-introduction.md) k vytváření aplikací, které implementují horizontálního dělení pro horizontální navýšení kapacity datové vrstvy.  Tento dokument popisuje změny v aplikacích založených na Dapperem, které jsou nezbytné pro integraci s nástroji elastické databáze. Naše zaměření se zaměřuje na vytváření horizontálních oddílů správy elastické databáze a směrování závislého na datech pomocí Dapperem. 

**Vzorový kód** : [nástroje elastické databáze pro integraci Azure SQL Database-dapperem](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrace **dapperem** a **DapperExtensions** s klientskou knihovnou elastické databáze pro Azure SQL Database je snadné. Vaše aplikace mohou používat směrování závislé na datech změnou vytváření a otevírání nových objektů [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) pro použití volání [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) z [klientské knihovny](/previous-versions/azure/dn765902(v=azure.100)). Tím se změny v aplikaci omezí jenom na místo, kde se vytvářejí a otevřou nová připojení. 

## <a name="dapper-overview"></a>Dapperem – přehled
**Dapperem** je objektově-relační Mapovač. Mapuje objekty .NET z aplikace do relační databáze (a naopak). První část ukázkového kódu znázorňuje, jak můžete integrovat knihovnu klienta elastické databáze s aplikacemi založenými na Dapperem. Druhá část ukázkového kódu ukazuje, jak integrovat při použití Dapperem i DapperExtensions.  

Funkce Mapper v Dapperem poskytuje metody rozšíření pro databázová připojení, která zjednodušují odesílání příkazů T-SQL pro spuštění nebo dotazování databáze. Dapperem například usnadňuje mapování mezi objekty .NET a parametry příkazů SQL pro **spuštění** volání nebo pro využití výsledků dotazů SQL do objektů .NET pomocí volání **dotazů** z dapperem. 

Při použití DapperExtensions už nemusíte zadávat SQL příkazy. Metody rozšíření, jako je **GetList** nebo **INSERT** přes připojení k databázi, vytvářejí příkazy SQL na pozadí.

Další výhodou Dapperem a také DapperExtensions je, že aplikace řídí vytvoření připojení k databázi. To pomáhá komunikovat s klientskou knihovnou elastické databáze, která poskytuje připojení databáze na základě mapování shardlety k databázím.

Chcete-li získat sestavení Dapperem, přečtěte si část [dapperem tečka net](https://www.nuget.org/packages/Dapper/). Rozšíření Dapperem naleznete v tématu [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Rychlý pohled na klientskou knihovnu elastické databáze
Pomocí klientské knihovny elastické databáze definujete oddíly dat vaší aplikace s názvem *shardlety* , namapujete je na databáze a identifikujete je pomocí *klíčů horizontálního dělení* . Můžete mít tolik databází, kolik potřebujete, a distribuovat shardlety napříč těmito databázemi. Mapování hodnot klíče horizontálního dělení na databáze je uloženo v mapě horizontálních oddílů, kterou poskytuje rozhraní API knihovny. Tato funkce se nazývá **Správa mapování horizontálních oddílů** . Mapa horizontálních oddílů slouží také jako zprostředkovatel připojení databáze pro požadavky, které přenášejí klíč horizontálního dělení. Tato funkce se označuje jako **Směrování závislé na datech** .

![Mapy horizontálních oddílů a směrování závislé na datech][1]

Správce map horizontálních oddílů chrání uživatele před nekonzistentními zobrazeními do dat shardletu, ke kterým může dojít, když se v databázích děje souběžné operace správy shardletu. Za tímto účelem horizontálních oddílů mapuje připojení databáze pro aplikaci vytvořenou pomocí knihovny. Pokud by horizontálních oddílů operace správy mohly mít vliv na shardletu, umožňuje funkce mapy horizontálních oddílů automaticky odstranit připojení k databázi. 

Místo používání tradičního způsobu vytváření připojení pro Dapperem je nutné použít [metodu OpenConnectionForKey](/previous-versions/azure/dn824099(v=azure.100)). Tím zajistíte, že proběhne všechna ověření a že se všechna data přesunou mezi horizontálních oddílů, budou připojení spravovaná správně.

### <a name="requirements-for-dapper-integration"></a>Požadavky na integraci Dapperem
Při práci s knihovnou klienta elastické databáze a rozhraními API Dapperem chcete zachovat následující vlastnosti:

* **Horizontální** navýšení kapacity: chceme v případě požadavků na kapacitu aplikace přidat nebo odebrat databáze z datové vrstvy aplikace horizontálně dělené. 
* **Konzistence** : vzhledem k tomu, že aplikace se škáluje pomocí horizontálního dělení, je nutné provést směrování závislé na datech. K tomu chceme použít možnosti směrování závislé na datech knihovny. Konkrétně je vhodné zachovat záruky ověřování a konzistence poskytované připojeními, které jsou zprostředkované prostřednictvím Správce map horizontálních oddílů, aby se předešlo poškození nebo špatnému výsledku dotazu. Tím zajistíte, že připojení k danému shardletuu se odmítnou nebo zastaví, pokud (například instance) shardletu je v současné době přesunuta do jiného horizontálních oddílů pomocí rozhraní API pro dělení a slučování.
* **Mapování objektu** : chceme zachovat pohodlí mapování poskytovaných dapperem k překladu mezi třídami v aplikaci a podkladové struktury databáze. 

V následující části najdete pokyny pro tyto požadavky na aplikace založené na **dapperem** a **DapperExtensions** .

## <a name="technical-guidance"></a>Technické pokyny
### <a name="data-dependent-routing-with-dapper"></a>Směrování závislé na datech s Dapperem
V Dapperem je aplikace obvykle odpovědná za vytváření a otevírání připojení k podkladové databázi. Při zadání typu t aplikací Dapperem vrátí výsledky dotazu, protože kolekce .NET typu T. Dapperem provede mapování z řádků výsledku T-SQL na objekty typu T. Podobně Dapperem mapuje objekty .NET do hodnot nebo parametrů SQL pro příkazy jazyka DML (data remanipulace Language). Dapperem nabízí tuto funkci prostřednictvím metod rozšíření pro běžný objekt [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) z klientských knihoven objektů ADO .NET SQL. K [SqlConnectionm](/dotnet/api/system.data.sqlclient.sqlconnection) objektům, které jsou vracené rozhraními API elastického ŠKÁLOVÁNÍ pro DDR, se také používají běžné objekty. Díky tomu můžeme přímo používat rozšíření Dapperem přes typ vrácený rozhraním API DDR klientské knihovny, protože to je také jednoduché připojení klienta SQL.

Tyto poznámky zjednodušují použití připojení, která jsou zprostředkovaná knihovnou klienta elastické databáze pro Dapperem.

Tento příklad kódu (z doprovodné ukázky) ilustruje přístup, kde horizontálního dělení klíč poskytuje aplikace do knihovny za účelem zprostředkovatele připojení ke správnému horizontálních oddílů.   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

Volání rozhraní [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) API nahrazuje výchozí vytváření a otevírání připojení klienta SQL. Volání [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) přijímá argumenty, které jsou požadovány pro směrování závislé na datech: 

* Mapa horizontálních oddílů pro přístup k rozhraním směrování závislým na datech
* Klíč horizontálního dělení k identifikaci shardletu
* Přihlašovací údaje (uživatelské jméno a heslo) pro připojení k horizontálních oddílů

Objekt mapy horizontálních oddílů vytvoří připojení k horizontálních oddílů, které obsahuje shardletu pro daný klíč horizontálního dělení. Rozhraní API klienta elastické databáze také označí připojení k implementaci záruk konzistence. Vzhledem k tomu, že volání [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) vrací běžný objekt připojení klienta SQL, následné volání metody rozšíření **Execute** z Dapperem se řídí standardním dapperem postupem.

Dotazy fungují velmi stejným způsobem – nejprve otevřete připojení pomocí [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) z klientského rozhraní API. Pak použijete regulární metody rozšíření Dapperem k namapování výsledků dotazu SQL do objektů .NET:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

Všimněte si, že blok **using** s připojením DDR je oborem všech databázových operací v rámci bloku do jedné horizontálních oddílů, kde se udržuje tenantId1. Dotaz vrátí pouze Blogy uložené na aktuální horizontálních oddílů, ale ne ty, které jsou uložené na všech ostatních horizontálních oddílů. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Směrování závislé na datech s Dapperem a DapperExtensions
Dapperem obsahuje ekosystém dalších rozšíření, která poskytují lepší pohodlí a abstrakci z databáze při vývoji databázových aplikací. DapperExtensions je příklad. 

Použití DapperExtensions ve vaší aplikaci nemění způsob vytváření a správy připojení k databázi. Je stále zodpovědností aplikace otevírat připojení a metody rozšíření očekávají běžné objekty připojení klienta SQL. Můžeme spoléhat na [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) , jak je uvedeno výše. Jak ukazuje následující ukázka kódu, jedinou změnou je, že již nemusíte psát příkazy T-SQL:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

A zde je ukázka kódu pro dotaz: 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>Zpracování přechodných chyb
Tým Microsoft Patterns & Practices publikoval [přechodný blok aplikace pro zpracování přechodných chyb](/previous-versions/msp-n-p/hh680934(v=pandp.50)) , který vývojářům aplikací umožní zmírnit při spuštění v cloudu časté problémy s přechodnými chybami. Další informace najdete v tématu [Perseverance, tajný klíč všech Triumphs: použití bloku přechodné aplikace pro zpracování chyb](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

Ukázka kódu spoléhá na přechodovou knihovnu selhání k ochraně proti přechodným chybám. 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

**SqlDatabaseUtils. SqlRetryPolicy** ve výše uvedeném kódu je definováno jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací doba mezi opakovanými pokusy. Pokud používáte transakce, ujistěte se, že se váš rozsah opakování vrátí zpět na začátek transakce v případě přechodného selhání.

## <a name="limitations"></a>Omezení
Přístup popsaný v tomto dokumentu má několik omezení:

* Vzorový kód pro tento dokument neukazuje, jak spravovat schéma napříč horizontálních oddílů.
* Vzhledem k žádosti předpokládáme, že veškeré zpracování této databáze je obsaženo v jednom horizontálních oddílů, jak je identifikované pomocí klíče horizontálního dělení, který poskytuje požadavek. Tento předpoklad ale nedrží vždycky, například když není možné zpřístupnit horizontálního dělení klíč. K tomu je potřeba, aby Klientská knihovna pro elastickou databázi obsahovala [třídu MultiShardQuery](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception). Třída implementuje abstrakci připojení pro dotazování přes několik horizontálních oddílů. Použití MultiShardQuery v kombinaci s Dapperem je nad rámec tohoto dokumentu.

## <a name="conclusion"></a>Závěr
Aplikace využívající Dapperem a DapperExtensions můžou snadno využívat výhod nástrojů elastické databáze pro Azure SQL Database. Pomocí kroků popsaných v tomto dokumentu můžou tyto aplikace používat schopnost nástroje pro směrování závislé na datech změnou vytváření a otevírání nových objektů [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) , aby se použilo volání [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) z klientské knihovny elastické databáze. Tím se omezí změny aplikace, které jsou potřeba pro ta místa, kde se vytvoří a otevřou nová připojení. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png