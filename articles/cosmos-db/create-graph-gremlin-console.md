---
title: 'Kurz služby Azure Cosmos DB: Vytvářet, dotazovat a procházení v konzole Apache TinkerPops Gremlin'
description: Rychlý start ke službě Azure Cosmos DB vám pomůže s vytvářením vrcholů, okrajů a dotazů pomocí rozhraní Gremlin API služby Azure Cosmos DB.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 676fd2322dee170b015d0029163800989ff4e814
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383012"
---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Vytvářet, dotazovat a procházení grafu v konzole Gremlin

> [!div class="op_single_selector"]
> * [Konzola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

Tento rychlý start popisuje způsob vytvoření účtu rozhraní [Gremlin API](graph-introduction.md) služby Azure Cosmos DB, databáze a grafu (kontejneru) pomocí webu Azure Portal a následné použití [konzoly Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) z webu [Apache TinkerPop](https://tinkerpop.apache.org) pro práci s daty rozhraní Gremlin API. V tomto kurzu se naučíte vytvářet vrcholy a okraje a zadávat k nim dotazy, a to aktualizací vlastnosti vrcholu, a dále zadávat dotazy pro vrcholy, procházet graf a vyřadit konkrétní vrchol.

![Služba Azure Cosmos DB z konzoly Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

Konzola Gremlin je založena na technologii Groovy nebo Java a běží v systémech Linux, Mac a Windows. Můžete si ji stáhnout z [webu Apache TinkerPop](https://tinkerpop.apache.org/downloads.html).

## <a name="prerequisites"></a>Požadavky

Abyste si mohli vytvořit účet služby Azure Cosmos DB pro tento rychlý start, musíte mít předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Musíte si také nainstalovat [konzolu Gremlin](https://tinkerpop.apache.org/). Použijte verzi 3.2.5 nebo vyšší. (Pomocí konzoly Gremlin na Windows, je potřeba nainstalovat [modul Runtime Java](https://www.oracle.com/technetwork/java/javase/overview/index.html).)

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Přidání grafu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Připojení ke službě aplikace
1. Než začnete používat konzolu Gremlin, vytvořte nebo upravte v adresáři `apache-tinkerpop-gremlin-console-3.2.5/conf` konfigurační soubor remote-secure.yaml.
2. Podle následující tabulky vyplňte konfigurace *Hostitel*, *Port*, *Uživatelské jméno*, *Heslo*, *Fond připojení* a *Serializátor*:

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    hostitelé|[*název-účtu*.gremlin.cosmosdb.azure.com] nebo [*název-účtu*.graphs.azure.com] pro účty vytvořené před 20. prosincem 2017|Viz následující snímek obrazovky. Toto je hodnota Gremlin URI na stránce Přehled na webu Azure Portal v hranatých závorkách a s odebraným řetězcem „:443/“ na konci.
    port|443|Nastavte na hodnotu 443.
    uživatelské jméno|*Vaše uživatelské jméno*|Prostředek ve formátu `/dbs/<db>/colls/<coll>`, kde `<db>` je název vaší databáze a `<coll>` je název vaší kolekce.
    heslo|*Váš primární klíč*| Viz druhý snímek obrazovky níže. Toto je váš primární klíč, který můžete získat ze stránky Klíče na webu Azure Portal v poli Primární klíč. Pomocí tlačítka pro kopírování na levé straně pole hodnotu zkopírujte.
    fond připojení|{enableSsl: true}|Nastavení fondu připojení pro protokol SSL.
    serializátor|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Nastavte na tuto hodnotu a odstraňte případné konce řádků `\n` vzniklé vložením hodnoty.

    Pro hodnotu hostitelé zkopírujte **Gremlin URI** hodnotu **přehled** stránky: ![Zobrazení a zkopírování hodnoty Gremlin URI na stránce Přehled na webu Azure Portal](./media/create-graph-gremlin-console/gremlin-uri.png)

    Pro hodnotu heslo zkopírujte **primární klíč** z **klíče** stránky: ![Zobrazení a zkopírování primárního klíče na stránce webu Azure portal, klíče](./media/create-graph-gremlin-console/keys.png)

Váš soubor remote-secure.yaml by měl vypadat nějak takto:

```
hosts: [your_database_server.gremlin.cosmosdb.azure.com]
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0, config: { serializeResultToString: true }}
```

Ujistěte se, že při zabalení hodnoty parametru hostitelů v hranatých závorkách []. 

3. V terminálu spuštěním příkazu `bin/gremlin.bat` nebo `bin/gremlin.sh` spusťte [konzolu Gremlin](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/).
4. V terminálu se spuštěním příkazu `:remote connect tinkerpop.server conf/remote-secure.yaml` připojte k aplikační službě.

    > [!TIP]
    > Pokud se zobrazí chyba `No appenders could be found for logger`, zkontrolujte, že jste v souboru remote-secure.yaml aktualizovali hodnotu serializátoru, jak je popsáno v kroku 2. 

5. Potom spusťte příkaz `:remote console`, abyste přesměrovali všechny příkazy konzoly na vzdálený server.

   > [!NOTE]
   > Pokud jste nespustili příkaz `:remote console`, ale chcete přesměrovat všechny příkazy konzoly na vzdálený server, zadejte před příkaz předponu `:>`. Příklad spuštěného příkazu: `:> g.V().count()`. Předpona je součástí příkazu. Při používání konzoly Gremlin s Azure Cosmos DB je to důležité. Pokud tuto předponu vynecháte, dáte konzole pokyn, aby příkaz spustila lokálně – často s grafem v paměti. Použitím předpony `:>` dáváte konzole pokyn ke spuštění vzdáleného příkazu. V tomto případě ve službě Azure Cosmos DB (v emulátoru místního hostitele nebo v instanci Azure).

Výborně! Nastavení se nám podařilo dokončit a teď můžete spouštět některé příkazy konzoly.

Vyzkoušejme jednoduchý příkaz count(). Zadejte do příkazového řádku konzoly následující:

```
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Vytváření vrcholů a okrajů

Začněme přidáním pěti osob pro nastavení vrcholů: *Tomáš*, *Marie*, *Robin*, *Petr* a *Jan*.

Vstup (Tomáš):

```
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1)
```

Výstup:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Vstup (Marie):

```
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2)

```

Výstup:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Vstup (Robin):

```
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3)
```

Výstup:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Vstup (Petr):

```
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4)

```

Výstup:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Vstup (Jan):

```
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5)
```

Výstup:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


V dalším kroku přidejme pro vztahy mezi osobami okraje.

Vstup (Tomáš -> Marie):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Výstup:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Vstup (Tomáš -> Robin):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Výstup:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Vstup (Robin -> Petr):

```
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Výstup:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Aktualizace vrcholu

