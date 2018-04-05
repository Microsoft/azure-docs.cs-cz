---
title: Kurz k Node.js pro rozhraní SQL API pro Azure Cosmos DB | Microsoft Docs
description: Kurz Node.js, která vytvoří Cosmos DB s rozhraní SQL API.
keywords: kurz node.js, databáze uzlů
services: cosmos-db
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: article
ms.date: 08/14/2017
ms.author: anhoh
ms.openlocfilehash: de1ac7cb11f3e0d9c2de6dd3e5f635a9c508b2d2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="nodejs-tutorial-use-the-sql-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Kurz k Node.js: rozhraní SQL API v Azure Cosmos DB použít k vytvoření konzolové aplikace Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js pro MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Vítejte v kurzu Node.js pro sadu Azure Cosmos DB Node.js SDK! Až projdete tímto kurzem, budete mít konzolovou aplikaci, která vytváří prostředky Azure Cosmos DB a dotazuje se na ně.

Budeme se zabývat těmito tématy:

* Vytvoření účtu služby Azure Cosmos DB a připojení k němu
* Nastavení aplikace
* Vytvoření databáze Node
* Vytvoření kolekce
* Vytvoření dokumentů JSON
* Dotazování na kolekci
* Nahrazení dokumentu
* Odstranění dokumentu
* Odstranění databáze Node

