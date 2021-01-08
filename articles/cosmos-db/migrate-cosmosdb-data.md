---
title: Migrace stovek terabajtů dat do Azure Cosmos DB
description: Tento dokument popisuje, jak můžete migrovat stovky terabajtů dat do Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: b24ea79737c9e1f64abb7f62807352dbd9573695
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018067"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrace stovek terabajtů dat do Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB může uchovávat terabajty dat. Můžete provést rozsáhlou migraci dat a přesunout svou produkční úlohu do služby Azure Cosmos DB. Tento článek popisuje výzvy spojené s přesunem velkých objemů dat do služby Azure Cosmos DB a představuje nástroj, který pomáhá tyto výzvy řešit a migruje data do služby Azure Cosmos DB. V této případové studii zákazník použil rozhraní SQL API služby Cosmos DB.  

Před migrací celé úlohy na Azure Cosmos DB můžete migrovat podmnožinu dat a ověřit některé aspekty, jako je například volba klíče oddílu, výkon dotazů a modelování dat. Po ověření konceptu konceptu můžete přesunout celou úlohu na Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Nástroje pro migraci dat 

Azure Cosmos DB strategie migrace se aktuálně liší podle volby rozhraní API a velikosti dat. Migrace menších datových sad – pro ověřování modelování dat, výkonu dotazů, výběru klíče oddílu atd. – můžete zvolit [Nástroj pro migraci dat](import-data.md) nebo [konektor Azure Cosmos DB Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Pokud máte zkušenosti s Sparkem, můžete také použít [konektor Azure Cosmos DB Spark](spark-connector.md) k migraci dat.

## <a name="challenges-for-large-scale-migrations"></a>Problémy s migrací ve velkém měřítku 

Stávající nástroje pro migraci dat na Azure Cosmos DB mají určitá omezení, která se budou obzvláště vymezit velkými škálami:

 * **Omezené možnosti horizontálního** navýšení kapacity: aby bylo možné migrovat terabajty dat do Azure Cosmos DB co nejrychleji a efektivně spotřebovat celou zřízenou propustnost, klienti migrace by měli mít možnost horizontálního navýšení kapacity navýšit na neomezenou dobu.  

* **Nedostatek sledování průběhu a vracení se změnami**: je důležité sledovat průběh migrace a vracet se změnami při migraci velkých datových sad. V opačném případě všechny chyby, ke kterým dojde během migrace, zastaví migraci a vy budete muset proces začít od začátku. Nepovedlo se vám neproduktivní restartování celého procesu migrace, až 99% z něj už je dokončený.  

* **Nedostatek fronty nedoručených zpráv**: v rámci velkých datových sad může v některých případech dojít k problémům s částmi zdrojových dat. Kromě toho může docházet k přechodným problémům s klientem nebo sítí. Některé z těchto případů by nemělo způsobit selhání celé migrace. I když většina nástrojů pro migraci má robustní možnosti opakovaného pokusů, které chrání před přerušovanými problémy, není vždy dostatek. Pokud je například méně než 0,01% zdrojových datových dokumentů větší než 2 MB, způsobí to, že zápis dokumentu selže v Azure Cosmos DB. V ideálním případě je vhodné, aby nástroj pro migraci zachoval tyto "neúspěšné" dokumenty do jiné fronty nedoručených zpráv, která může být zpracována po migraci. 

Mnohé z těchto omezení se stanovují pro nástroje, jako je Azure Data Factory, služby Azure Data Migration Services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Vlastní nástroj s knihovnou hromadných prováděcích modulů 

Výzvy popsané v předchozí části se dají vyřešit pomocí vlastního nástroje, který je možné snadno škálovat napříč několika instancemi a je odolný vůči přechodným chybám. Navíc může vlastní nástroj pozastavit a obnovit migraci v různých kontrolních bodech. Azure Cosmos DB již poskytuje [knihovnu hromadného prováděcího modulu](./bulk-executor-overview.md) , která zahrnuje některé z těchto funkcí. Například knihovna hromadného prováděcího modulu již má funkce pro zpracování přechodných chyb a může škálovat vlákna v jednom uzlu, aby se spotřeboval přibližně 500 K ru na uzel. Knihovna hromadného prováděcího modulu také rozdělí zdrojovou datovou sadu do mikrodávkování, které se provozují nezávisle jako forma kontrolního bodu.  

Vlastní nástroj používá knihovnu hromadného prováděcího modulu a podporuje horizontální navýšení kapacity mezi více klienty a sledování chyb během procesu příjmu. Aby bylo možné použít tento nástroj, zdrojová data by měla být rozdělená do samostatných souborů v Azure Data Lake Storage (ADLS), aby různé procesy migrace mohly každý soubor vybírat a ingestovat do Azure Cosmos DB. Vlastní nástroj využívá samostatnou kolekci, která ukládá metadata o průběhu migrace pro každý jednotlivý zdrojový soubor v ADLS a sleduje případné chyby, které jsou k nim přidruženy.  

Následující obrázek popisuje proces migrace pomocí tohoto vlastního nástroje. Nástroj je spuštěný v sadě virtuálních počítačů a každý virtuální počítač se dotazuje kolekce sledování v Azure Cosmos DB, aby získal zapůjčení na jednom ze zdrojových datových oddílů. Až to uděláte, zdrojový oddíl dat přečte nástroj a ingestuje se do Azure Cosmos DB pomocí knihovny hromadného prováděcího modulu. V dalším kroku se kolekce sledování aktualizuje, aby se zaznamenal průběh přijímání dat a případné chyby. Po zpracování datového oddílu se nástroj pokusí zadat dotaz na další dostupný zdrojový oddíl. I nadále zpracovává další zdrojový oddíl, dokud nebudou všechna data migrována. Zdrojový kód pro nástroj je k dispozici v úložišti [Azure Cosmos DB hromadných přijímání](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion) .  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Nastavení nástroje pro migraci" border="false":::
 

 

Kolekce sledování obsahuje dokumenty, jak je znázorněno v následujícím příkladu. Tyto dokumenty se zobrazí pro každý oddíl ve zdrojových datech.  Každý dokument obsahuje metadata pro oddíl zdrojových dat, jako je jeho umístění, stav migrace a chyby (pokud existují):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Předpoklady pro migraci dat 

Před zahájením migrace dat je potřeba vzít v úvahu několik požadavků:  

#### <a name="estimate-the-data-size"></a>Odhadnout velikost dat:  

Velikost zdrojových dat nemusí být přesně namapována na velikost dat v Azure Cosmos DB. Můžete vložit několik ukázkových dokumentů ze zdroje a ověřit jejich velikost dat v Azure Cosmos DB. V závislosti na velikosti dokumentu můžete odhadnout celkovou velikost dat v Azure Cosmos DB po migraci. 

Pokud je například každý dokument po migraci v Azure Cosmos DB o velikosti přibližně 1 KB a v případě, že ve zdrojové datové sadě máte přibližně 60 000 000 000 dokumentů, znamená to, že Předpokládaná velikost v Azure Cosmos DB by byla blízko 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Předem vytvořit kontejnery s dostatečným ru: 

I když Azure Cosmos DB škálovat úložiště automaticky, není vhodné začít od nejmenší velikosti kontejneru. Menší kontejnery mají nízkou propustnost, což znamená, že dokončení migrace by trvat mnohem delší dobu. Místo toho je vhodné vytvořit kontejnery s konečnou velikostí dat (podle odhadu v předchozím kroku) a ujistit se, že úlohy migrace plně spotřebovávají zřízenou propustnost.  

V předchozím kroku. vzhledem k tomu, že velikost dat byla odhadnuta přibližně 60 TB, je nutné, aby se pro celou datovou sadu vešl kontejner aspoň 2,4 M ru.  

 

#### <a name="estimate-the-migration-speed"></a>Odhad rychlosti migrace: 

Za předpokladu, že úloha migrace může spotřebovat celou zřízenou propustnost, poskytne vám v celém rozsahu odhad rychlosti migrace. Když budete pokračovat v předchozím příkladu, k vytvoření dokumentu 1 KB pro Azure Cosmos DB účtu rozhraní SQL API se vyžaduje 5 ru.  2 400 000 ru povolí přenos 480 000 dokumentů za sekundu (nebo 480 MB/s). To znamená, že dokončení migrace 60 TB bude trvat 125 000 sekund nebo asi 34 hodin.  

Pokud chcete, aby se migrace dokončila během dne, měli byste zvýšit propustnost na 5 000 000 ru. 

 

#### <a name="turn-off-the-indexing"></a>Vypnutí indexování:  

Vzhledem k tomu, že by migrace měla být dokončena co nejdříve, doporučuje se minimalizovat čas a ru strávený na vytváření indexů pro každý z přijatých dokumentů.  Azure Cosmos DB automaticky indexuje všechny vlastnosti, je vhodné minimalizovat indexování na vybrané pár podmínek nebo je úplně vypnout pro migraci. Zásady indexování kontejneru můžete vypnout změnou indexingMode na None, jak je znázorněno níže:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Po dokončení migrace můžete rejstřík aktualizovat.  

## <a name="migration-process"></a>Proces migrace 

Po dokončení požadovaných součástí můžete migrovat data pomocí následujících kroků:  

1. Nejdřív importujte data ze zdroje do Azure Blob Storage. Pro zvýšení rychlosti migrace je užitečné paralelizovat napříč různými zdrojovými oddíly. Než začnete s migrací, měla by být zdrojová datová sada rozdělená do souborů o velikosti přibližně 200 MB.   

2. Knihovna hromadného prováděcího modulu se může škálovat nahoru a spotřebovat 500 000 ru v jednom virtuálním počítači klienta. Vzhledem k tomu, že dostupná propustnost je 5 000 000 ru, je třeba zřídit 10 virtuálních počítačů Ubuntu 16,04 (Standard_D32_v3) ve stejné oblasti, ve které se nachází vaše databáze Azure Cosmos. Tyto virtuální počítače byste měli připravit pomocí nástroje pro migraci a souboru nastavení.  

3. Spusťte krok Queue na jednom z klientských virtuálních počítačů. Tento krok vytvoří shromažďování sledování, který prohledá kontejner ADLS a vytvoří dokument pro sledování průběhu pro každý soubor oddílu zdrojové datové sady.  

4. Dále spusťte krok import na všech virtuálních počítačích klienta. Každý z klientů může převzít vlastnictví zdrojového oddílu a ingestovat jeho data do Azure Cosmos DB. Po dokončení a jeho stav se aktualizuje v kolekci sledování, klienti si pak mohou dotazovat na další dostupný zdrojový oddíl v kolekci sledování.  

5. Tento proces pokračuje, dokud nedošlo k ingestování celé sady zdrojových oddílů. Až budou všechny zdrojové oddíly zpracovávány, měl by být tento nástroj znovu spuštěn v režimu opravy chyb v rámci stejné kolekce sledování. Tento krok je nutný k identifikaci zdrojových oddílů, které by se měly znovu zpracovat z důvodu chyb.  

6. Některé z těchto chyb mohou být způsobeny nesprávnými dokumenty ve zdrojových datech. Ty by měly být identifikovány a opraveny. Dále byste měli znovu spustit krok importu u neúspěšných oddílů a znovu je přijmout. 

Po dokončení migrace můžete ověřit, že je počet dokumentů v Azure Cosmos DB stejný jako počet dokumentů ve zdrojové databázi. V tomto příkladu je celková velikost v Azure Cosmos DB zapnula na 65 terabajty. Po migraci je možné indexování selektivně zapnout a ru se dá snížit na úroveň požadovanou operacemi úloh.

## <a name="next-steps"></a>Další kroky

* Další informace získáte vyzkoušením ukázkových aplikací, které využívají knihovnu hromadných prováděcích modulů v jazycích [.NET](bulk-executor-dot-net.md) a [Java](bulk-executor-java.md). 
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark. Další informace najdete v článku [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Obraťte se na Azure Cosmos DB produktového týmu otevřením lístku podpory v části problémový typ problému "Obecné poradenství" a "velké (TB +) migrace" pro další nápovědu k migracím ve velkém měřítku.