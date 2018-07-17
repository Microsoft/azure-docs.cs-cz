---
title: 'Rychlý start: Cassandra API s využitím .NET – Azure Cosmos DB | Dokumentace Microsoftu'
description: Tento rychlý start ukazuje, jak použít rozhraní Apache Cassandra API služby Azure Cosmos DB k vytvoření aplikace profilu pomocí webu Azure Portal a technologie .NET.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.custom: quick start connect, mvc
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 1b7021987c1c58c7b08798a28643ac647569e2bc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38485544"
---
# <a name="quickstart-build-a-cassandra-app-with-net-and-azure-cosmos-db"></a>Rychlý start: Sestavení aplikace Cassandra pomocí .NET a Azure Cosmos DB

Tento rychlý start ukazuje, jak pomocí .NET a rozhraní Azure Cosmos DB [Cassandra API](cassandra-introduction.md) sestavit aplikaci profilu naklonováním příkladu z GitHubu. Tento rychlý start vás také provede vytvořením účtu služby Azure Cosmos DB pomocí webu Azure Portal.   

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, párů klíč-hodnota a grafů, které tak můžou využívat výhody možnosti globální distribuce a horizontálního škálování v jádru služby Azure Cosmos DB. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternativně můžete [vyzkoušet službu Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

Přejděte k programu Azure Cosmos DB Cassandra API Preview. Pokud jste ještě nepožádali o přístup, [zaregistrujte se](cassandra-introduction.md#sign-up-now).

Navíc platí: 
* Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Nainstalujte [Git](https://www.git-scm.com/) pro klonování příkladu.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Teď naklonujeme aplikaci rozhraní Cassandra API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-getting-started.git
    ```

3. Potom otevřete soubor řešení CassandraQuickStartSample v sadě Visual Studio. 

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny fragmenty kódu pocházejí ze souboru Program.cs nainstalovaného ve složce C:\git-samples\azure-cosmos-db-cassandra-dotnet-getting-started\CassandraQuickStartSample. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string).

* Inicializujte relaci připojením ke koncovému bodu clusteru Cassandra. Cassandra API ve službě Azure Cosmos DB podporuje jenom TLSv1.2. 

  ```csharp
   var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
   options.SetHostNameResolver((ipAddress) => CassandraContactPoint);
   Cluster cluster = Cluster.Builder().WithCredentials(UserName, Password).WithPort(CassandraPort).AddContactPoint(CassandraContactPoint).WithSSL(options).Build();
   ISession session = cluster.Connect();
   ```

* Vytvořte nový prostor klíčů.

    ```csharp
    session.Execute("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"); 
    ```

* Vytvořte novou tabulku.

   ```csharp
  session.Execute("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)");
   ```

* Vložte entity uživatelů pomocí objektu IMapper s novou relací, která se připojuje k prostoru klíčů uprofile.

    ```csharp
    mapper.Insert<User>(new User(1, "LyubovK", "Dubai"));
    ```
    
* Použijte dotaz pro získání informací o všech uživatelích.

    ```csharp
   foreach (User user in mapper.Fetch<User>("Select * from user"))
   {
      Console.WriteLine(user);
   }
    ```
    
* Použijte dotaz pro získání informací o jednom uživateli.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Informace o připojovacím řetězci umožňují aplikaci komunikovat s hostovanou databází.

1. Na webu [Azure Portal](http://portal.azure.com/) klikněte na **Připojovací řetězec**. 

    Pomocí tlačítka ![Tlačítko pro kopírování](./media/create-cassandra-dotnet/copy.png) na pravé straně obrazovky zkopírujte hodnotu UŽIVATELSKÉ JMÉNO.

    ![Zobrazení a zkopírování přístupového klíče na webu Azure Portal, stránka Připojovací řetězec](./media/create-cassandra-dotnet/keys.png)

2. V sadě Visual Studio 2017 otevřete soubor Program.cs. 

3. Vložte hodnotu UŽIVATELSKÉ JMÉNO z portálu místo `<FILLME>` na řádku 13.

    Řádek 13 souboru Program.cs by teď měl vypadat nějak takto: 

    `private const string UserName = "cosmos-db-quickstart";`

3. Přejděte zpátky na portál a zkopírujte hodnotu HESLO. Vložte hodnotu HESLO z portálu místo `<FILLME>` na řádku 14.

    Řádek 14 souboru Program.cs by teď měl vypadat nějak takto: 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

4. Přejděte zpátky na portál a zkopírujte hodnotu KONTAKTNÍ BOD. Vložte hodnotu KONTAKTNÍ BOD z portálu místo `<FILLME>` na řádku 15.

    Řádek 15 souboru Program.cs by teď měl vypadat nějak takto: 

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

5. Uložte soubor Program.cs.
    
## <a name="run-the-app"></a>Spuštění aplikace

1. V sadě Visual Studio klikněte na **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.

2. Na příkazovém řádku pomocí následujícího příkazu nainstalujte balíček NuGet ovladače .NET. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Spusťte aplikaci stisknutím CTRL+F5. Aplikace se zobrazí v okně konzoly. 

    ![Zobrazení a ověření výstupu](./media/create-cassandra-dotnet/output.png)

    Stisknutím CTRL + C zastavte provádění programu a zavřete okno konzoly. 
    
    Teď můžete otevřít Průzkumníka dat na webu Azure Portal a zobrazit dotaz nebo provést úpravy a pracovat s těmito novými daty. 

    ![Zobrazení dat v Průzkumníku dat](./media/create-cassandra-dotnet/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili vytvořit účet služby Azure Cosmos DB, vytvořit kontejner pomocí Průzkumníka dat a spustit webovou aplikaci. Teď můžete do účtu Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)
