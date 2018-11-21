---
title: Automatické, online zálohování a dat na vyžádání obnovení ve službě Azure Cosmos DB
description: Tento článek popisuje, jak automatické, online zálohování a dat na vyžádání obnovení ve službě Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 39c4a6108f4a5133e2c77904dcd67bf235801956
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265130"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Zálohování online a na vyžádání dat obnovení ve službě Azure Cosmos DB

Azure Cosmos DB automaticky provede zálohování dat v pravidelných intervalech. Automatické zálohy jsou prováděny bez vlivu na výkon nebo dostupnost databázových operací. Všechny zálohy jsou uloženy zvlášť ve službě úložiště a tyto zálohy jsou globálně replikuje odolnosti proti místní havárií. Automatické zálohování jsou užitečné v situacích, pokud omylem odstraníte nebo aktualizovat účet služby Azure Cosmos, databáze nebo kontejneru a později vyžadují obnovení dat.

## <a name="automatic-and-online-backups"></a>Automatické a online zálohování

Pomocí služby Azure Cosmos DB jsou pouze data, ale také zálohy dat vysoce redundantní a odolná proti selháním regionální havárií. Automatizované zálohy jsou prováděny aktuálně každé čtyři hodiny a v libovolném bodě čas poslední dvě zálohy jsou uložené. Pokud jste omylem odstraněn nebo poškozen vaše data, měli byste požádat [podpory Azure](https://azure.microsoft.com/support/options/) do osmi hodin tak, aby tým služby Azure Cosmos DB může pomoci obnovíte data ze zálohy.

Zálohy jsou prováděny bez vlivu na výkon nebo dostupnost vaší aplikace. Azure Cosmos DB provede zálohování dat na pozadí bez použití jakékoli další zřízená propustnost (ru) nebo které mají vliv výkon a dostupnost vaší databáze.

Azure Cosmos DB ukládá automatické zálohování ve službě Azure Blob Storage, že jsou skutečná data uložená místně v rámci služby Azure Cosmos DB. Pro zajištění nízké latence, snímek zálohy je uložen v úložišti objektů Blob v Azure ve stejné oblasti jako aktuální oblasti pro zápis (nebo jeden z oblasti zápisu, pokud máte více hlavních konfiguraci) účet databáze Cosmos DB. Což zvyšuje odolnost vůči regionálního jednotlivých snímků zálohování dat v úložišti objektů Blob v Azure znovu replikovat do jiné oblasti pomocí geograficky redundantního úložiště (GRS). Oblast, do které se replikují zálohování je na základě zdrojové oblasti a pár oblastí přidružené zdrojové oblasti. Další informace najdete v tématu [seznam dvojic geograficky redundantní oblastí Azure](../best-practices-availability-paired-regions.md) článku. Tuto zálohu nelze přistupovat přímo. Azure Cosmos DB bude používat tato záloha pouze v případě, že zahájila obnovení zálohy.
Následující obrázek ukazuje, jak je kontejner služby Azure Cosmos se všechny tři oddíly primární prostředek v oblasti západní USA zazálohovaný ve vzdálené účtu Azure Blob Storage v oblasti západní USA a pak replikují do USA – východ:

![Pravidelné úplné zálohy všechny entity Cosmos DB v geograficky Redundantního úložiště Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Možnosti Správa vlastních záloh

S účty SQL API služby Azure Cosmos DB můžete také spravovat vlastní zálohy pomocí jedné z následujících postupů:

* Použití [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pravidelně přesun dat do úložiště podle vašeho výběru.

* Pomocí služby Azure Cosmos DB [kanálu změn](change-feed.md) pravidelně čtení dat pro úplné zálohování, stejně jako na přírůstkové změny a uložte ho ve svém vlastním úložišti.

## <a name="backup-retention-period"></a>Období uchování zálohy

Azure Cosmos DB trvá snímky dat každé čtyři hodiny. V každém okamžiku pouze posledních dvou snímky, zůstanou zachovány. Pokud však odstranění kontejneru nebo databáze Azure Cosmos DB zůstane existující snímky daného kontejneru nebo databáze po dobu 30 dnů.

## <a name="restoring-data-from-online-backups"></a>Obnovení dat z online záloh

Náhodnému odstranění nebo změna dat může stát v jednom z následujících scénářů:  

* Odstraní celý účet Azure Cosmos

* Jeden nebo více databází Azure Cosmos se odstraní.

* Jeden nebo více kontejnerů Azure Cosmos se odstraní.

* Azure Cosmos položek (například dokumenty) v rámci kontejneru jsou odstraněna nebo upravena. Tento konkrétní případ se obvykle označuje jako "poškození dat".

* Nabídka sdílené databáze nebo kontejnery v rámci nabídky sdílené databáze odstraněn nebo poškozen

Azure Cosmos DB můžete obnovit data ve všech výše uvedených scénářích. Proces obnovení vždycky vytvoří nový účet Azure Cosmos pro obnovená data. Název nového účtu, pokud není zadán, bude mít formát `<Azure_Cosmos_account_original_name>-restored1`. Poslední číslice se zvýší, pokud více obnoví nedochází k pokusům o. Nelze obnovit data do předem vytvořeném účtu Azure Cosmos.

Při odstranění účtu Azure Cosmos, jsme obnovit data do účtu se stejným názvem, za předpokladu, že název účtu není používán. V takových případech se doporučuje není znovu vytvořte účet po odstranění, protože nejen brání obnovená data používat se stejným názvem, ale rovněž provede zjišťování pravý účet obnovit z obtížnější. 

Při odstranění databáze Azure Cosmos je možné obnovit celé databáze nebo podmnožinu kontejnery v rámci této databáze. Je také možné vybrat kontejnery v databázích a obnovit je a všechno, co se obnovená data nachází v novém účtu Azure Cosmos.

Při jedné nebo více položek v rámci kontejneru jsou náhodném odstranění nebo změně (případ poškození dat), je potřeba zadat čas obnovení. Čas je essence pro tento případ. Protože kontejneru je v provozu, zálohování je stále spuštěna, tak pokud čekáte po uplynutí doby uchování (výchozí hodnota je 8 hodin) zálohy budou přepsána. V případě odstranění, vaše data jsou už uložená protože nesmí být přepsána zálohování cyklu. Zálohy pro odstraněné databáze nebo kontejnery jsou uložena po dobu 30 dnů.

Pokud zřizujete propustnosti na úrovni databáze (to znamená, kde sadu kontejnerů sdílí zřízená propustnost), proces zálohování a obnovení v tomto případě dochází na úrovni celé databáze a ne na úrovni jednotlivých kontejnerů. V takových případech výběrem podmnožiny kontejnery obnovení není možné.

## <a name="migrating-data-to-the-original-account"></a>Migrace dat do původního účtu

Primární cíl obnovení dat je poskytnout způsob, jak obnovit všechna data, která Nemazat ani neupravovat omylem. Proto doporučujeme nejprve zkontrolovat obsah obnovená data k zajištění, že obsahuje, co jste očekávali. Pak práce na migraci dat zpět do primárního účtu. I když je možné použít obnovený účet jako účet za provozu, není doporučená možnost máte úlohy v produkčním prostředí.  

Následují různé způsoby, jak migrovat data zpět do původního účtu Azure Cosmos:

* Pomocí [nástroj pro migraci dat Cosmos DB](import-data.md)
* Pomocí [služby Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Pomocí [kanálu změn](change-feed.md) ve službě Azure Cosmos DB 
* Psát vlastní kód

Odstranit obnovenou účty ihned poté, co dokončíte migraci, protože se bude zbytečně nenabíhaly poplatky.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak obnovit data z účtu služby Azure Cosmos, nebo zjistěte, jak migrovat data do účtu Azure Cosmos

* Chcete-li obnovení žádostí, obraťte se na podporu Azure, [lístek na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Postup obnovení dat z účtu služby Azure Cosmos](how-to-backup-and-restore.md)
* [Cosmos DB pomocí kanálu změn](change-feed.md) pro přesun dat do služby Azure Cosmos DB.
* [Použití Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pro přesun dat do služby Azure Cosmos DB.

