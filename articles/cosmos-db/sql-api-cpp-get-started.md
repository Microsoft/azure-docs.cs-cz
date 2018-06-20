---
title: Kurz jazyka C++ pro službu Azure Cosmos DB | Dokumentace Microsoftu
description: Kurz jazyka C++, v rámci kterého se vytvoří databáze a konzolová aplikace v jazyce C++ pomocí služby Azure Cosmos DB se schválenou sadou SDK pro jazyk C++. Azure Cosmos DB je databázová služba na globální úrovni.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: cpp
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: sngun
ms.openlocfilehash: 0e142eaf4182331e0a5803c54d2cc1284e21b221
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807171"
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure Cosmos DB: kurz konzolové aplikace v jazyce C++ pro rozhraní SQL API
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js pro MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Vítejte v kurzu jazyka C++ pro rozhraní Azure Cosmos DB SQL API se schválenou sadou SDK pro jazyk C++! Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB, včetně databáze v jazyce C++, a dotazuje se na ně.

Tento rychlý start zahrnuje:

* Vytvoření účtu služby Azure Cosmos DB a připojení k němu
* Nastavení aplikace
* Vytvoření databáze Azure Cosmos DB v jazyce C++
* Vytvoření kolekce
* Vytvoření dokumentů JSON
* Dotazování na kolekci
* Nahrazení dokumentu
* Odstranění dokumentu
* Odstranění databáze Azure Cosmos DB v jazyce C++

