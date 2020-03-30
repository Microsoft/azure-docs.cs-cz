---
title: Migrace stovek terabajtů dat do Azure Cosmos DB
description: Tento dokument popisuje, jak můžete migrovat 100s terabajtů dat do Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880205"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrace stovek terabajtů dat do Azure Cosmos DB 

Azure Cosmos DB může uchovávat terabajty dat. Můžete provést rozsáhlou migraci dat a přesunout svou produkční úlohu do služby Azure Cosmos DB. Tento článek popisuje výzvy spojené s přesunem velkých objemů dat do služby Azure Cosmos DB a představuje nástroj, který pomáhá tyto výzvy řešit a migruje data do služby Azure Cosmos DB. V této případové studii zákazník použil rozhraní SQL API služby Cosmos DB.  

Před migrací celé úlohy do Azure Cosmos DB můžete migrovat podmnožinu dat a ověřit některé aspekty, jako je volba klíče oddílu, výkon dotazu a modelování dat. Po ověření ověření konceptu můžete přesunout celou úlohu do Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Nástroje pro migraci dat 

Strategie migrace Azure Cosmos DB se aktuálně liší v závislosti na volbě rozhraní API a velikosti dat. Chcete-li migrovat menší datové sady – pro ověřování datového modelování, [Data Migration Tool](import-data.md) výkonu dotazů, volby klíče oddílu atd. [Azure Data Factory’s Azure Cosmos DB connector](../data-factory/connector-azure-cosmos-db.md) Pokud jste obeznámeni s Spark, můžete také zvolit použití [konektoru Azure Cosmos DB Spark](spark-connector.md) k migraci dat.

## <a name="challenges-for-large-scale-migrations"></a>Výzvy pro rozsáhlé migrace 

Stávající nástroje pro migraci dat do Azure Cosmos DB mají určitá omezení, která se projeví zejména ve velkém měřítku:

 * **Omezené horizontální navýšení kapacity**: Chcete-li migrovat terabajty dat do služby Azure Cosmos DB co nejrychleji a efektivně využívat celou zřízenou propustnost, klienti migrace by měli mít možnost škálovat na neurčito.  

* **Nedostatečné sledování průběhu a ukazování kontrolou**: Je důležité sledovat průběh migrace a při migraci velkých datových sad ukazování ukazování kontroly. V opačném případě jakákoli chyba, ke které dojde během migrace, zastaví migraci a je třeba spustit proces od začátku. Nebylo by produktivní restartovat celý proces migrace, když 99 % z něj již bylo dokončeno.  

* **Nedostatek fronty nedoručených zpráv**: V rámci velkých datových sad může v některých případech nacházet problémy s částmi zdrojových dat. Navíc může být přechodné problémy s klientem nebo sítě. Jeden z těchto případů by neměl způsobit selhání celé migrace. I když většina nástrojů pro migraci má robustní možnosti opakování, které chrání před občasnými problémy, není to vždy dostačovací. Například pokud méně než 0,01 % zdrojových dat dokumenty jsou větší než 2 MB velikosti, způsobí, že zápis dokumentu nezdaří v Azure Cosmos DB. V ideálním případě je užitečné pro nástroj pro migraci zachovat tyto 'selhalé' dokumenty do jiné fronty nedoručených zpráv, které mohou být zpracovány po migraci. 

Mnoho z těchto omezení se opravuje pro nástroje, jako je Azure Data Factory, služby migrace dat Azure. 

## <a name="custom-tool-with-bulk-executor-library"></a>Vlastní nástroj s knihovnou hromadného prováděcího modulu 

Problémy popsané ve výše uvedené části lze vyřešit pomocí vlastního nástroje, který lze snadno škálovat napříč více instancemi a je odolný vůči přechodným selháním. Vlastní nástroj navíc může pozastavit a obnovit migraci na různých kontrolních bodech. Azure Cosmos DB již poskytuje [knihovnu hromadného vykonavatele,](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) která zahrnuje některé z těchto funkcí. Například knihovna hromadného vykonavatele již má funkce pro zpracování přechodných chyb a může škálovat vlákna v jednom uzlu tak, aby spotřebovávaly přibližně 500 K RU na uzel. Knihovna hromadného vykonavatele také rozdělí zdrojovou datovou sadu do mikrodávek, které jsou provozovány nezávisle jako forma kontrolního bodu.  

