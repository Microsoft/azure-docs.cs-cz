---
title: Klientská knihovna pro elastické databáze pomocí Dapper | Microsoft Docs
description: Klientská knihovna pro elastické databáze pomocí Dapper.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 6619f2dfe5f58cd23dbd0ffe6e2b545b803f3acc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647924"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Klientská knihovna pro elastické databáze pomocí Dapper
Tento dokument je pro vývojáře, které spoléhají na Dapper vytvářet aplikace, ale také chcete zapojení [nástroje elastické databáze](sql-database-elastic-scale-introduction.md) k vytvoření aplikací, že implementace horizontálního dělení chcete škálovat své datové vrstvy.  Tento dokument ukazuje změny v Dapper aplikacím, které jsou potřebné k integraci s nástroje elastické databáze. Naše zaměřuje se na správu horizontálního oddílu elastické databáze a závislé na data směrování s Dapper skládání. 

**Ukázkový kód**: [nástroje elastické databáze pro databázi SQL Azure - Dapper integrace](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrace **Dapper** a **DapperExtensions** s elastickou databází je snadné klientské knihovny pro databázi SQL Azure. Aplikace může použít závislé na data směrování změnou vytváření a otevírání nových [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekty, které chcete použít [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) volat z [klientské knihovny ](http://msdn.microsoft.com/library/azure/dn765902.aspx). Toto nastavení omezuje změny v aplikaci jenom tam, kde jsou vytvořeny a otevřít nové připojení. 

## <a name="dapper-overview"></a>Dapper – přehled
**Dapper** je objekt relační mapper. Mapuje objekty .NET z vaší aplikace na relační databázi (a naopak). První část ukázkový kód ukazuje, jak integrovat klientské knihovny pro elastické databáze pomocí aplikace založené na Dapper. Druhá část ukázkový kód ukazuje, jak integrovat při používání Dapper a DapperExtensions.  

Funkce mapper v Dapper poskytuje rozšiřující metody u připojení databáze, které zjednodušují odesílá příkazů T-SQL pro provádění nebo dotaz na databázi. Například Dapper usnadňuje mapování mezi objekty rozhraní .NET a parametry příkazů SQL pro **Execute** volání, nebo využívat výsledky vašich dotazů SQL na objekty .NET pomocí **dotazu** volání z Dapper. 

Při použití DapperExtensions, už potřebujete poskytovat příkazy SQL. Metody rozšíření, jako **GetList** nebo **vložit** přes připojení k databázi vytváření příkazů SQL na pozadí.

Další výhodou Dapper a také DapperExtensions je, že aplikace řídí vytváření připojení k databázi. To pomáhá interakci s klientskou knihovnu elastické databáze, která zprostředkovatelé databáze připojení na základě mapování shardlets k databázím.

Chcete-li získat Dapper sestavení, přečtěte si téma [Dapper tečka síť](http://www.nuget.org/packages/Dapper/). Dapper rozšíření, najdete v části [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Rychlý přehled klientské knihovny pro elastické databáze
Pomocí klientské knihovny pro elastické databáze, definovat oddíly data aplikací názvem *shardlets*, jejich namapování na databáze a identifikovat pomocí *horizontálního dělení klíče*. Může mít libovolný počet databází, potřebujete a distribuovat vaše shardlets do těchto databází. Mapování hodnot klíče horizontálního dělení k databázím ukládá horizontálního oddílu mapu poskytuje rozhraní API knihovny. Tato funkce je volána **horizontálního oddílu mapy správu**. Mapování horizontálních slouží taky jako zprostředkovatel připojení databáze pro požadavky, které zajišťují klíč horizontálního dělení. Tato funkce se označuje jako **závislé na data směrování**.

![Mapování horizontálních a závislé na data směrování][1]

Mapa správce horizontálního oddílu chrání uživatelé z nekonzistentní zobrazení do shardlet data, která může dojít, když operace správy souběžných shardlet se děje v databázích. Uděláte to tak mapy horizontálního oddílu Zprostředkovatel připojení databáze pro aplikace vytvořené s nástroji knihovny. Při operacích správy horizontálního oddílu by mohlo mít vliv shardlet, tato funkce umožňuje funkci horizontálního oddílu mapy automaticky ukončit připojení k databázi. 

Místo použití tradiční způsob, jak vytvořit připojení pro Dapper, budete muset použít [OpenConnectionForKey metoda](http://msdn.microsoft.com/library/azure/dn824099.aspx). To zajišťuje, že všechny ověřování probíhá a připojení spravuje správně, pokud se přesune všechna data mezi horizontálních oddílů.

### <a name="requirements-for-dapper-integration"></a>Požadavky pro Dapper integrace
Při práci s klientské knihovny pro elastické databáze a Dapper rozhraní API, které chcete uchovávat následující vlastnosti:

* **Horizontální navýšení kapacity**: chceme přidat nebo odebrat databáze z datové vrstvy horizontálně dělené aplikace v případě potřeby u požadavky kapacity aplikace. 
* **Konzistence**: vzhledem k tomu, že aplikace je škálovat na více systémů pomocí horizontálního dělení, je třeba provést závislé na data směrování. Chceme používat funkce směrování dat závislé knihovny Uděláte to tak. Konkrétně chcete zachovat ověření a konzistence zaručuje poskytované připojení, která jsou zprostředkované prostřednictvím správce mapy horizontálního oddílu aby se zabránilo poškození nebo výsledky nesprávný dotazu. To zajišťuje, že se připojení k dané shardlet odmítl nebo zastavena, pokud (například) shardlet aktuálně přesunout do jiné horizontálních pomocí rozhraní API rozdělení či sloučení.
* **Mapování objektu**: chceme zachovat pohodlím, které představuje mapování poskytované Dapper pro převod mezi třídy v aplikaci a základní struktury databáze. 

Následující část obsahuje pokyny pro tyto požadavky pro aplikace na základě **Dapper** a **DapperExtensions**.

## <a name="technical-guidance"></a>Technické pokyny
### <a name="data-dependent-routing-with-dapper"></a>Závislé na data směrování s Dapper
S Dapper je obvykle zodpovědný za vytváření a otevírání připojení k databázi základní aplikace. Zadaný typ T aplikací, Dapper vrátí výsledky dotazu jako kolekcí .NET typu T. Dapper provede mapování od řádků výsledek T-SQL pro objekty typu T. Podobně Dapper mapuje objekty .NET do hodnot SQL nebo parametry pro příkazy data manipulaci language (DML). Tato funkce prostřednictvím metody rozšíření na běžné nabízí Dapper [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt z knihoven klienta SQL ADO .NET. Připojení SQL vrácený rozhraními API sady elastické škálování pro DDR jsou také regulární [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekty. To umožňuje nám přímo použít Dapper rozšíření přes typ vrácený rozhraním API DDR klientské knihovny, protože je zároveň jednoduchého připojení klienta SQL.

Tyto připomínky zjednodušují používat připojení pro Dapper zprostředkované pomocí klientské knihovny pro elastické databáze.

Tento příklad kódu (od doprovodné vzorku) znázorňuje přístup, kde je klíč horizontálního dělení poskytované aplikaci do knihovny Zprostředkovatel připojení k správné horizontálního oddílu.   

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

Volání [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) rozhraní API nahradí výchozí vytváření a otevírání připojení klienta SQL. [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) volání přijímá argumenty, které jsou požadovány pro směrování závislé na data: 

* Mapování horizontálních pro přístup k rozhraní závislé na data
* Klíč horizontálního dělení k identifikaci shardlet
* Přihlašovací údaje (uživatelské jméno a heslo) pro připojení k horizontálního oddílu

Objekt map horizontálního oddílu vytvoří připojení k horizontálního oddílu, který obsahuje shardlet pro danou horizontálního dělení klíč. Rozhraní API klienta elastické databáze také označovat připojení k implementaci jeho konzistence záruky. Od volání [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) vrátí regulární SQL objekt připojení klienta, následných volání **Execute** metoda rozšíření z Dapper dodržovat standardní Dapper postupem.

Dotazy na pracovní velmi mnohem stejným způsobem jako – poprvé otevřete připojení pomocí [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) z klientského rozhraní API. Potom můžete pomocí regulární Dapper rozšiřující metody mapovat na objekty .NET výsledky dotazu SQL:

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

Všimněte si, že **pomocí** blokovat pomocí oborů připojení DDR všechny databázové operace v rámci blok, který má jeden horizontálního oddílu, kde je udržována tenantId1. Dotaz vrátí jenom blogy uložené na aktuální horizontálního oddílu, ale není těm, které jsou uložené na jiné horizontálních oddílů. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Závislé na data směrování s Dapper a DapperExtensions
Dapper se dodává s prostředí další rozšíření, které můžete zadat další pohodlí a abstrakce z databáze při vývoji aplikace databáze. DapperExtensions je příklad. 

Použití DapperExtensions v aplikaci se nezměnil způsob vytváření a spravovaná databázová připojení. Je stále odpovědnost aplikace k otevření připojení a regulární objekty připojení klienta SQL se očekává metodami rozšíření. Spoléháme na [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) jak je uvedeno výš. Jak zobrazit následující ukázky kódu, jedinou změnou je, že už máte k zápisu příkazů T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

A zde je ukázka kódu pro dotaz: 

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

### <a name="handling-transient-faults"></a>Zpracování přechodných chyb
Tým postupy společnosti Microsoft Patterns publikována [přechodné chyby zpracování bloku aplikace](http://msdn.microsoft.com/library/hh680934.aspx) , což vývojářům aplikací zmírnit běžné podmínky přechodná chyba došlo při spuštění v cloudu. Další informace najdete v tématu [Perseverance, tajný klíč všechny vítězství: použití bloku aplikace zpracování přechodné chyby](http://msdn.microsoft.com/library/dn440719.aspx).

Ukázka kódu spoléhá na knihovnu přechodná chyba k ochraně proti přechodných. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** ve výše uvedeném kódu je definován jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací dobu mezi opakovanými pokusy. Pokud používáte transakce, ujistěte se, že váš rozsah opakování přejde zpět na začátek v případě přechodná chyba transakce.

## <a name="limitations"></a>Omezení
Přístupy uvedených v tomto dokumentu za následek několik omezení:

* Ukázkový kód pro tento dokument není ukazují, jak spravovat schématu v rámci horizontálních oddílů.
* Zadaný požadavek, předpokládáme, že všechny jeho zpracování databáze je obsažena v jedné horizontálního oddílu určeno klíčem horizontálního dělení poskytované žádost. Však tento předpoklad vždy nemá, například když není možné zpřístupnit klíč horizontálního dělení. Chcete-li to vyřešit, zahrnuje klientské knihovny pro elastické databáze [MultiShardQuery třída](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Třída implementuje abstraktní připojení pro dotazování přes několik horizontálních oddílů. Použití MultiShardQuery v kombinaci s Dapper je nad rámec tohoto dokumentu.

## <a name="conclusion"></a>Závěr
Aplikace s použitím Dapper a DapperExtensions můžete snadno využívat nástroje elastické databáze pro databázi SQL Azure. Tyto aplikace pomocí kroků uvedených v tomto dokumentu, můžete použít funkce nástroje pro závislé na data směrování změnou vytváření a otevírání nových [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekty, které chcete použít [ OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) volání Klientská knihovna pro elastické databáze. Toto nastavení omezuje změny aplikace, které jsou potřeba těchto míst, kde jsou vytvořen a otevřít nové připojení. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
