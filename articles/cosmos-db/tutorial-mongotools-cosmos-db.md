---
title: Migrace MongoDB do offline režimu pro Azure Cosmos DB rozhraní API pro MongoDB pomocí nativních nástrojů MongoDB
description: Přečtěte si, jak lze pomocí nativních nástrojů MongoDB migrovat malé datové sady z instancí MongoDB na Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2b52a9227e8bd487a8929df11047eef4672f7f4a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100421773"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Kurz: migrace MongoDB do rozhraní API služby Azure Cosmos DB pro MongoDB v režimu offline pomocí nativních nástrojů MongoDB

Pomocí nativních nástrojů MongoDB můžete provést offline (jednorázovou) migraci databází z místní nebo cloudové instance MongoDB, abyste Azure Cosmos DB rozhraní API pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Zvolit vhodný nativní nástroj MongoDB pro váš případ použití
> * Spuštění migrace
> * Monitorujte migraci.
> * Ověřte, jestli migrace proběhla úspěšně.

V tomto kurzu migrujete datovou sadu v MongoDB hostovaném na virtuálním počítači Azure, abyste mohli rozhraní API pro MongoDB Azure Cosmos DB pomocí nativních nástrojů MongoDB. Nativní nástroje MongoDB jsou sadou binárních souborů, které usnadňují manipulaci s daty existující instance MongoDB. Vzhledem k tomu, že Azure Cosmos DB zpřístupňuje rozhraní Mongo API, MongoDB nativní nástroje budou moci vkládat data do Azure Cosmos DB. Tento dokument se zaměřuje na migraci dat z instance MongoDB pomocí *mongoexport/mongoimport* nebo *mongodump/mongorestore*. Vzhledem k tomu, že se nativní nástroje připojují k MongoDB pomocí připojovacích řetězců, můžete nástroje spustit kdekoli, ale doporučujeme spouštět tyto nástroje v rámci stejné sítě jako instance MongoDB, aby nedocházelo k problémům s bránou firewall. 

