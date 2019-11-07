---
title: Řešení potíží s Azure Data Factory toky dat
description: Naučte se řešit problémy toku dat v Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 1b2309ec71cb3d43f4e5a39b80db593ab201c614
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721344"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Řešení potíží s Azure Data Factory toky dat

Tento článek popisuje běžné metody řešení potíží pro toky dat v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Chybová zpráva: DF-SYS-01: vystínované. datacihly. org. Apache. Hadoop. FS. Azure. AzureException: com. Microsoft. Azure. Storage. StorageException: zadaný kontejner neexistuje.

- **Příznaky**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.

- **Příčina**: když DataSet obsahuje kontejner, který v úložišti neexistuje.

- **Řešení**: Ujistěte se, že kontejner, na který odkazujete, existuje ve vaší datové sadě.

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Chybová zpráva: DF-SYS-01: Java. lang. AssertionError: kontrolní výraz se nezdařil: byly zjištěny konfliktní struktury adresáře. Podezřelé cesty

- **Příznaky**: při použití zástupných znaků ve zdrojové transformaci pomocí souborů Parquet

- **Příčina**: nesprávná nebo neplatná syntaxe zástupných znaků

- **Řešení**: Podívejte se na zástupnou syntaxi, kterou používáte v možnostech transformace zdroje.

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Chybová zpráva: DF-SRC-002: Container (název kontejneru) je povinný.

- **Příznaky**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.

- **Příčina**: když DataSet obsahuje kontejner, který v úložišti neexistuje.

- **Řešení**: Ujistěte se, že kontejner, na který odkazujete, existuje ve vaší datové sadě.

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Chybová zpráva: DF-UNI-001: PrimaryKeyValue má nekompatibilní typy IntegerType a StringType

- **Příznaky**: spuštění toku dat ve verzi Preview, ladění a kanálu se nezdařilo, protože kontejner neexistuje.

- **Příčina**: nastane při pokusu vložit nesprávný typ primárního klíče v jímky databáze.

- **Řešení**: použijte odvozený sloupec k přetypování sloupce, který používáte pro primární klíč v toku dat tak, aby odpovídal datovému typu cílové databáze.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Chybová zpráva: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: připojení TCP/IP k hostitelskému portu xxxxx.database.windows.net 1433 se nezdařilo. Chyba: "xxxx.database.windows.net. Ověřte vlastnosti připojení. Ujistěte se, že je na hostiteli spuštěná instance SQL Server a přijímá připojení TCP/IP na portu. Ujistěte se, že brána firewall neblokuje připojení TCP k portu.

- **Příznaky**: nejde zobrazit náhled dat nebo spustit kanál se zdrojem nebo jímkou databáze.

- **Příčina**: databáze je chráněná branou firewall

- **Řešení**: otevřete bránu firewall pro přístup k databázi.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Chybová zpráva: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: v databázi se již nachází objekt s názvem "XXXXXX".

- **Příznaky**: jímka nemůže vytvořit tabulku.

- **Příčina**: v cílové databázi už existuje existující název tabulky se stejným názvem, který je definovaný ve vašem zdroji nebo v sadě dat.

- **Řešení**: Změna názvu tabulky, kterou se pokoušíte vytvořit

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Chybová zpráva: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: data String nebo Binary by byla zkrácena. 

- **Příznaky**: při zápisu dat do jímky SQL se datový tok při spuštění kanálu nezdařil s možnou chybou zkrácení.

- **Příčina**: pole z datového toku se mapuje do sloupce ve vaší databázi SQL Database není dostatečné pro uložení hodnoty, což způsobí, že ovladač SQL vyvolá tuto chybu.

- **Řešení**: můžete zkrátit délku dat pro sloupce řetězců pomocí ```left()``` v odvozeném sloupci nebo implementovat [vzor "chybový řádek".](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Chybová zpráva: protože Spark 2,3, dotazy z nezpracovaných souborů JSON/CSV jsou zakázané, pokud odkazované sloupce obsahují jenom interní poškozený sloupec záznamu. 

- **Příznaky**: čtení ze zdroje JSON se nepovedlo.

- **Příčina**: při čtení ze zdroje JSON s jedním dokumentem na mnoha vnořených řádcích, ADF, přes Spark, nemůže určit, kde začíná nový dokument a předchozí dokument končí.

- **Řešení**: ve zdrojové transformaci, která používá datovou sadu JSON, rozbalte "nastavení JSON" a zapněte "jednotlivý dokument".


## <a name="general-troubleshooting-guidance"></a>Obecné pokyny k odstraňování potíží

1. Ověřte stav připojení datové sady. V každé transformaci zdroje a jímky navštivte propojenou službu pro každou datovou sadu, kterou používáte, a otestujte připojení.
2. Ověřte stav připojení k souborům a tabulkám z návrháře toku dat. Přepněte na ladění a klikněte na náhled dat ve vašich zdrojových transformacích, abyste měli jistotu, že budete mít přístup k datům.
3. Pokud vše vypadá z verze Preview, přejdete do návrháře kanálů a zadáte tok dat do aktivity kanálu. Ladění kanálu pro ucelený test.

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog věnovaný Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce výkonem datových toků mapování ADF](concepts-data-flow-performance.md)