Nemáte čas? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/documentdb-node-getting-started). Rychlé pokyny najdete v části [Získání úplného řešení](#GetSolution).

Až tento kurz Node.js dokončíte, řekněte nám prosím svůj názor pomocí hlasovacích tlačítek v horní a dolní části této stránky. Pokud chcete, abychom vás kontaktovali přímo, můžete nám nechat e-mailovou adresu v komentářích.

Můžeme začít!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Předpoklady pro kurz k Node.js 
Ujistěte se prosím, že máte následující:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) verze 0.10.29 nebo vyšší

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit k části [Nastavení aplikace Node.js](#SetupNode). Pokud používáte emulátor DB Cosmos Azure, postupujte podle kroků v [emulátoru DB Cosmos Azure](local-emulator.md) nastavit emulátoru a přeskočit na [nastavení aplikace Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Krok 2: Nastavení aplikace Node.js
1. Otevřete svůj oblíbený terminál.
2. Vyhledejte složku nebo adresář, do kterého chcete uložit aplikaci Node.js.
3. Vytvořte dva prázdné javascriptové soubory, které budou obsahovat následující příkazy:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux nebo OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Přes npm nainstalujte modul documentdb. Použijte následující příkaz:
   * ```npm install documentdb --save```

Výborně! Teď když jste dokončili nastavování, napišme nějaký kód.

## <a id="Config"></a>Krok 3: Nastavení konfigurací aplikace
Ve svém oblíbeném textovém editoru otevřete ```config.js```.

Potom, kopírování a vložte následující fragment kódu a nastavte vlastnosti ```config.endpoint``` a ```config.primaryKey``` pro koncový bod Azure Cosmos DB identifikátor uri a primární klíč. Obě tyto konfigurace lze nalézt v [portál Azure](https://portal.azure.com).

![Kurz k Node.js – snímek obrazovky portálu Azure znázorňující účet Azure Cosmos DB s AKTIVNÍM centrem zvýrazní, tlačítkem klíče v okně účtu Azure Cosmos DB a hodnotami URI, primární klíč a sekundární klíč v okně klíče – Databáze node][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Zkopírujte ```database id```, ```collection id``` a ```JSON documents``` a vložte je do objektu ```config``` níže, kde nastavíte vlastnosti ```config.endpoint``` a ```config.primaryKey```. Pokud již máte data, která chcete uložit do databáze, můžete místo přidávání definic dokumentů použít [nástroj pro migraci dat](import-data.md) služby Azure Cosmos DB.

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Databáze, kolekce a definice dokumentů bude fungovat jako vaše Azure DB Cosmos ```database id```, ```collection id```a data dokumentů.

Nakonec exportujte objekt ```config```, abyste na něj mohli odkazovat ze souboru ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>Krok 4: Připojení k účtu služby Azure Cosmos DB
V textovém editoru otevřete prázdný soubor ```app.js```. Zkopírováním a vložením kódu níže importujte modul ```documentdb``` a nově vytvořený modul ```config```.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Zkopírujte a vložte kód, který použije dříve uložené ```config.endpoint``` a ```config.primaryKey``` k vytvoření nové instance DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Teď, když máte kód pro inicializaci klienta Azure Cosmos DB, Podívejme se na práci s prostředky Azure Cosmos DB.

## <a name="step-5-create-a-node-database"></a>Krok 5: Vytvoření databáze Node
Zkopírujte a vložte kód níže, který nastaví stav HTTP pro odpověď Not Found (Nenalezeno), URL databáze a URL kolekce. Tyto adresy URL se, jak bude klient Azure Cosmos DB najít správné databáze a kolekce.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

[Databázi](sql-api-resources.md#databases) je možné vytvořit pomocí funkce [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) třídy **DocumentClient**. Databáze je logický kontejner úložiště dokumentů rozděleného mezi kolekcemi.

Pro vytvoření nové databáze v souboru app.js s ```id``` zadaným v objektu ```config``` zkopírujte a vložte funkci **getDatabase**. Funkce zkontroluje, jestli již neexistuje databáze se stejným ID ```FamilyRegistry```. Pokud ano, místo vytvoření nové databáze se vrátí databáze s tímto ID.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Zkopírujte a vložte kód níže, kde za funkci **getDatabase** přidáte pomocnou funkci **exit**. Ta vypíše závěrečnou zprávu a zavolá funkci **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.

## <a id="CreateColl"></a>Krok 6: Vytvoření kolekce
> [!WARNING]
> **createCollection** vytvoří novou kolekci, kterou se hradí. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

[Kolekci](sql-api-resources.md#collections) je možné vytvořit pomocí funkce [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) třídy **DocumentClient**. Kolekce je kontejner dokumentů JSON a přidružené logiky javascriptové aplikace.

Pro vytvoření nové kolekce s ```id``` zadaným v objektu ```config``` zkopírujte a vložte funkci **getCollection** pod funkci **getDatabase** v souboru app.js. Opět zkontrolujeme, že kolekce se stejným ID ```FamilyCollection``` ještě neexistuje. Pokud existuje, místo vytvoření nové kolekce se vrátí kolekce s tímto ID.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Zkopírujte a vložte kód pod voláním funkce **getDatabase**, aby se spustila funkce **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Blahopřejeme! Úspěšně jste vytvořili kolekci Azure Cosmos DB.

## <a id="CreateDoc"></a>Krok 7: Vytvoření dokumentu
[Dokument](sql-api-resources.md#documents) je možné vytvořit pomocí metody [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) třídy **DocumentClient**. Dokumenty představují uživatelem definovaný (libovolný) obsah JSON. Nyní můžete do služby Azure Cosmos DB vložit dokument.

Pod funkci **getCollection** zkopírujte a vložte funkci **getFamilyDocument**, která bude vytvářet dokumenty obsahující data JSON uložené v objektu ```config```. Opět zkontrolujeme, že dokument se stejným ID ještě neexistuje.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Zkopírujte a vložte kód pod voláním funkce **getCollection**, aby se spustila funkce **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Blahopřejeme! Úspěšně jste vytvořili dokument Azure Cosmos DB.

![Kurz k Node.js – diagram ilustrující hierarchický vztah mezi účtem, databází, kolekcí a dokumenty – databáze Node](./media/sql-api-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>Krok 8: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje bohaté [dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v každé z kolekcí. Následující ukázka kódu obsahuje dotaz, který je možné spustit proti dokumentům v kolekci.

Zkopírujte funkci **queryCollection** a vložte ji pod funkci **getFamilyDocument** v souboru app.js. Azure Cosmos DB podporuje dotazy podobné jazyku SQL, jak je uvedeno níže. Další informace o vytváření komplexních dotazů najdete v [Query Playground](https://www.documentdb.com/sql/demo) a [dokumentaci k dotazům](sql-api-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Následující diagram znázorňuje, jak Azure Cosmos DB SQL se volá syntaxe dotazu proti kolekci jste vytvořili.

![Kurz k Node.js – diagram znázorňující obor a význam dotazu – databáze Node](./media/sql-api-nodejs-get-started/node-js-tutorial-collection-documents.png)

[FROM](sql-api-sql-query.md#FromClause) – klíčové slovo je v dotazu volitelné, protože Azure Cosmos DB dotazy již mají obor nastaven na jedinou kolekci. Proto je možné příkaz „FROM Families f“ vyměnit za „FROM root r“ nebo jakoukoli jinou proměnnou, kterou si zvolíte. Azure Cosmos DB bude odvození že Families, root nebo název proměnné, které jste zvolili, odkazují na aktuální kolekci ve výchozím nastavení.

Zkopírujte a vložte kód pod voláním funkce **getFamilyDocument**, aby se spustila funkce **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Blahopřejeme! Úspěšně jste provedli dotaz na dokumenty Azure Cosmos DB.

## <a id="ReplaceDocument"></a>Krok 9: Nahrazení dokumentu
Azure Cosmos DB podporuje nahrazování dokumentů JSON.

Zkopírujte funkci **replaceFamilyDocument** a vložte ji pod funkci **queryCollection** v souboru app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Zkopírujte a vložte kód pod voláním funkce **queryCollection**, aby se spustila funkce **replaceDocument**. Také znovu přidejte kód volání **queryCollection**, aby bylo možné ověřit, že se dokument úspěšně změnil.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Blahopřejeme! Úspěšně jste nahradili dokument Azure Cosmos DB.

## <a id="DeleteDocument"></a>Krok 10: Odstranění dokumentu
Azure Cosmos DB podporuje odstraňování dokumentů JSON.

Zkopírujte funkci **deleteFamilyDocument** a vložte ji pod funkci **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Zkopírujte a vložte kód pod druhým voláním funkce **queryCollection**, aby se spustila funkce **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Blahopřejeme! Úspěšně jste odstranili dokument Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 11: Odstranění databáze Node
Odstraněním vytvořené databáze dojde k odstranění databáze a všech jejích podřízených prostředků (kolekcí, dokumentů atd.).

Zkopírujte a pod funkci **deleteFamilyDocument** vložte funkci **cleanup**, která odebere databázi a všechny podřízené prostředky.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Zkopírujte a vložte kód pod volání funkce **deleteFamilyDocument**, aby se spustila funkce **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Krok 12: Spuštění celé aplikace Node.js
Jako celek by pořadí volání funkcí mělo vypadat takto:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz ```node app.js```.

Měl by se zobrazit výstup počáteční aplikace. Výstup by měl odpovídat ukázkovému textu níže.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Blahopřejeme! Dokončili jste kurz Node.js a máte svou první konzolovou aplikaci využívající službu Azure Cosmos DB!

## <a id="GetSolution"></a>Získání úplného řešení kurzu k Node.js
Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jenom chcete stáhnout kód, můžete ho získat z [GitHubu](https://github.com/Azure-Samples/documentdb-node-getting-started).

Abyste mohli spustit řešení GetStarted, které obsahuje všechny ukázky tohoto článku, budete potřebovat následující:

* [Účet služby Azure Cosmos DB][create-account]
* Řešení [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) dostupné na GitHubu

Přes npm nainstalujte modul **documentdb**. Použijte následující příkaz:

* ```npm install documentdb --save```

Vedle ```config.js``` souboru, jak je popsáno v aktualizujte hodnoty config.endpoint a config.primaryKey [krok 3: nastavení konfigurací aplikace](#Config). 

Potom v terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: ```node app.js```.

A to je vše, stačí sestavit a máte hotovo. 

## <a name="next-steps"></a>Další postup
* Hledáte složitější ukázku Node.js? Viz [Sestavení webové aplikace Node.js využívající službu Azure Cosmos DB](sql-api-nodejs-application.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
