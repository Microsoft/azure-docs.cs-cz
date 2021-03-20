---
title: Nastavení připojení k účtu Cosmos DB pomocí spravované identity
titleSuffix: Azure Cognitive Search
description: Naučte se, jak nastavit připojení indexeru k účtu Cosmos DB pomocí spravované identity.
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422089"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Nastavení připojení indexeru k databázi Cosmos DB pomocí spravované identity

Tato stránka popisuje, jak nastavit připojení indexeru k databázi Azure Cosmos DB pomocí spravované identity namísto zadání přihlašovacích údajů do připojovacího řetězce objektu zdroje dat.

Než se dozvíte víc o této funkci, doporučujeme vám pochopit, co indexer je a jak nastavit indexer pro zdroj dat. Další informace najdete na následujících odkazech:

* [Přehled indexeru](search-indexer-overview.md)
* [Indexer pro Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Nastavení připojení pomocí spravované identity

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – zapnout spravovanou identitu přiřazenou systémem

Když je povolená spravovaná identita přiřazená systémem, Azure vytvoří identitu pro vaši vyhledávací službu, která se dá použít k ověření dalších služeb Azure v rámci stejného tenanta a předplatného. Tuto identitu pak můžete použít v přiřazeních řízení přístupu na základě role v Azure (Azure RBAC), které umožňuje přístup k datům během indexování.

![Zapnout spravovanou identitu přiřazenou systémem](./media/search-managed-identities/turn-on-system-assigned-identity.png "Zapnout spravovanou identitu přiřazenou systémem")

Po výběru možnosti **Uložit** se zobrazí ID objektu, které bylo přiřazeno k vaší vyhledávací službě.

![ID objektu](./media/search-managed-identities/system-assigned-identity-object-id.png "ID objektu")
 
### <a name="2---add-a-role-assignment"></a>2. přidání přiřazení role

V tomto kroku udělíte službě Azure Kognitivní hledání oprávnění ke čtení dat z databáze Cosmos DB.

1. V Azure Portal přejděte na účet Cosmos DB obsahující data, která chcete indexovat.
2. Vyberte **Řízení přístupu (IAM)** .
3. Vyberte **Přidat** a pak **Přidat přiřazení role** .

    ![Přidat přiřazení role](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Přidat přiřazení role")

4. Vyberte **roli Čtenář účtu Cosmos DB** .
5. Ponechat **přiřazení přístupu k** **uživateli, skupině nebo instančnímu objektu Azure AD**
6. Vyhledejte vyhledávací službu, vyberte ji a pak vyberte **Uložit** .

    ![Přidat přiřazení čtecího zařízení a role přístup k datům](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Přidat přiřazení čtecího zařízení a role přístup k datům")

### <a name="3---create-the-data-source"></a>3. vytvoření zdroje dat

[REST API](/rest/api/searchservice/create-data-source), Azure Portal a [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) podporují připojovací řetězec spravované identity. Níže je uveden příklad vytvoření zdroje dat pro indexaci dat z Cosmos DB pomocí [REST API](/rest/api/searchservice/create-data-source) a spravovaného připojovacího řetězce identity. Formát připojovacího řetězce spravované identity je stejný pro REST API, sadu .NET SDK a Azure Portal.

Při ověřování pomocí spravovaných identit nebudou **přihlašovací údaje** obsahovat klíč účtu.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Tělo požadavku obsahuje definici zdroje dat, která by měla obsahovat následující pole:

| Pole   | Description |
|---------|-------------|
| **Jméno** | Povinná hodnota. Vyberte libovolný název, který bude představovat váš objekt zdroje dat. |
|**textový**| Povinná hodnota. Musí být `cosmosdb` . |
|**přihlašovací údaje** | Povinná hodnota. <br/><br/>Při připojování pomocí spravované identity by měl mít formát **pověření** : *Database = [název databáze]; ResourceId = [Resource-ID-String];(ApiKind = [typ rozhraní API];)*<br/> <br/>Formát ResourceId: *ResourceID =/Subscriptions/**ID vašeho předplatného**/resourceGroups/**název vaší skupiny prostředků**/Providers/Microsoft.DocumentDB/databaseAccounts/**název vašeho účtu služby Cosmos DB**/;*<br/><br/>V případě kolekcí SQL nevyžaduje připojovací řetězec ApiKind.<br/><br/>Pro kolekce MongoDB přidejte **ApiKind = MongoDB** do připojovacího řetězce. <br/><br/>V případě grafů Gremlin a tabulek Cassandra si zaregistrujte si ve [verzi Preview služby gated indexer](https://aka.ms/azure-cognitive-search/indexer-preview) , abyste získali přístup k verzi Preview a informace o tom, jak tato pověření naformátovat.<br/>|
| **vnitřního** | Obsahuje následující prvky: <br/>**název**: povinné. Zadejte ID kolekce databází, která se má indexovat.<br/>**dotaz**: volitelné. Můžete zadat dotaz pro sloučení libovolného dokumentu JSON do plochého schématu, které může Azure Kognitivní hledání indexovat.<br/>Pro rozhraní MongoDB API, rozhraní Gremlin API a rozhraní API Cassandra se dotazy nepodporují. |
| **dataChangeDetectionPolicy** | Doporučeno |
|**dataDeletionDetectionPolicy** | Volitelné |

### <a name="4---create-the-index"></a>4. vytvoření indexu

Index určuje pole v dokumentu, atributech a dalších konstrukcích, které prohledají možnosti vyhledávání.

Tady je postup vytvoření indexu s prohledávatelným `booktitle` polem:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Další informace o vytváření indexů najdete v tématu [vytvoření indexu](/rest/api/searchservice/create-index) .

### <a name="5---create-the-indexer"></a>5. vytvoření indexeru

Indexer připojuje zdroj dat k cílovému vyhledávacímu indexu a poskytuje plán pro automatizaci aktualizace dat.

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer.

Příklad definice indexeru:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Tento indexer se spustí každé dvě hodiny (časový interval je nastaven na "PT2H"). Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je nepovinný – Pokud je vynechaný, indexer se při vytvoření spustí jenom jednou. Můžete ale kdykoli spustit indexer na vyžádání.   

Další informace o rozhraní API Create indexeru najdete v části [Vytvoření indexeru](/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Poradce při potížích

Pokud zjistíte, že nemůžete indexovat data z Cosmos DB zvažte následující:

1. Pokud jste nedávno provedli použití klíčů účtu Cosmos DB, budete muset počkat až 15 minut, než se v připojovacím řetězci spravované identity dokončí.

1. Zkontrolujte, jestli má účet Cosmos DB omezený přístup pro vybrané sítě. Pokud tomu tak je, přečtěte si informace o [přístupu indexeru k obsahu chráněnému funkcemi zabezpečení sítě Azure](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Další kroky

* [Indexer pro Azure Cosmos DB](search-howto-index-cosmosdb.md)