Vlastní nástroj používá knihovnu hromadného vykonavatele a podporuje horizontální navýšení kapacity mezi více klienty a ke sledování chyb během procesu ingestování. Chcete-li použít tento nástroj, zdrojová data by měla být rozdělena do různých souborů v Azure Data Lake Storage (ADLS), aby různí pracovníci migrace mohli vyzvednout každý soubor a ingestovat je do Azure Cosmos DB. Vlastní nástroj využívá samostatné kolekce, která ukládá metadata o průběhu migrace pro každý jednotlivý zdrojový soubor v ADLS a sleduje všechny chyby s nimi spojené.  

Následující obrázek popisuje proces migrace pomocí tohoto vlastního nástroje. Nástroj běží na sadě virtuálních počítačů a každý virtuální počítač se dotazuje na kolekci sledování v Azure Cosmos DB získat zapůjčení na jednom ze zdrojových datových oddílů. Jakmile je to hotovo, zdrojový datový oddíl se čte nástrojem a ingestován do Azure Cosmos DB pomocí knihovny hromadného prováděcího modulu. Dále sledování kolekce je aktualizován a zaznamenat průběh přijímaní dat a všechny chyby došlo. Po zpracování datového oddílu se nástroj pokusí dotazovat na další dostupný zdrojový oddíl. Pokračuje ve zpracování dalšího zdrojového oddílu, dokud nebudou migrována všechna data. Zdrojový kód nástroje je k dispozici [zde](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion).  

 
![Nastavení nástroje pro migraci](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

Kolekce sledování obsahuje dokumenty, jak je znázorněno v následujícím příkladu. Tyto dokumenty se zobrazí jeden pro každý oddíl ve zdrojových datech.  Každý dokument obsahuje metadata pro zdrojový datový oddíl, jako je jeho umístění, stav migrace a chyby (pokud existuje):  

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

Před zahájením migrace dat existuje několik předpokladů, které je třeba zvážit:  

#### <a name="estimate-the-data-size"></a>Odhad velikosti dat:  

Velikost zdrojových dat nemusí přesně mapovat na velikost dat v Azure Cosmos DB. Několik ukázkových dokumentů ze zdroje lze vložit ke kontrole jejich velikosti dat v Azure Cosmos DB. V závislosti na velikosti ukázkového dokumentu lze odhadnout celkovou velikost dat v Azure Cosmos DB po migraci. 

Například pokud každý dokument po migraci v Azure Cosmos DB je kolem 1 KB a pokud existuje přibližně 60 miliard dokumentů ve zdrojové datové sady, znamenalo by to, že odhadovaná velikost v Azure Cosmos DB by se blížila 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Předvytvoření kontejnerů s dostatkem ru: 

Přestože Azure Cosmos DB škáluje úložiště automaticky, není vhodné začít od nejmenší velikost kontejneru. Menší kontejnery mají nižší dostupnost propustnost, což znamená, že migrace bude trvat mnohem déle. Místo toho je užitečné vytvořit kontejnery s konečnou velikostí dat (jak se odhaduje v předchozím kroku) a ujistěte se, že úloha migrace plně spotřebovává zřízenou propustnost.  

V předchozím kroku. vzhledem k tomu, že velikost dat byla odhadnuta na přibližně 60 TB, je pro přizpůsobení celé datové sady vyžadován kontejner s nejméně 2,4 m ru.  

 

#### <a name="estimate-the-migration-speed"></a>Odhad rychlosti migrace: 

Za předpokladu, že úloha migrace může spotřebovat celou zřízenou propustnost, zřízené v rámci by poskytlo odhad rychlosti migrace. Pokračování v předchozím příkladu 5 RU jsou vyžadovány pro zápis dokumentu 1 KB do účtu Azure Cosmos DB SQL API.  2,4 milionu ru by umožnilo převod 480 000 dokumentů za sekundu (nebo 480 MB/s). To znamená, že úplná migrace 60 TB bude trvat 125 000 sekund nebo přibližně 34 hodin.  

V případě, že chcete, aby migrace byla dokončena během jednoho dne, měli byste zvýšit zřízenou propustnost na 5 milionů ru. 

 

#### <a name="turn-off-the-indexing"></a>Vypněte indexování:  

Vzhledem k tomu, že migrace by měla být dokončena co nejdříve, je vhodné minimalizovat čas a ru vynaložené na vytváření indexů pro každý z pozůstalých dokumentů.  Azure Cosmos DB automaticky indexuje všechny vlastnosti, stojí za to minimalizovat indexování na několik vybraných termínů nebo ho úplně vypnout pro průběh migrace. Zásady indexování kontejneru můžete vypnout změnou režimu indexování na žádný, jak je znázorněno níže:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Po dokončení migrace můžete indexování aktualizovat.  

## <a name="migration-process"></a>Proces migrace 

Po dokončení požadavků můžete migrovat data pomocí následujících kroků:  

1. Nejprve importujte data ze zdroje do úložiště objektů blob Azure. Chcete-li zvýšit rychlost migrace, je užitečné paralelizovat mezi různých zdrojových oddílů. Před zahájením migrace by měla být sada zdrojových dat rozdělena do souborů o velikosti přibližně 200 MB.   

2. Knihovna hromadného vykonavatele můžete vertikálně navýšit kapacitu, spotřebovávat 500 000 ru v jednom virtuálním virtuálním virtuálním připojení klienta. Vzhledem k tomu, že dostupná propustnost je 5 milionů ru, 10 virtuálních počítačích Ubuntu 16.04 (Standard_D32_v3) by se mělo zřídit ve stejné oblasti, kde je umístěna vaše databáze Azure Cosmos. Tyto virtuální počítače byste měli připravit pomocí nástroje pro migraci a jeho souboru nastavení.  

3. Spusťte krok fronty na jednom z virtuálních počítačů klienta. Tento krok vytvoří kolekci sledování, která prohledává kontejner ADLS a vytvoří dokument sledování průběhu pro každý soubor oddílů zdrojové datové sady.  

4. Dále spusťte krok importu na všech klientských virtuálních počítačích. Každý z klientů můžete převzít vlastnictví na zdrojový oddíl a ingestovat jeho data do Azure Cosmos DB. Po dokončení a jeho stav je aktualizován v kolekci sledování, klienti pak můžete dotaz na další dostupný zdrojový oddíl v kolekci sledování.  

5. Tento proces pokračuje, dokud byla ingestována celá sada zdrojových oddílů. Po zpracování všech zdrojových oddílů by měl být nástroj znovu spuštěn v režimu opravy chyb ve stejné kolekci sledování. Tento krok je nutné k identifikaci zdrojové oddíly, které by měly být znovu zpracovány z důvodu chyb.  

6. Některé z těchto chyb může být způsobeno nesprávné dokumenty ve zdrojových datech. Ty by měly být identifikovány a stanoveny. Dále byste měli znovu spustit krok importu na neúspěšných oddílech, abyste je znovu získali. 

Po dokončení migrace můžete ověřit, že počet dokumentů v Azure Cosmos DB je stejný jako počet dokumentů ve zdrojové databázi. V tomto příkladu se celková velikost v Azure Cosmos DB ukázala na 65 terabajtů. Po migraci lze indexování selektivně zapnout a ru mohou být sníženy na úroveň požadovanou operacemi úlohy.

## <a name="contact-the-azure-cosmos-db-team"></a>Kontaktování týmu Azure Cosmos DB
I když můžete postupovat podle tohoto průvodce úspěšně migrovat velké datové sady do Azure Cosmos DB, pro rozsáhlé migrace, doporučujeme oslovit produktový tým Azure Cosmos DB k ověření modelování dat a obecné přezkoumání architektury. Na základě vaší datové sady a pracovního vytížení může produktový tým také navrhnout další optimalizace výkonu a nákladů, které by se na vás mohly vztahovat. Chcete-li kontaktovat tým Azure Cosmos DB o pomoc s migrací ve velkém měřítku, můžete otevřít lístek podpory v rámci typu problému "Obecné poradenství" a podtypu "Velké (TB+) migrace, jak je znázorněno níže.

![Téma podpory migrace](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Další kroky

* Další informace naleznete v vyzkoušení ukázkových aplikací, které spotřebovávají knihovnu hromadných prováděcích modulů v [rozhraní .NET](bulk-executor-dot-net.md) a [Vja](bulk-executor-java.md). 
* Knihovna hromadného prováděcího modulu je integrovaná do konektoru Cosmos DB Spark, abyste se dozvěděli další informace, přečtěte si článek [konektoru Azure Cosmos DB Spark.](spark-connector.md)  
* Obraťte se na produktový tým Azure Cosmos DB otevřením lístku podpory v rámci typu problému "Obecné poradenství" a "Velké (TB+) migrace podtyp pro další pomoc s migrací ve velkém měřítku. 
