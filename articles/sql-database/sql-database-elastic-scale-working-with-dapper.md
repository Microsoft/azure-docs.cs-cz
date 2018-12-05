---
title: Používání klientské knihovny pro elastické databáze s Dapperem | Dokumentace Microsoftu
description: Používání klientské knihovny pro elastické databáze s Dapperem.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 14eb92141a9d27d9f8978abb6d5c9a738c821ead
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866300"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Používání klientské knihovny pro elastické databáze s Dapperem
Tento dokument je pro vývojáře, kteří využívají Dapperem k vytváření aplikací, ale také chtít využívat [nástrojů elastic database](sql-database-elastic-scale-introduction.md) k vytvoření tohoto horizontálního dělení implementují pro horizontální navýšení kapacity datovou vrstvu aplikace.  Tento dokument ukazuje změny v aplikacích Dapperem, které jsou potřebné k integraci s nástroji elastic database. Našim hlavním cílem je sestavování, Správa elastických databází horizontálních oddílů a směrování závislé na datech s Dapperem. 

**Ukázkový kód**: [nástrojů Elastic database pro Azure SQL Database – Dapper integrace](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrace **Dapperem** a **DapperExtensions** službou elastic database klientské knihovny pro službu Azure SQL Database je snadné. Aplikace může použít směrování závislé na datech změnou vytváření a otevírání nových [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektů na používání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) volání z [klientské knihovny ](https://msdn.microsoft.com/library/azure/dn765902.aspx). Toto nastavení omezuje změny ve vaší aplikaci pouze tam, kde jsou vytvořeny a otevřít nové připojení. 

## <a name="dapper-overview"></a>Přehled dapper
**Dapper** je objektově relační Mapovač. Mapuje se objekty .NET z vaší aplikace do relační databáze (a naopak). První část vzorový kód ukazuje, jak integrovat Klientská knihovna elastic database s Dapperem aplikace. Druhá část vzorový kód ukazuje, jak integrovat při použití Dapperem a DapperExtensions.  

Mapování funkce v Dapperem poskytuje rozšiřující metody u připojení k databázi, které usnadňují odesílání příkazů T-SQL pro zpracování nebo dotazování na databázi. Například Dapperem usnadňuje mapování mezi objekty .NET a parametrů příkazů SQL pro **Execute** volání, nebo využívat výsledky svých dotazů SQL do objektů .NET pomocí **dotazu** volání z Dapperem. 

Když používáte DapperExtensions, musíte už poskytují příkazy SQL. Metody rozšíření jako **GetList** nebo **vložit** přes připojení k databázi vytvořit příkazy SQL na pozadí.

Další výhodou Dapperem a také DapperExtensions je, že aplikace řídí vytváření připojení k databázi. Díky tomu interakci s Klientská knihovna elastic database, která můžou být zprostředkovatelé připojení na základě mapování shardletů k databázím databáze.

Pokud chcete získat Dapper sestavení, naleznete v tématu [Dapper dot net](http://www.nuget.org/packages/Dapper/). Dapper rozšíření, naleznete v tématu [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Rychlý pohled na Klientská knihovna elastic database
Pomocí Klientská knihovna elastic database definujete oddílů dat ve vašich aplikacích nazývá *shardletů*mapovat k databázím a poznají podle *klíče horizontálního dělení*. Může mít libovolný počet databází, podle potřeby a distribuci vašich shardletů v těchto databázích. Mapování hodnot klíče horizontálního dělení do databází se ukládá pomocí mapy horizontálních oddílů poskytuje knihovny rozhraní API. Tato funkce je volána **správy mapování horizontálních oddílů**. Mapy horizontálních oddílů slouží také jako zprostředkovatel připojení k databázi pro požadavky, které mají klíč horizontálního dělení. Tato funkce se označuje jako **směrování závislé na datech**.

![Směrování závislé na datech a mapy horizontálních oddílů][1]

Správce mapování horizontálních oddílů uživatelé chrání před konzistentní zobrazení do shardletu data, která může dojít, když operace správy souběžných shardletu se dějí v databázích. Uděláte to tak, zprostředkovatele mapy horizontálních oddílů připojení k databázi pro aplikaci sestavenou pomocí knihovny. Při operacích správy horizontálních oddílů může mít vliv shardletu, díky tomu funkce mapy horizontálních oddílů automaticky ukončit připojení k databázi. 

Nemusíte používat tradiční způsob, jak vytvořit připojení pro Dapperem, budete muset použít [OpenConnectionForKey metoda](https://msdn.microsoft.com/library/azure/dn824099.aspx). Tím se zajistí, že všechny ověřování probíhá a připojení se spravují správně, pokud žádná data se pohybuje mezi horizontálními oddíly.

### <a name="requirements-for-dapper-integration"></a>Požadavky pro integraci Dapper
Při práci s Klientská knihovna elastic database a Dapper rozhraní API, budete chtít zachovat následující vlastnosti:

* **Horizontální navýšení kapacity**: chcete přidat nebo odebrat databází v horizontálně dělené aplikace podle potřeby pro požadavky kapacity aplikace datové vrstvy. 
* **Konzistence**: vzhledem k tomu, aplikace bude škálovat pomocí horizontálního dělení, je potřeba provést směrování závislé na datech. Chceme použít k tomu možnosti směrování závislé na datech z knihovny. Zejména chcete zachovat ověření a záruky konzistence poskytuje připojení, která jsou zprostředkované přes správce mapování horizontálních oddílů Pokud se chcete vyhnout poškození nebo nesprávné výsledků. Tím se zajistí, že se připojení k dané shardletu Zamítnutá nebo zastaven, pokud (například) shardletu aktuálně přesunout do jiného horizontálního oddílu pomocí rozhraní API pro dělení a slučování.
* **Mapování objektu**: Chcete zachovat pohodlí mapování poskytované Dapperem pro převod mezi tříd v aplikaci a základní struktury databáze. 

Následující část obsahuje pokyny pro tyto požadavky pro aplikace na základě **Dapperem** a **DapperExtensions**.

## <a name="technical-guidance"></a>Technické pokyny
### <a name="data-dependent-routing-with-dapper"></a>Směrování závislé na datech s Dapperem
Aplikace s Dapperem, je obvykle zodpovědný za vytváření a otevírání připojení k podkladové databázi. Daný typ T aplikací, Dapperem vrátí výsledky dotazu jako kolekce .NET typu T. Dapper provede mapování z výsledné řádky T-SQL pro objekty typu T. Obdobně Dapperem mapuje objektů .NET do SQL hodnoty nebo parametry pro příkazy data manipulaci s language (DML). Dapperem nabízí tyto funkce prostřednictvím rozšíření metod na standardní [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt ADO .NET SQL klientské knihovny. Připojení SQL vrátil rozhraní API pružné škálování pro záznam DDR jsou také pravidelné [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekty. To nám umožňuje přímo používat Dapper rozšíření nad typem vrácená rozhraním API DDR klientskou knihovnu, jako je také jednoduchý připojení klienta SQL.

Tyto poznámky zjednodušují připojení zprostředkovaných podle Klientská knihovna elastic database pro Dapperem.

Tento příklad kódu (z doprovodných ukázky) ukazuje přístupu, kde je klíč horizontálního dělení poskytnutý aplikací ke knihovně pro zprostředkování připojení ke správné horizontálních oddílů.   

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

Volání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API nahradí výchozí vytváření a otevírání připojení klienta SQL. [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) volání přijímá argumenty, které jsou požadovány pro směrování závislé na datech: 

* Mapy horizontálních oddílů pro přístup k rozhraní směrování závislé na datech
* Klíč horizontálního dělení k identifikaci shardletu
* Přihlašovací údaje (uživatelské jméno a heslo) pro připojení do horizontálního oddílu

Objekt map horizontálních oddílů vytvoří připojení do horizontálního oddílu, který má shardletu pro klíč horizontálního dělení dané. Elastické databáze klientských rozhraní API také označovat připojení k implementaci jeho záruky konzistence. Od posledního volání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) vrátí regulární objekt klienta systému SQL připojení, následné volání **Execute** rozšiřující metoda z Dapperem dodržuje standardní praxí Dapper.

Dotazy fungují velmi podobně jako když stejné – prvním otevření připojení pomocí [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) z klientského rozhraní API. Pak můžete pomocí regulárních Dapper rozšiřující metody do objektů .NET mapovat výsledky dotazu SQL:

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

Všimněte si, že **pomocí** blokovat všechny operace databáze v rámci bloku do jednoho horizontálního oddílu, ve kterém se ukládají tenantId1 s obory připojení DDR. Dotaz vrátí pouze blogy uložené na aktuální horizontálního oddílu, ale ne těch, které jsou uložené na jiných horizontálních oddílů. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Směrování závislé na datech s Dapperem a DapperExtensions
Dapperem se dodává s ekosystémem další rozšíření, které můžete zadat další abstrakce z databáze a pohodlí při vytváření databázových aplikací. DapperExtensions je příklad. 

Ve vaší aplikaci pomocí DapperExtensions nemění způsob vytvoření a Správa připojení k databázi. Zodpovídá za stále aplikace k otevření připojení a pravidelné objekty připojení klienta SQL se očekává metodami rozšíření. Spoléháme na [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) jak je uvedeno výš. Jak ukazují následující ukázky kódu, pouze změny je, že už máte psát příkazy jazyka T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

A tady je příklad dotazu: 

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
Tým Microsoft Patterns a postupy publikované [přechodné Fault Handling Application Block](https://msdn.microsoft.com/library/hh680934.aspx) pomáhá vývojářům aplikací zmírnit běžné stavy přechodných chyb došlo při spuštění v cloudu. Další informace najdete v tématu [Perseverance, tajný klíč všechny vítězství: použití blok aplikací zpracování přechodných selhání](https://msdn.microsoft.com/library/dn440719.aspx).

Vzorový kód spoléhá na knihovny přechodných chyb pro ochranu proti přechodným chybám. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** ve výše uvedeném kódu je definován jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací dobu mezi opakovanými pokusy. Pokud používáte transakce, ujistěte se, že váš rozsah opakování přejde zpět na začátek transakce v případě přechodných chyb.

## <a name="limitations"></a>Omezení
Přístupů popsaných v tomto dokumentu zahrnuje několik omezení:

* Ukázkový kód pro tento dokument není ukazují, jak spravovat schéma napříč horizontálními oddíly.
* Zadaný požadavek, předpokládáme, že všechny její zpracování databáze je součástí jeden horizontální oddíl pomocí klíče horizontálního dělení zadané v požadavku. Ale tento předpoklad vždy neobsahuje, například když není možné zpřístupnit klíč horizontálního dělení. Z toho Klientská knihovna elastic database zahrnuje [MultiShardQuery třídy](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Třída implementuje abstraktní připojení pro dotazování více horizontálních oddílů. Použití MultiShardQuery v kombinaci s Dapperem je nad rámec tohoto dokumentu.

## <a name="conclusion"></a>Závěr
Aplikace používající Dapperem nebo DapperExtensions snadno využívat nástrojů elastic database pro Azure SQL Database. Pomocí kroků uvedených v tomto dokumentu, tyto aplikace můžete použít funkci nástroje pro směrování závislé na datech změnou vytváření a otevírání nových [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektů na používání [ OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) volání Klientská knihovna elastic database. To omezuje změny aplikace, které jsou potřeba těchto míst, kde jsou vytvořen a otevřít nové připojení. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
