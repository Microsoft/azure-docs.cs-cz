---
title: Začínáme s nástroji elastické databáze
description: Základní vysvětlení funkce Nástroje elastické databáze azure SQL database, včetně snadno spustit ukázkové aplikace.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987018"
---
# <a name="get-started-with-elastic-database-tools"></a>Začínáme s nástroji elastické databáze

Tento dokument vás seznámí s prostředím pro vývojáře pro [klientskou knihovnu elastické databáze](sql-database-elastic-database-client-library.md) tím, že vám pomůže spustit ukázkovou aplikaci. Ukázková aplikace vytvoří jednoduchou roztřepenou aplikaci a prozkoumá klíčové funkce funkce Nástroje elastické databáze azure SQL database. Zaměřuje se na případy použití pro [správu mapy síta](sql-database-elastic-scale-shard-map-management.md), [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md)a [dotazování s více oddíly](sql-database-elastic-scale-multishard-querying.md). Klientská knihovna je k dispozici pro rozhraní .NET i jazykJava. 

## <a name="elastic-database-tools-for-java"></a>Elastické databázové nástroje pro Javu

### <a name="prerequisites"></a>Požadavky

* Java Developer Kit (JDK), verze 1.8 nebo novější
* [Maven](https://maven.apache.org/download.cgi)
* Databázový server SQL v Azure nebo místní instance serveru SQL Server

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace

Chcete-li vytvořit soubory JAR a začít s ukázkovým projektem, postupujte takto: 
1. Klonujte [úložiště GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) obsahující klientskou knihovnu spolu s ukázkovou aplikací. 

2. Upravte soubor _./sample/src/main/resources/resource.properties_ a nastavte následující:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Chcete-li vytvořit ukázkový projekt, spusťte v adresáři _./sample_ následující příkaz:

    ```
    mvn install
    ```
    
4. Chcete-li spustit ukázkový projekt, spusťte v adresáři _./sample_ následující příkaz: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Chcete-li se dozvědět více o možnostech klientské knihovny, experimentujte s různými možnostmi. Neváhejte a prozkoumejte kód a dozvíte se o implementaci ukázkové aplikace.

    ![Progress-java][5]
    
Blahopřejeme! Úspěšně jste vytvořili a spusťte první horizontově rozlišenou aplikaci pomocí nástrojů elastické databáze v Azure SQL Database. Pomocí sady Visual Studio nebo sql server Management Studio se připojit k databázi SQL a rychle se podívat na úlomky, které vzorek vytvořil. Zaznamenáte si nové ukázkové databáze střepů a databázi správce map svižné části, kterou vzorek vytvořil. 

Chcete-li přidat klientskou knihovnu do vlastního projektu Maven, přidejte do souboru POM následující závislost:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Nástroje elastické databáze pro rozhraní .NET

### <a name="prerequisites"></a>Požadavky

* Visual Studio 2012 nebo novější s c#. Stáhněte si bezplatnou verzi v [sadě Visual Studio ke stažení](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 nebo novější. Nejnovější verzi najdete v [tématu Instalace nugetu](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace

Chcete-li knihovnu nainstalovat, přejděte na [web Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Knihovna je nainstalovaná s ukázkovou aplikací, která je popsána v následující části.

Chcete-li stáhnout a spustit ukázku, postupujte takto: 

1. Stáhněte si [ukázku Nástroje elastická DB pro Azure SQL – začínáme](https://github.com/Azure/elastic-db-tools). Rozbalte ukázku do zvoleného umístění.

2. Chcete-li vytvořit projekt, otevřete řešení *ElasticScaleStarterKit.sln* z adresáře *C#.*

3. V řešení ukázkového projektu otevřete soubor *app.config.* Potom podle pokynů v souboru přidejte název serveru Azure SQL Database a přihlašovací informace (uživatelské jméno a heslo).

4. Sestavte a spusťte aplikaci. Po zobrazení výzvy povolte Visual Studio obnovit NuGet balíčky řešení. Tato akce stáhne nejnovější verzi knihovny klienta elastické databáze z NuGet.

5. Chcete-li se dozvědět více o možnostech klientské knihovny, experimentujte s různými možnostmi. Všimněte si kroků, které aplikace provede ve výstupu konzoly a nebojte se prozkoumat kód na pozadí.
   
    ![Průběh][4]

Blahopřejeme! Úspěšně jste vytvořili a spusťte první horizontově rozlišenou aplikaci pomocí nástrojů elastické databáze v databázi SQL. Pomocí sady Visual Studio nebo sql server Management Studio se připojit k databázi SQL a rychle se podívat na úlomky, které vzorek vytvořil. Zaznamenáte si nové ukázkové databáze střepů a databázi správce map svižné části, kterou vzorek vytvořil.

> [!IMPORTANT]
> Doporučujeme vždy používat nejnovější verzi Sady Management Studio, abyste zůstali synchronizovaní s aktualizacemi Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Klíčové části ukázky kódu

* **Správa štrůdků a map štrůdků**: Kód ukazuje, jak pracovat s úlomky, rozsahy a mapování v *souboru ShardManagementUtils.cs.* Další informace naleznete v [tématu Horizontální navýšení kapacity databází pomocí správce mapy horizontálního oddílu](https://go.microsoft.com/?linkid=9862595).  

* **Směrování závislé na datech**: Směrování transakcí na pravý úlomek je zobrazeno v *souboru DataDependentRoutingSample.cs.* Další informace naleznete v [tématu Směrování závislé na datech](https://go.microsoft.com/?linkid=9862596). 

* **Dotazování přes více úlomků**: Dotazování napříč úlomky je znázorněno v *souboru MultiShardQuerySample.cs.* Další informace naleznete [v tématu Dotazování na více oddílů](https://go.microsoft.com/?linkid=9862597).

* **Přidání prázdných úlomků**: Iterativní přidání nových prázdných štřepů provádí kód v *CreateShardSample.cs* souboru. Další informace naleznete v [tématu Horizontální navýšení kapacity databází pomocí správce mapy horizontálního oddílu](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Jiné operace elastického rozsahu

* **Rozdělení existujícího šněrového štěpení**: Schopnost rozdělit úlomky je poskytována nástrojem rozdělení sloučení. Další informace naleznete v [tématu Přesouvání dat mezi cloudovými databázemi s horizontálním navýšením kapacity](sql-database-elastic-scale-overview-split-and-merge.md).

* **Sloučení existujících úlomků**: Sloučení úlomků se provádí také pomocí nástroje rozdělení sloučení. Další informace naleznete v [tématu Přesouvání dat mezi cloudovými databázemi s horizontálním navýšením kapacity](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Náklady

Knihovna Elastic Database Tools je zdarma. Při použití nástrojů elastické databáze vám nevznikají žádné další poplatky nad rámec nákladů na využití Azure. 

Ukázková aplikace například vytvoří nové databáze. Náklady na tuto funkci závisí na sql database edition, kterou zvolíte a využití Azure vaší aplikace.

Informace o cenách naleznete v [tématu PODROBNOSTI o cenách databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další kroky

Další informace o nástrojích elastické databáze naleznete v následujících článcích:

* Ukázky kódu: 
  * Nástroje pro elastické databáze ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastické databázové nástroje pro Azure SQL – integrace entity frameworku](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticita štěřídla v centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Oznámení o elastické stupnici](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanál 9: [Video s přehledem elastické škály](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskusní fórum: [Fórum Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Měření výkonu: [Čítače výkonu pro správce mapy skřípků](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

