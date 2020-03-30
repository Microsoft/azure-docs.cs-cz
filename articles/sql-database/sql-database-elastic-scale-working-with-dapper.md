---
title: Použití klientské knihovny elastické databáze s aplikací Dapper
description: Použití klientské knihovny elastické databáze s Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 83d24d45d7628a2e02068c8757fa6568d6d3fc37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823477"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Použití klientské knihovny elastické databáze s aplikací Dapper
Tento dokument je určen pro vývojáře, kteří spoléhají na Dapper vytvářet aplikace, ale také chcete přijmout [elastické databázové nástroje](sql-database-elastic-scale-introduction.md) k vytvoření aplikací, které implementují horizontálního oddílu pro horizontální navýšení kapacity jejich datové vrstvy.  Tento dokument ilustruje změny v aplikacích založených na Dapper, které jsou nezbytné pro integraci s elastické databázové nástroje. Zaměřujeme se na vytváření správy síte elastické databáze a směrování závislé na datech s Dapper. 

**Ukázkový kód:** [Elastické databázové nástroje pro Azure SQL Database – integrace Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrace **Dapper** a **DapperExtensions** s klientskou knihovnou elastické databáze pro Azure SQL Database je snadná. Vaše aplikace mohou používat směrování závislé na datech změnou vytvoření a otevření nových objektů [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) pro použití volání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) z [klientské knihovny](https://msdn.microsoft.com/library/azure/dn765902.aspx). To omezuje změny v aplikaci pouze tam, kde jsou vytvořena a otevřena nová připojení. 

## <a name="dapper-overview"></a>Přehled aplikace Dapper
**Dapper** je objekt-relační mapper. Mapuje objekty .NET z vaší aplikace do relační databáze (a naopak). První část ukázkového kódu ukazuje, jak můžete integrovat klientskou knihovnu elastické databáze s aplikacemi založenými na Dapperu. Druhá část ukázkového kódu ukazuje, jak integrovat při použití Dapper a DapperExtensions.  

Funkce mapovače v Dapper poskytuje metody rozšíření na připojení databáze, které zjednodušují odesílání Příkazů T-SQL pro spuštění nebo dotazování na databázi. Například Dapper usnadňuje mapování mezi objekty .NET a parametry příkazů SQL pro **volání execute** nebo spotřebovávají výsledky dotazů SQL do objektů .NET pomocí volání **dotazu** z Dapper. 

Při použití DapperExtensions již není nutné poskytovat příkazy SQL. Metody rozšíření, jako je **Například GetList** nebo **Vložit** přes připojení k databázi vytvořit příkazy SQL na pozadí.

Další výhodou Dapper a také DapperExtensions je, že aplikace řídí vytváření připojení k databázi. To pomáhá pracovat s klientskou knihovnou elastické databáze, která zprostředkovává připojení k databázi na základě mapování shardlets na databáze.

Chcete-li získat sestavy Dapper, viz [Dapper dot net](https://www.nuget.org/packages/Dapper/). Rozšíření Dapper naleznete v tématu [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Rychlý pohled na klientskou knihovnu elastické databáze
Pomocí klientské knihovny elastické databáze definujete oddíly dat aplikace nazývané *shardlety*, namapujete je na databáze a identifikujete je *pomocí klíčů dělení*. Můžete mít tolik databází, kolik potřebujete, a distribuovat vaše shardlety mezi těmito databázemi. Mapování hodnot klíče s ráždí do databází je uloženo pomocí mapy skládek poskytované rozhraní API knihovny. Tato funkce se nazývá **správa mapy síní**. Mapa střepu slouží také jako zprostředkovatel připojení databáze pro požadavky, které nesou klíč s ráždí. Tato funkce se označuje jako **směrování závislé na datech**.

![Mapy úlomků a směrování závislé na datech][1]

Správce mapy střepů chrání uživatele před nekonzistentními zobrazeními do dat shardletu, ke kterým může dojít, když se v databázích vyskytují souběžné operace správy shardletu. Chcete-li tak učinit, mapy střepu zprostředkovávat připojení databáze pro aplikaci vytvořenou s knihovnou. Když operace správy střepů může mít vliv na shardlet, to umožňuje funkce mapy střep automaticky zabít připojení k databázi. 

Namísto použití tradičního způsobu vytváření připojení pro Dapper je třeba použít [metodu OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx). Tím je zajištěno, že probíhá všechna ověření a připojení jsou správně spravovány při přesunu dat mezi oddíly.

### <a name="requirements-for-dapper-integration"></a>Požadavky na integraci Dapper
Při práci s klientskou knihovnou elastické databáze a rozhraními API Dapper chcete zachovat následující vlastnosti:

* **Horizontální navýšení kapacity**: Chceme přidat nebo odebrat databáze z datové vrstvy horizontálně se ale řAZované aplikace podle potřeby pro požadavky na kapacitu aplikace. 
* **Konzistence**: Vzhledem k tomu, že aplikace je škálovat pomocí horizontálního horizontu, je třeba provést směrování závislé na datech. Chceme k tomu využít možnosti směrování závislé na datech knihovny. Zejména chcete zachovat záruky ověření a konzistence poskytované připojení, které jsou zprostředkovány prostřednictvím správce mapy střepů, aby se zabránilo poškození nebo chybné výsledky dotazu. Tím je zajištěno, že připojení k dané shardlet jsou odmítnuty nebo zastaveny, pokud (například) shardlet je aktuálně přesunuta do jiného oddílu pomocí split/merge API.
* **Mapování objektů**: Chceme zachovat pohodlí mapování poskytované Dapper překládat mezi třídami v aplikaci a základní databázové struktury. 

Následující část obsahuje pokyny pro tyto požadavky pro aplikace založené na **Dapper** a **DapperExtensions**.

## <a name="technical-guidance"></a>Technické pokyny
### <a name="data-dependent-routing-with-dapper"></a>Směrování závislé na datech s dapperem
S Dapper aplikace je obvykle zodpovědný za vytváření a otevírání připojení k podkladové databázi. Daný typ T aplikací Dapper vrátí výsledky dotazu jako kolekce .NET typu T. Dapper provádí mapování z řádků výsledku T-SQL na objekty typu T. Podobně Dapper mapuje objekty .NET na hodnoty SQL nebo parametry pro příkazy jazyka dml (manipulace s daty). Dapper nabízí tuto funkci prostřednictvím rozšiřujících metod na běžném objektu [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) z knihoven ADO .NET SQL Client. Připojení SQL vrácené elastické škálování API pro DDR jsou také běžné [sqlconnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekty. To nám umožňuje přímo používat rozšíření Dapper nad typem vráceným rozhraním DDR API klientské knihovny, protože se jedná také o jednoduché připojení klienta SQL.

Tato pozorování usnadňují použití připojení zprostředkované klientskou knihovnou elastické databáze pro Dapper.

Tento příklad kódu (z doprovodné ukázky) ilustruje přístup, kde je klíč srážlivosti poskytované aplikací do knihovny zprostředkovat připojení k pravému oddílu.   

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

Volání rozhraní [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API nahrazuje výchozí vytvoření a otevření připojení klienta SQL. Volání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) přebírá argumenty, které jsou požadovány pro směrování závislé na datech: 

* Mapa střepů pro přístup k rozhraním směrování závislých na datech
* Klíč sharding k identifikaci shardlet
* Přihlašovací údaje (uživatelské jméno a heslo) pro připojení ke střepu

Objekt mapy šněrovacího oddílu vytvoří připojení k oddílu, který drží shardlet pro daný klíč nástružníku. Elastické databáze klienta API také označit připojení k implementaci jeho záruky konzistence. Vzhledem k tomu, že volání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) vrátí objekt připojení běžného klienta SQL, následné volání metody **rozšíření Execute** z Dapper následuje standardní dapper praxi.

Dotazy fungují velmi podobně – nejprve otevřete připojení pomocí [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) z klientského rozhraní API. Potom použijete běžné metody rozšíření Dapper k mapování výsledků dotazu SQL na objekty .NET:

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

Všimněte si, že **using** blok s rozsahy připojení DDR všechny databázové operace v rámci bloku na jeden úlomek, kde tenantId1 je zachována. Dotaz vrátí pouze blogy uložené na aktuální mříži, ale ne ty uložené na jiných šiřících dřících. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Směrování závislé na datech s Dapper a DapperExtensions
Dapper přichází s ekosystémem dalších rozšíření, které mohou poskytnout další pohodlí a abstrakci z databáze při vývoji databázových aplikací. Příkladem je DapperExtensions. 

Použití DapperExtensions ve vaší aplikaci nezmění způsob vytváření a spravování připojení databáze. Je stále odpovědností aplikace otevřít připojení a běžné objekty připojení klienta SQL jsou očekávány metody rozšíření. Můžeme se spolehnout na [OpenConnectionForKey,](https://msdn.microsoft.com/library/azure/dn807226.aspx) jak je uvedeno výše. Jak ukazují následující ukázky kódu, jedinou změnou je, že již nemusíte psát příkazy T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

A tady je ukázka kódu pro dotaz: 

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

### <a name="handling-transient-faults"></a>Zpracování přechodných poruch
Tým Microsoft Patterns & Practices publikoval [blok aplikací pro zpracování přechodných chyb,](https://msdn.microsoft.com/library/hh680934.aspx) aby vývojářům aplikací pomohl zmírnit běžné přechodné chybové stavy, ke kterým došlo při spuštění v cloudu. Další informace naleznete [v tématu Vytrvalost, tajemství všech triumfů: Použití](https://msdn.microsoft.com/library/dn440719.aspx)bloku aplikace pro zpracování přechodných chyb .

Ukázka kódu závisí na knihovně přechodných chyb, která chrání před přechodovými chybami. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** ve výše uvedeném kódu je definován jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací doby mezi opakování. Pokud používáte transakce, ujistěte se, že rozsah opakování se vrátí na začátek transakce v případě přechodné chyby.

## <a name="limitations"></a>Omezení
Přístupy uvedené v tomto dokumentu s sebou nesou několik omezení:

* Ukázkový kód pro tento dokument neukazuje, jak spravovat schéma mezi oddíly šikřítek.
* Daný požadavek, předpokládáme, že všechny jeho zpracování databáze je obsažena v rámci jednoho oddílu, jak je identifikován klíč sharding poskytované požadavek. Tento předpoklad však není vždy držet, například pokud není možné zpřístupnit klíč srážlivosti. Chcete-li tento problém vyřešit, knihovna klienta elastické databáze obsahuje [třídu MultiShardQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Třída implementuje abstrakce připojení pro dotazování přes několik šmejdů. Použití MultiShardQuery v kombinaci s Dapper je nad rámec tohoto dokumentu.

## <a name="conclusion"></a>Závěr
Aplikace používající Dapper a DapperExtensions můžou snadno těžit z elastických databázových nástrojů pro Azure SQL Database. Prostřednictvím kroků popsaných v tomto dokumentu mohou tyto aplikace použít funkci nástroje pro směrování závislé na datech změnou vytvoření a otevření nových objektů [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) pro použití volání [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) klientské knihovny elastické databáze. To omezuje změny aplikace požadované na místa, kde jsou vytvořena a otevřena nová připojení. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
