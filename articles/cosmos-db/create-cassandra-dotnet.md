---
title: 'Rychlý start: Cassandra API s využitím .NET – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Apache Cassandra API služby Azure Cosmos DB k vytvoření aplikace profilu pomocí webu Azure Portal a technologie .NET.
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a8d98485b180d999fb0762551e05ea5e3ef365b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661473"
---
# <a name="quickstart-build-a-cassandra-app-with-net-sdk-and-azure-cosmos-db"></a>Rychlý Start: Vytvoření aplikace Cassandra pomocí sady .NET SDK a Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

V tomto rychlém startu se dozvíte, jak pomocí .NET a Azure Cosmos DB [rozhraní API Cassandra](cassandra-introduction.md) vytvořit profilovou aplikaci klonováním příkladu z GitHubu. Tento rychlý start také ukazuje, jak pomocí webového portálu Azure Portal vytvořit účet Azure Cosmos DB.

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazovat databáze dokumentů, párů klíč-hodnota a grafů, které tak můžou využívat výhody možnosti globální distribuce a horizontálního škálování v jádru služby Azure Cosmos DB. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternativně můžete [vyzkoušet službu Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

Kromě toho je potřeba toto: 
* Pokud ještě nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Nainstalujte [Git](https://www.git-scm.com/) pro klonování příkladu.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Teď naklonujeme aplikaci rozhraní Cassandra API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek. Vytvořte novou složku s názvem `git-samples`. Pak zavřete příkazový řádek.

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

4. Potom otevřete soubor řešení CassandraQuickStartSample v sadě Visual Studio. 

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se pomocí kódu vytvoří prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Všechny fragmenty kódu pocházejí ze souboru `Program.cs` nainstalovaného ve složce `C:\git-samples\azure-cosmos-db-cassandra-dotnet-getting-started\CassandraQuickStartSample`. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string).

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

1. Na portálu [Azure Portal](https://portal.azure.com/) vyberte **Připojovací řetězec**.

1. Pomocí :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: tlačítka na pravé straně obrazovky Zkopírujte hodnotu uživatelské jméno.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Zobrazení a zkopírování přístupového klíče na webu Azure Portal, stránka Připojovací řetězec":::

1. V aplikaci Visual Studio otevřete soubor program. cs. 

1. Vložte hodnotu UŽIVATELSKÉ JMÉNO z portálu místo `<FILLME>` na řádku 13.

    Řádek 13 souboru Program.cs by teď měl vypadat nějak takto: 

    `private const string UserName = "cosmos-db-quickstart";`

1. Přejděte zpátky na portál a zkopírujte hodnotu HESLO. Vložte hodnotu HESLO z portálu místo `<FILLME>` na řádku 14.

    Řádek 14 souboru Program.cs by teď měl vypadat nějak takto: 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Přejděte zpátky na portál a zkopírujte hodnotu KONTAKTNÍ BOD. Vložte hodnotu KONTAKTNÍ BOD z portálu místo `<FILLME>` na řádku 15.

    Řádek 15 souboru Program.cs by teď měl vypadat nějak takto: 

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Uložte soubor Program.cs.
    
## <a name="run-the-net-app"></a>Spuštění aplikace .NET

1. V aplikaci Visual Studio vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.

2. Na příkazovém řádku pomocí následujícího příkazu nainstalujte balíček NuGet ovladače .NET. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Spusťte aplikaci stisknutím CTRL + F5. Aplikace se zobrazí v okně konzoly. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="Zobrazení a ověření výstupu":::

    Stisknutím kláves CTRL + C zastavte provádění programu a zavřete okno konzoly. 
    
4. Na portálu Azure Portal otevřete **Data Explorer**, abyste se mohli na tato nová data dotazovat, měnit je a pracovat s nimi.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="Zobrazení dat v Průzkumníku dat":::

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili vytvořit účet služby Azure Cosmos DB, vytvořit kontejner pomocí Průzkumníka dat a spustit webovou aplikaci. Teď můžete do účtu databáze Cosmos importovat další data. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)
