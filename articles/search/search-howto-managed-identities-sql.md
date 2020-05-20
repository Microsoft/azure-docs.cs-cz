---
title: Nastavení připojení ke službě Azure SQL Database pomocí spravované identity (Preview)
titleSuffix: Azure Cognitive Search
description: Naučte se, jak nastavit připojení indexeru ke službě Azure SQL Database pomocí spravované identity (Preview).
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 87389651707a3bdcc18ae7eb03b88681b5303c4d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664799"
---
# <a name="set-up-an-indexer-connection-to-an-azure-sql-database-using-a-managed-identity-preview"></a>Nastavení připojení indexeru ke službě Azure SQL Database pomocí spravované identity (Preview)

> [!IMPORTANT] 
> Podpora nastavení připojení ke zdroji dat pomocí spravované identity je aktuálně v ověřované veřejné verzi Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/mi-preview-request)můžete požádat o přístup k verzi Preview.

Tato stránka popisuje, jak nastavit připojení indexeru ke službě Azure SQL Database pomocí spravované identity namísto zadání přihlašovacích údajů do připojovacího řetězce objektu zdroje dat.

Než se dozvíte víc o této funkci, doporučujeme vám pochopit, co indexer je a jak nastavit indexer pro zdroj dat. Další informace najdete na následujících odkazech:
* [Přehled indexeru](search-indexer-overview.md)
* [Indexer pro Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Nastavení připojení pomocí spravované identity

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – zapnout spravovanou identitu přiřazenou systémem

Když je povolená spravovaná identita přiřazená systémem, Azure vytvoří identitu pro vaši vyhledávací službu, která se dá použít k ověření dalších služeb Azure v rámci stejného tenanta a předplatného. Tuto identitu pak můžete použít v přiřazeních řízení přístupu na základě role (RBAC), které umožňuje přístup k datům během indexování.

![Zapnout spravovanou identitu přiřazenou systémem](./media/search-managed-identities/turn-on-system-assigned-identity.png "Zapnout spravovanou identitu přiřazenou systémem")

Po výběru možnosti **Uložit** se zobrazí ID objektu, které bylo přiřazeno k vaší vyhledávací službě.

![ID objektu](./media/search-managed-identities/system-assigned-identity-object-id.png "ID objektu")
 
### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2. zřízení Azure Active Directory správce pro SQL Server

Při připojování k databázi v dalším kroku se budete muset připojit pomocí účtu Azure Active Directory (Azure AD), který má oprávnění správce k databázi, aby vaše služba vyhledávání mohla mít přístup k databázi.

Postupujte podle pokynů uvedených [tady](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) a udělte správci účtu Azure AD přístup k databázi.

### <a name="3---assign-the-search-service-permissions"></a>3. přiřazení oprávnění vyhledávací služby

Pomocí následujících kroků přiřaďte oprávnění vyhledávací služby ke čtení databáze.

1. Připojit k aplikaci Visual Studio

    ![Připojit k aplikaci Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Připojit k aplikaci Visual Studio")

2. Ověřování pomocí účtu Azure AD

    ![Ověření](./media/search-managed-identities/visual-studio-authentication.png "Ověření")

3. Spusťte následující příkazy:

    Název vyhledávací služby uzavřete do hranatých závorek.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Nový dotaz](./media/search-managed-identities/visual-studio-new-query.png "Nový dotaz")

    ![Spustit dotaz](./media/search-managed-identities/visual-studio-execute-query.png "Spustit dotaz")

>[!NOTE]
> Pokud se identita služby Search z kroku 1 po dokončení tohoto kroku změní, je nutné odebrat členství v roli a odebrat uživatele v databázi SQL a pak znovu přidat oprávnění provedením kroku 3.
> Odebrání členství role a uživatele můžete provést spuštěním následujících příkazů:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4. přidání přiřazení role

V tomto kroku udělíte službě Azure Kognitivní hledání oprávnění číst data z vašeho SQL Server.

1. V Azure Portal přejděte na stránku Azure SQL Server.
2. Výběr **řízení přístupu (IAM)**
3. Vyberte **Přidat** a pak **Přidat přiřazení role** .

    ![Přidat přiřazení role](./media/search-managed-identities/add-role-assignment-sql-server.png "Přidat přiřazení role")

4. Vyberte příslušnou roli **čtenáře** .
5. Ponechat **přiřazení přístupu k** **uživateli, skupině nebo instančnímu objektu Azure AD**
6. Vyhledejte vyhledávací službu, vyberte ji a pak vyberte **Uložit** .

    ![Přidat přiřazení role čtenáře](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Přidat přiřazení role čtenáře")

### <a name="5---create-the-data-source"></a>5. vytvoření zdroje dat

Při indexování z databáze SQL musí mít zdroj dat následující požadované vlastnosti:

* **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
* **typ** je`azuresql`
* **přihlašovací údaje**
    * Při ověřování pomocí spravované identity se formát **přihlašovacích údajů** liší od použití identity spravovaných. Tady poskytnete počáteční katalog nebo název databáze a ResourceId, které nemají klíč nebo heslo účtu. ResourceId musí zahrnovat ID předplatného Azure SQL Database, skupinu prostředků služby SQL Database a název databáze SQL. 
    * Formát připojovacího řetězce spravované identity:
        * *Počáteční katalog | Databáze =**název databáze**; ResourceId =/Subscriptions/**ID vašeho předplatného**/resourceGroups/**název vaší skupiny prostředků**/Providers/Microsoft.SQL/Servers/**vaše SQL Server jméno**/; Časový limit připojení =**časový limit připojení**;*
* **Container** Určuje název tabulky nebo zobrazení, které chcete indexovat.

Příklad vytvoření objektu zdroje dat SQL Azure pomocí [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

Azure Portal a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) podporují také připojovací řetězec spravovaných identit. Azure Portal vyžaduje příznak funkce, který vám poskytne při registraci ve verzi Preview pomocí odkazu v horní části této stránky. 

### <a name="6---create-the-index"></a>6. vytvoření indexu

Index určuje pole v dokumentu, atributech a dalších konstrukcích, které prohledají možnosti vyhledávání.

Tady je postup vytvoření indexu s prohledávatelným `booktitle` polem:   

```
POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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

Další informace o vytváření indexů najdete v tématu [vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) .

### <a name="7---create-the-indexer"></a>7. vytvoření indexeru

Indexer připojuje zdroj dat k cílovému vyhledávacímu indexu a poskytuje plán pro automatizaci aktualizace dat.

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer.

Příklad definice indexeru pro indexer SQL Azure:

```
POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Tento indexer se spustí každé dvě hodiny (časový interval je nastaven na "PT2H"). Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je nepovinný – Pokud je vynechaný, indexer se při vytvoření spustí jenom jednou. Můžete ale kdykoli spustit indexer na vyžádání.   

Další informace o rozhraní API Create indexeru najdete v části [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Řešení potíží

Pokud se při pokusu indexeru o připojení ke zdroji dat zobrazí chybová zpráva s informacemi o tom, že klient nemá povolený přístup k serveru, podívejte se na [běžné chyby indexeru](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting).

## <a name="see-also"></a>Viz také

Další informace o službě Azure SQL indexer:
* [Indexer pro Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