Nativní nástroje MongoDB můžou data přesouvat jenom tak rychle, jak to hostitelský hardware povoluje; nativní nástroje můžou být nejjednodušší řešení pro malé datové sady, kde celková doba migrace nepředstavuje žádný problém. [Konektor MongoDB Spark](https://docs.mongodb.com/spark-connector/current/), [Služba Azure Data MIGRATION Service (DMS)](../dms/tutorial-mongodb-cosmos-db.md)nebo [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) může být lepší alternativou, pokud potřebujete škálovatelný kanál migrace.

Pokud ještě nemáte nastavený zdroj MongoDB, přečtěte si článek [instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure](../virtual-machines/windows/install-mongodb.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* [Dokončete kroky před migrací](../cosmos-db/mongodb-pre-migration.md) , například odhad propustnosti, výběr klíče oddílu a zásady indexování.
* [Vytvořte Azure Cosmos DB API pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Přihlášení k instanci MongoDB
    * [Stáhněte a nainstalujte si z tohoto odkazu nástroje MongoDB Native Tools](https://www.mongodb.com/try/download/database-tools).
        * **Ujistěte se, že verze nativních nástrojů MongoDB odpovídá vaší stávající instanci MongoDB.**
        * Pokud má vaše instance MongoDB jinou verzi než Azure Cosmos DB Mongo API, **nainstalujte obě verze nástrojů MongoDB Native a použijte odpovídající verzi nástroje pro MongoDB a rozhraní API pro Azure Cosmos DB Mongo v uvedeném pořadí.**
    * Přidat uživatele s `readWrite` oprávněním, pokud nikdo už neexistuje. Později v tomto kurzu poskytněte uživatelské jméno a heslo k nástrojům *mongoexport* a *mongodump* .

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Konfigurace Azure Cosmos DBch opakovaných pokusů na straně serveru

Zákazníci, kteří migrují z Azure Cosmos DB MongoDB, přináší výhody funkcí zásad správného řízení prostředků, což zaručuje schopnost plně využít vaše zřízené zvýšení propustnosti/s. Azure Cosmos DB může v průběhu migrace omezit daný požadavek, pokud požadavek překročí zřízené požadavky na kontejner/s. pak se musí tento požadavek opakovat. Doba odezvy v síti, která je součástí směrování mezi nástrojem pro migraci a Azure Cosmos DB dopadem na celkovou dobu odezvy tohoto požadavku; Kromě toho MongoDB nativní nástroje nemusí zpracovávat opakované pokusy. Funkce *opakování na straně serveru* Azure Cosmos DB umožňuje službě zachytit kódy chyb omezení a opakovat s mnohem nižší dobou odezvy, což výrazně vylepšuje dobu odezvy na žádosti. Z perspektivy MongoDBch nativních nástrojů se minimalizuje nutnost zpracovat opakované pokusy. to má za důsledek pozitivní dopad na vaše prostředí během migrace.

Funkci opakování na straně serveru můžete najít v okně *funkce* portálu Azure Cosmos DB.

![Snímek obrazovky funkce MongoDB SSR](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

A pokud je *zakázaný*, doporučujeme ho povolit, jak vidíte níže.

![Snímek obrazovky s povoleným MongoDB SSR](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Volba správného nativního nástroje MongoDB

![Diagram výběru nejlepšího nástroje MongoDB Native](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* je nejlepší pár migračních nástrojů pro migraci podmnožiny vaší databáze MongoDB.
    * *mongoexport* exportuje vaše stávající data do souboru JSON nebo CSV s možností čtení. *mongoexport* přebírá argument určující podmnožinu stávajících dat k exportu. 
    * *mongoimport* otevře soubor JSON nebo CSV a vloží obsah do instance cílové databáze (v tomto případě Azure Cosmos DB.). 
    * Všimněte si, že JSON a CSV nejsou kompaktní formáty; můžou se vám účtovat nadbytečné poplatky za sítě, které *mongoimport* odesílá data do Azure Cosmos DB.
* *mongodump/mongorestore* je nejlepší pár migračních nástrojů pro migraci celé databáze MongoDB. Formát Compact BSON zajistí efektivnější využití síťových prostředků, protože data jsou vložena do Azure Cosmos DB.
    * *mongodump* exportuje vaše stávající data jako soubor bson.
    * *mongorestore* naimportuje výpis souboru BSON do Azure Cosmos DB.
* Pokud budete mít k dispozici jenom malý soubor JSON, který chcete importovat do Azure Cosmos DB rozhraní Mongo API, nástroj *mongoimport* je rychlé řešení pro ingestování dat.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Shromáždit přihlašovací údaje rozhraní API pro Azure Cosmos DB Mongo

Rozhraní Azure Cosmos DB API Mongo poskytuje kompatibilní přihlašovací údaje pro přístup, které mohou využívat nativní nástroje MongoDB. Aby bylo možné migrovat data do Azure Cosmos DB rozhraní Mongo API, budete muset mít tato pověření pro přístup k dispozici. Vyhledání těchto přihlašovacích údajů:

1. Otevřete Azure Portal.
1. Přejít na váš Azure Cosmos DB účet rozhraní API pro Mongo
1. V levém navigačním panelu vyberte okno *připojovací řetězec* a zobrazí se podobné zobrazení jako v následujícím příkladu:

    ![Snímek obrazovky s přihlašovacími údaji Azure Cosmos DB](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Host* – Azure Cosmos DB koncový bod funguje jako název hostitele MongoDB
    * *Port* – když se Azure Cosmos DB nativní nástroje MongoDB připojit k, musíte tento port explicitně zadat.
    * *Username* – Předpona názvu domény Azure Cosmos DB koncového bodu funguje jako uživatelské jméno MongoDB
    * *Heslo* – hlavní klíč Azure Cosmos DB funguje jako heslo MongoDB.
    * Dále si poznamenejte pole *SSL* , které je `true` -MongoDB Native Tool **musí** při zápisu dat do Azure Cosmos DB povolit protokol SSL.

## <a name="perform-the-migration"></a>Provedení migrace

1. Vyberte databáze, které chcete migrovat, a kolekce (y). V tomto příkladu migrujeme kolekci *dotazů* v databázi *EDX* z MongoDB na Azure Cosmos DB.

Zbytek této části vás provede použitím dvojice nástrojů, které jste vybrali v předchozí části.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Pokud chcete exportovat data ze zdrojové instance MongoDB, otevřete terminál na počítači instance MongoDB. Pokud se jedná o počítač se systémem Linux, zadejte

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Ve Windows bude spustitelný soubor `mongoexport.exe` . Na základě vlastností existující instance databáze MongoDB by mělo být zadáno *hostitel*, *port*, *uživatelské jméno* a *heslo* . 
    
    Můžete se také rozhodnout exportovat pouze podmnožinu MongoDB datové sady. Jedním ze způsobů, jak to udělat, je přidání dalšího argumentu filtru:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Budou exportovány pouze dokumenty, které odpovídají filtru `{"field1":"value1"}` .

    Po spuštění volání byste měli vidět, že `edx.json` je vytvořen soubor:

    ![Snímek obrazovky s voláním mongoexport](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Ke importování do Azure Cosmos DB můžete použít stejného terminálu `edx.json` . Pokud používáte počítač se systémem `mongoimport` Linux, zadejte

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Ve Windows bude spustitelný soubor `mongoimport.exe` . *Hostitel*, *port*, *uživatelské jméno* a *heslo* by se měly vyplnit na základě dříve shromážděných přihlašovacích údajů Azure Cosmos DB. 
1. **Monitorujte** výstup terminálu z *mongoimport*. Měli byste vidět, že v terminálu se zobrazí řádky textu obsahující aktualizace stavu migrace:

    ![Snímek obrazovky s voláním mongoimport](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Nakonec zkontrolujte Azure Cosmos DB a **Ověřte** , jestli migrace proběhla úspěšně. Otevřete portál Azure Cosmos DB a přejděte na Průzkumník dat. Měli byste vidět (1), že se vytvořila databáze *EDX* s kolekcí *importedQuery* a (2) Pokud jste exportovali jenom podmnožinu dat, *importedQuery* by měla obsahovat *jenom* dokumenty odpovídající požadované podmnožině dat. V následujícím příkladu se filtr shodoval pouze s jedním dokumentem `{"field1":"value1"}` :

    ![Snímek obrazovky s ověřením dat Cosmos DB](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Pokud chcete vytvořit výpis dat BSON vaší instance MongoDB, otevřete terminál na počítači instance MongoDB. Pokud se jedná o počítač se systémem Linux, zadejte

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    Na základě vlastností existující instance databáze MongoDB by mělo být zadáno *hostitel*, *port*, *uživatelské jméno* a *heslo* . Měli byste vidět, že `edx-dump` se vytvořil adresář a že adresářová struktura `edx-dump` reprodukuje hierarchii prostředků (databáze a struktury kolekcí) zdrojové instance MongoDB. Jednotlivé kolekce jsou reprezentovány souborem BSON:

    ![Snímek obrazovky s voláním mongodump](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Stejný terminál můžete použít k obnovení obsahu `edx-dump` do Azure Cosmos DB. Pokud používáte počítač se systémem `mongorestore` Linux, zadejte

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Ve Windows bude spustitelný soubor `mongorestore.exe` . *Hostitel*, *port*, *uživatelské jméno* a *heslo* by se měly vyplnit na základě dříve shromážděných přihlašovacích údajů Azure Cosmos DB. 
1. **Monitorujte** výstup terminálu z *mongorestore*. Měli byste vidět, že na stav migrace se budou zobrazovat řádky s aktualizací terminálu:

    ![Snímek obrazovky s voláním mongorestore](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Nakonec zkontrolujte Azure Cosmos DB a **Ověřte** , jestli migrace proběhla úspěšně. Otevřete portál Azure Cosmos DB a přejděte na Průzkumník dat. Měli byste vidět (1), že se vytvořila databáze *EDX* s kolekcí *importedQuery* a (2) *importedQuery* by měla obsahovat *celou* datovou sadu ze zdrojové kolekce:

    ![Snímek obrazovky s ověřením Cosmos DB mongorestore data](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Optimalizace po migraci

Po migraci dat uložených v databázi MongoDB Azure Cosmos DB k rozhraní API pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Můžete také provést další kroky optimalizace po migraci, jako je například optimalizace zásad indexování, aktualizace výchozí úrovně konzistence nebo konfigurace globální distribuce pro účet Azure Cosmos DB. Další informace najdete v článku věnovaném [optimalizaci po migraci](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Další zdroje informací

* [Informace o Cosmos DB službě](https://azure.microsoft.com/services/cosmos-db/)
* [Dokumentace k nástrojům pro MongoDB Database](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Další kroky

* Projděte si pokyny k migraci dalších scénářů v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.