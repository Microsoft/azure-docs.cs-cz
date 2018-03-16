---
title: "Začínáme s nástroji elastické databáze – Azure | Microsoft Docs"
description: "Základní vysvětlení funkce nástroje pro elastické databáze Azure SQL Database, včetně Snadné spuštění ukázkové aplikace."
services: sql-database
manager: craigg
author: anumjs
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: 28ff3f6eee2316a078badcf29e6780f3844f3a54
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-elastic-database-tools"></a>Začínáme s nástroji elastické databáze
Tento dokument vás seznámí s možnosti pro vývojáře [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md) tím, že pomáhá spustíte ukázkovou aplikaci. Ukázková aplikace vytvoří jednoduchou aplikaci horizontálně dělené a jsou zde popsány klíčové funkce funkce nástroje pro elastické databáze Azure SQL Database. Zaměřuje se na případy použití pro [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md), [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md), a [víc horizontálních dotazování](sql-database-elastic-scale-multishard-querying.md). Klientská knihovna je k dispozici pro rozhraní .NET, jakož i Java. 

## <a name="elastic-database-tools-for-java"></a>Nástroje elastické databáze pro jazyk Java
### <a name="prerequisites"></a>Požadavky
* Java Developer Kit (JDK), verze 1,8 nebo novější
* [Maven](http://maven.apache.org/download.cgi)
* Logický server v Azure nebo místní instanci systému SQL Server

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace
Sestavení souborů JAR a začít pracovat s ukázkovým projektem, postupujte takto: 
1. Klon [úložiště GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) obsahující klientské knihovny, společně s ukázkovou aplikaci. 

2. Upravit _./sample/src/main/resources/resource.properties_ souboru můžete nastavit tyto:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. K vytvoření ukázkového projektu, ve _. / ukázkové_ adresáře, spusťte následující příkaz:

    ```
    mvn install
    ```
    
4. Chcete-li spustit ukázkový projekt v _. / ukázkové_ adresáře, spusťte následující příkaz: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Další informace o možnostech knihovny klienta Experimentujte s různými možnostmi. Nebojte se, že prozkoumat kód Další informace o implementaci ukázkové aplikace.

    ![Progress-java][5]
    
Blahopřejeme! Úspěšně jste vytvořené a spuštění vaší první horizontálně dělené aplikace pomocí nástrojů pro elastické databáze v databázi SQL Azure. Pomocí sady Visual Studio nebo SQL Server Management Studio k připojení k vaší databázi SQL a rychle zobrazit v horizontálních oddílů, vytvořené v ukázce. Všimnete nové ukázkové databáze horizontálního oddílu a databázi správce mapy horizontálního oddílu, které vytvořil vzorku. 

Pokud chcete přidat do projektu Maven klientské knihovny, přidejte následující závislost v souboru POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Nástroje elastické databáze pro .NET 
### <a name="prerequisites"></a>Požadavky
* Visual Studio 2012 nebo novějším s C#. Stáhněte si bezplatnou verzi na [Visual Studio stáhne](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 nebo vyšší. Chcete-li získat nejnovější verzi, přečtěte si téma [instalace NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace
Chcete-li nainstalovat knihovny, přejděte na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Knihovny se instaluje s ukázkovou aplikaci, která je popsána v následující části.

Ke stažení a spuštění vzorového, postupujte takto: 

1. Stažení [elastické databáze nástroje pro Azure SQL – ukázka Začínáme](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) z webu MSDN. Rozbalte vzorku, který se umístění, které zvolíte.

2. Chcete-li vytvořit projekt, otevřete *ElasticScaleStarterKit.sln* řešení z *C#* adresáře.

3. V řešení pro ukázkový projekt, otevřete *app.config* souboru. Potom postupujte podle pokynů v souboru a přidejte název serveru Azure SQL Database a vaše přihlašovací údaje (uživatelské jméno a heslo).

4. Sestavte a spusťte aplikaci. Pokud se zobrazí výzva, povolte obnovení balíčků NuGet, řešení sady Visual Studio. Tato akce stáhne nejnovější verzi klientské knihovny pro elastické databáze NuGet.

5. Další informace o možnostech knihovny klienta Experimentujte s různými možnostmi. Poznamenejte si kroky, které aplikace provede v konzole pro výstup a Nebojte se prozkoumat kód na pozadí.
   
    ![Průběh][4]

Blahopřejeme! Úspěšně jste vytvořené a spuštění vaší první horizontálně dělené aplikace pomocí nástrojů pro elastické databáze v databázi SQL. Pomocí sady Visual Studio nebo SQL Server Management Studio k připojení k vaší databázi SQL a rychle zobrazit v horizontálních oddílů, vytvořené v ukázce. Všimnete nové ukázkové databáze horizontálního oddílu a databázi správce mapy horizontálního oddílu, které vytvořil vzorku.

> [!IMPORTANT]
> Doporučujeme, aby vám zůstaly synchronizované s aktualizacemi a Azure SQL Database vždy použít nejnovější verzi aplikace Management Studio. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Klíče kusy ukázka kódu
* **Správa horizontálních oddílů a horizontálního oddílu map**: kód ukazuje, jak pracovat s horizontálních oddílů, rozsahy a mapování v *ShardManagementUtils.cs* souboru. Další informace najdete v tématu [škálování databáze pomocí správce mapy horizontálního oddílu](http://go.microsoft.com/?linkid=9862595).  

* **Závislé na data směrování**: směrování transakce na pravém horizontálního oddílu se zobrazí v *DataDependentRoutingSample.cs* souboru. Další informace najdete v tématu [závislé na Data směrování](http://go.microsoft.com/?linkid=9862596). 

* **Dotazování přes víc horizontálních oddílů**: dotazování napříč horizontálních oddílů je znázorněn na následujícím *MultiShardQuerySample.cs* souboru. Další informace najdete v tématu [víc horizontálních dotazování](http://go.microsoft.com/?linkid=9862597).

* **Přidání prázdný horizontálních oddílů**: iterativní přidání nové prázdné horizontálních oddílů se provádí pomocí kódu v *CreateShardSample.cs* souboru. Další informace najdete v tématu [škálování databáze pomocí správce mapy horizontálního oddílu](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Jiné operace elastické škálování
* **Rozdělení existující horizontálního oddílu**: možnost Rozdělit horizontálních oddílů je poskytována nástrojem rozdělení sloučení. Další informace najdete v tématu [přesouvání dat mezi instancemi cloudu databázemi](sql-database-elastic-scale-overview-split-and-merge.md).

* **Slučování existující horizontálních oddílů**: sloučení horizontálního oddílu jsou také provést pomocí nástroje rozdělení sloučení. Další informace najdete v tématu [přesouvání dat mezi instancemi cloudu databázemi](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Náklady
Knihovna nástroje elastické databáze je zdarma. Při použití nástroje pro elastické databáze platit žádné další poplatky za náklady na vaše používání Azure. 

Ukázkové aplikace například vytvoří nové databáze. Náklady na tuto funkci závisí na edici SQL databáze, kterou zvolíte a Azure využití vaší aplikace.

Informace o cenách najdete v části [podrobnosti o cenách SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další postup
Další informace o nástrojích elastické databáze najdete v následujících článcích:

* Ukázky kódu: 
  * Nástroje elastické databáze ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Nástroje elastické databáze Azure SQL – integrace Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Pružnost horizontálního oddílu v Centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [elastické škálování oznámení](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [implementace Škálováním na více systémů pomocí horizontálního dělení s Video knihovny klienta elastické databáze](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Kanál 9: [elastické škálování přehled – video](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskusní fórum: [fórum Azure SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* K měření výkonu: [čítače výkonu pro správce horizontálního oddílu mapy](sql-database-elastic-database-client-library.md)

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