Aktualizujme vrchol *Tomáš* s novým věkovým údajem *45* let.

Vstup:
```
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Výstup:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Dotazování grafu

Teď spustíme pro graf celou řadu dotazů.

Vyzkoušejme nejdříve dotaz s filtrem pro vrácení pouze osob nad 40 let.

Vstup (dotaz s filtrem):

```
g.V().hasLabel('person').has('age', gt(40))
```

Výstup:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Teď navrhněme název pro skupinu osob nad 40 let.

Vstup (filtru + dotaz projekce):

```
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Výstup:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Procházení grafu

Umožňuje procházet graf tak, aby vrátil všechny přátele uživatele Tomáš.

Vstup (přátelé uživatele Tomáš):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Výstup: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

V následujícím kroku načteme další vrstvu vrcholů. Umožňuje procházet graf tak, aby vrátil všechny přátele uživatele Tomáš.

Vstup (přátelé přátel uživatele Tomáš):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Výstup:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Vyřazení vrcholu

Teď odstraníme vrchol z databáze grafu.

Vstup (vyřazení vrcholu Jan):

```
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Resetování grafu

Nakonec odstraníme z databáze všechny vrcholy a okraje.

Vstup:

```
g.E().drop()
g.V().drop()
```

Blahopřejeme! Dokončili jste tuto službu Azure Cosmos DB: Kurz gremlin API!

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili se způsobem vytvoření účtu služby Azure Cosmos DB, vytvoření grafu pomocí Průzkumníku dat, vytváření vrcholů a okrajů a procházení grafu pomocí konzoly Gremlin. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)