Nemáte čas? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/stalker314314/DocumentDBCpp). Rychlé pokyny najdete v části [Získání úplného řešení](#GetSolution).

Můžeme začít!

## <a name="prerequisites-for-the-c-tutorial"></a>Předpoklady pro kurz k C++
Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) s nainstalovanými komponentami jazyka C++. Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud už máte účet, který chcete použít, můžete přeskočit na [Nastavení aplikace C++](#SetupC++).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>Krok 2: Nastavení aplikace C++
1. Otevřete Visual Studio a v nabídce **Soubor** klikněte na **Nový** a potom na **Projekt**. 
2. V okně **Nový projekt** v podokně **Nainstalováno** rozbalte nabídku **Visual C++**, klikněte na **Win32** a potom klikněte na **Konzolová aplikace Win32**. Jako název dokumentu zadejte hellodocumentdb a klikněte na **OK**. 
   
    ![Snímek obrazovky s průvodcem novým projektem](media/sql-api-cpp-get-started/hello.png)
3. Jakmile se spustí průvodce aplikací Win32, klikněte na **Dokončit**.
4. Po vytvoření projektu otevřete správce balíčku NuGet kliknutím pravým tlačítkem myši na projekt **hellodocumentdb** v **průzkumníku řešení** a potom na **Spravovat balíčky NuGet**. 
   
    ![Snímek obrazovky s možností Spravovat balíčky NuGet v nabídce projektu](media/sql-api-cpp-get-started/nuget.png)
5. Na kartě **NuGet: hellodocumentdb** klikněte na **Procházet** a vyhledejte *documentdbcpp*. Ve výsledcích vyberte položku DocumentDbCPP, jak je znázorněno na následujícím snímku obrazovky:   
   
    ![Snímek obrazovky se zvýrazněným balíčkem DocumentDbCpp](media/sql-api-cpp-get-started/cpp.png)
   
    Tento balíček nainstaluje odkazy na sadu C++ REST SDK, která závislostí pro DocumentDbCPP. Jakmile se balíčky přidají do vašeho projektu, můžete začít psát kód.   

## <a id="Config"></a>Krok 3: Zkopírování podrobností o připojení z webu Azure Portal pro databázi Azure Cosmos DB
Otevřete web [Azure Portal](https://portal.azure.com) a přejděte do účtu Azure Cosmos DB, který jste vytvořili. Pro další krok budete z webu Azure Portal potřebovat identifikátor URI a primární klíč, pomocí kterých navážete připojení z fragmentu kódu v jazyce C++. 

![Identifikátor URI a klíče pro službu Azure Cosmos DB na webu Azure Portal](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Krok 4: Připojení k účtu služby Azure Cosmos DB
1. Do zdrojového kódu zadejte níže uvedené hlavičky a obory názvů za `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Dále do hlavní funkce přidejte následující kód a změňte konfiguraci účtu a primární klíč tak, aby se shodovaly s nastavením služby Azure Cosmos DB popsaným v kroku 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Nyní, když máte kód pro inicializaci klienta, se budeme věnovat práci s prostředky Azure Cosmos DB.

## <a id="CreateDBColl"></a>Krok 5: Vytvoření databáze a kolekce v jazyce C++
Pokud se službou Azure Cosmos DB začínáte, přečtěte si ještě před provedením tohoto kroku, jak databáze, kolekce a dokumenty vzájemně komunikují. [Databáze](sql-api-resources.md#databases) je logický kontejner úložiště dokumentů rozděleného mezi kolekcemi. [Kolekce](sql-api-resources.md#collections) je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace. Další informace o konceptech a hierarchickém modelu prostředků Azure Cosmos DB najdete v tématu [Koncepty a hierarchický model prostředků Azure Cosmos DB](sql-api-resources.md).

Databázi a odpovídající kolekci vytvoříte vložením níže uvedeného kódu na konec hlavní funkce. Prostřednictvím konfigurace klienta deklarované v předchozím kroku vznikne databáze „FamilyRegistry“ a kolekce „FamilyCollection“.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Krok 6: Vytvoření dokumentu
[Dokumenty](sql-api-resources.md#documents) představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete do služby Azure Cosmos DB vložit dokument. Dokument můžete vytvořit zkopírováním níže uvedeného kódu na konec hlavní funkce. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Souhrnně řečeno, tento kód vytvoří databázi, kolekci a dokumenty Azure Cosmos DB, na které se můžete dotazovat v Průzkumníku dat na webu Azure Portal. 

![Kurz k C++ – diagram ilustrující hierarchický vztah mezi účtem, databází, kolekcí a dokumenty](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>Krok 7: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující ukázkový kód ukazuje dotaz vytvořený pomocí syntaxe SQL, který můžete spouštět proti dokumentům vytvořeným v předchozím kroku.

Tato funkce využívá jako argumenty společně s klientem dokumentu i unikátní identifikátor nebo ID prostředku databáze a kolekce. Vložte tento kód před hlavní funkci.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Krok 8: Nahrazení dokumentu
Azure Cosmos DB podporuje nahrazování dokumentů JSON, jak můžete vidět na následujícím kódu. Tento kód vložte za funkci executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Krok 9: Odstranění dokumentu
Azure Cosmos DB podporuje odstraňování dokumentů JSON. Provedete to tak, že zkopírujete a vložíte následující kód za funkci replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Krok 10: Odstranění databáze
Odstraněním vytvořené databáze dojde k odebrání databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.).

Zkopírujte a za funkci deletedocument vložte následující fragment kódu (vyčištění funkce), který odebere databázi a všechny podřízené prostředky.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Krok 11: Spuštění celé konzolové aplikace jazyka C#
Vložili jste kód, pomocí kterého můžete vytvářet, upravovat a odstraňovat různé prostředky Azure Cosmos DB nebo se na ně dotazovat.  Nyní ho uveďte do provozu přidáním volání těchto funkcí z hlavní funkce v projektu hellodocumentdb.cpp a některých diagnostických zpráv.

Provedete to tak, že hlavní funkci vaší aplikace nahradíte níže uvedeným kódem. account_configuration_uri a primary_key, které jste do kódu zkopírovali v kroku 3, se přepíší, takže tento řádek uložte nebo hodnoty znovu zkopírujte z webu. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Teď už by mělo být možné vytvořit a spustit kód v sadě Visual Studio stisknutím klávesy F5, případně v okně terminálu vyhledáním aplikace a otevřením spustitelného souboru. 

Měl by se zobrazit výstup počáteční aplikace. Tento výstup by se měl shodovat se snímkem obrazovky níže:

![Výstup aplikace v jazyce C++ využívající službu Azure Cosmos DB](media/sql-api-cpp-get-started/console.png)

Blahopřejeme! Dokončili jste kurz jazyka C++ a máte svou první konzolovou aplikaci využívající službu Azure Cosmos DB!

## <a id="GetSolution"></a>Získání úplného řešení kurzu k C++
Abyste mohli sestavit řešení GetStarted, které obsahuje všechny ukázky tohoto článku, budete potřebovat následující:

* [Účet služby Azure Cosmos DB][create-account]
* Řešení [GetStarted](https://github.com/stalker314314/DocumentDBCpp) dostupné na GitHubu

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Přečtěte si více o tomto programovacím modelu v části Vyvíjejte na [stránce dokumentace ke službě Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

[create-account]: create-sql-api-dotnet.md#create-account


