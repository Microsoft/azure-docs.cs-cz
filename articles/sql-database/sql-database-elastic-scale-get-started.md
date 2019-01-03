---
title: Začínáme s Elastickými nástroji databáze – Azure | Dokumentace Microsoftu
description: Vysvětlení základní funkce nástroje pro elastické databáze Azure SQL Database, včetně Snadné spuštění ukázkové aplikace.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
manager: craigg
ms.date: 08/27/2018
ms.openlocfilehash: b3bdcc81776067f279c1f95458a0a79a8824f51c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603045"
---
# <a name="get-started-with-elastic-database-tools"></a>Začínáme s nástroji Elastic Database
Tento dokument vás seznámí se vývojářské prostředí pro [Klientská knihovna elastic database](sql-database-elastic-database-client-library.md) tím, že pomáhá spustíte ukázkovou aplikaci. Ukázková aplikace vytvoří jednoduchou horizontálně dělenou aplikaci a popisuje klíčové funkce funkci nástroje pro elastické databáze Azure SQL Database. Zaměřuje se na případy použití pro [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md), [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md), a [dotazování více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md). Klientská knihovna je k dispozici pro rozhraní .NET i Javu. 

## <a name="elastic-database-tools-for-java"></a>Nástroje pro elastické databáze pro Javu
### <a name="prerequisites"></a>Požadavky
* Java Developer Kit (JDK), verze 1.8 nebo vyšší
* [Maven](http://maven.apache.org/download.cgi)
* Logický server v Azure nebo místní instanci systému SQL Server

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace
Soubory JAR sestavení a začít pracovat s ukázkového projektu, postupujte takto: 
1. Klonování [úložiště GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) obsahující klientskou knihovnu, spolu s ukázkovou aplikací. 

2. Upravit _./sample/src/main/resources/resource.properties_ souboru můžete nastavit tyto:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. K vytvoření ukázkového projektu, v _. / sample_ adresáře, spusťte následující příkaz:

    ```
    mvn install
    ```
    
4. Spuštění ukázkového projektu a v _. / sample_ adresáře, spusťte následující příkaz: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Další informace o možnostech knihovna klienta, Experimentujte s různými možnostmi. Můžete prozkoumat kód pro další informace o ukázková implementace aplikace.

    ![Progress-java][5]
    
Blahopřejeme! Úspěšně jste sestavíte a spuštění vaší první horizontálně dělené aplikace s využitím nástroje Elastic Database pro Azure SQL Database. Pomocí sady Visual Studio nebo SQL Server Management Studio k připojení k SQL database a rychle zkontrolovat na horizontální oddíly vytvořené ukázky. Uvidíte nové ukázkové horizontálního dělení databáze a databáze správce mapování horizontálních oddílů, která ukázku vytvořila. 

Knihovna klienta přidat do projektu Maven, přidejte do souboru POM následující závislost:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Nástroje elastic Database pro .NET 
### <a name="prerequisites"></a>Požadavky
* Visual Studio 2012 nebo novějším s jazykem C#. Stáhněte si bezplatnou verzi na [stahování sady Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 nebo novější. Pokud chcete získat nejnovější verzi, najdete v článku [instalace balíčků NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Stažení a spuštění ukázkové aplikace
K instalaci knihovny, přejděte na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Knihovny se instaluje s ukázkovou aplikaci, která je popsána v následující části.

Ke stažení a spuštění ukázky, postupujte podle těchto kroků: 

1. Stáhněte si [elastické databáze nástroje pro Azure SQL – ukázka Začínáme](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) z webu MSDN. Rozbalte ukázku do umístění, které zvolíte.

2. Chcete-li vytvořit projekt, otevřete *ElasticScaleStarterKit.sln* řešení z *jazyka C#* adresáře.

3. V řešení pro ukázkový projekt, otevřete *app.config* souboru. Potom postupujte podle pokynů v souboru a přidejte název vašeho serveru Azure SQL Database a vaše přihlašovací údaje (uživatelské jméno a heslo).

4. Sestavte a spusťte aplikaci. Po zobrazení výzvy, umožní sadě Visual Studio pro obnovování balíčků NuGet řešení. Tato akce stáhne nejnovější verzi klientské knihovny elastic database NuGet.

5. Další informace o možnostech knihovna klienta, Experimentujte s různými možnostmi. Mějte na paměti, kroky, které aplikace přebírá v konzole výstupní a bez obav zkoumání kódu na pozadí.
   
    ![Průběh][4]

Blahopřejeme! Úspěšně jste sestavíte a spuštění vaší první horizontálně dělené aplikace s využitím nástroje Elastic Database v SQL Database. Pomocí sady Visual Studio nebo SQL Server Management Studio k připojení k SQL database a rychle zkontrolovat na horizontální oddíly vytvořené ukázky. Uvidíte nové ukázkové horizontálního dělení databáze a databáze správce mapování horizontálních oddílů, která ukázku vytvořila.

> [!IMPORTANT]
> Doporučujeme vám, takže můžete zůstat synchronizováni s aktualizacemi služeb Azure a SQL Database vždy používat nejnovější verzi aplikace Management Studio. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Klíčových součástí ukázkového kódu
* **Správa horizontálních oddílů a horizontální oddíl map**: Kód ukazuje, jak pracovat s horizontálními oddíly, rozsahy a mapování v *ShardManagementUtils.cs* souboru. Další informace najdete v tématu [horizontální navýšení kapacity pomocí Správce mapování horizontálních oddílů databáze](https://go.microsoft.com/?linkid=9862595).  

* **Směrování závislé na datech**: Směrování transakce do správné horizontálního oddílu je zobrazena ve *DataDependentRoutingSample.cs* souboru. Další informace najdete v tématu [směrování závislé na datech](https://go.microsoft.com/?linkid=9862596). 

* **Dotazování více horizontálních oddílů**: Dotazování napříč horizontálními oddíly je znázorněno *MultiShardQuerySample.cs* souboru. Další informace najdete v tématu [dotazování více horizontálních oddílů](https://go.microsoft.com/?linkid=9862597).

* **Přidání prázdné horizontálních oddílů**: Iterativní přidávání nových horizontálních oddílů prázdný se provádí pomocí kódu v *CreateShardSample.cs* souboru. Další informace najdete v tématu [horizontální navýšení kapacity pomocí Správce mapování horizontálních oddílů databáze](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Další operace elastické škálování
* **Rozdělení existující horizontálních oddílů**: Nástroj split-merge poskytuje schopnost rozdělení horizontálních oddílů. Další informace najdete v tématu [přesouvá data mezi databázemi s horizontálním navýšením kapacity](sql-database-elastic-scale-overview-split-and-merge.md).

* **Slučování existující horizontálních oddílů**: Sloučení horizontálního oddílu jsou také provést pomocí dělení a slučování. Další informace najdete v tématu [přesouvá data mezi databázemi s horizontálním navýšením kapacity](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Náklady
Knihovna nástroje Elastic Database je zdarma. Při použití nástroje Elastic Database můžete neúčtují žádné další poplatky nad rámec náklady na využívání služeb Azure. 

Například ukázkové aplikace vytvoří nové databáze. Náklady na tato funkce závisí na edici SQL Database, kterou zvolíte a využití Azure pro vaše aplikace.

Informace o cenách najdete v tématu [podrobnosti o cenách SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další postup
Další informace o nástroje pro elastické databáze najdete v následujících článcích:

* Ukázky kódu: 
  * Nástroje pro elastické databáze ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Nástroje elastic Database pro Azure SQL – integrace Entity Frameworku](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticita horizontálních oddílů v Centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Elastické škálování oznámení](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanál 9: [Elastické škálování video s přehledem](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskuzní fórum: [Fórum služby Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* K měření výkonu: [Čítače výkonu pro správce mapování horizontálních oddílů](sql-database-elastic-database-client-library.md)

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

