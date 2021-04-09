---
title: Provozní osvědčené postupy pro server MySQL – Azure Database for MySQL
description: Tento článek popisuje osvědčené postupy pro provoz databáze MySQL v Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96355033"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Osvědčené postupy pro operace serveru na Azure Database for MySQL – jeden server

Seznamte se s osvědčenými postupy pro práci s Azure Database for MySQL. Jakmile na platformu přidáváme nové možnosti, budeme se i nadále soustředit na postup, který je podrobně popsaný v této části.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL provozní pokyny 

Níže jsou uvedené provozní pokyny, které by měly být dodrženy při práci s Azure Database for MySQL pro zlepšení výkonu databáze: 

* **Společné umístění**: aby se snížila latence sítě, umístěte klienta a databázový server do stejné oblasti Azure.

* **Monitorujte využití paměti, procesoru a úložiště**: můžete [nastavit upozornění](howto-alert-on-metric.md) , která vás upozorní, když se změní vzorce používání nebo když se přiblížíte ke kapacitě nasazení, abyste mohli udržovat výkon systému a dostupnost. 

* **Horizontální navýšení kapacity databáze**: při přístupu k limitům kapacity úložiště můžete [škálovat](howto-create-manage-server-portal.md) . V úložišti a v paměti byste měli mít nějakou vyrovnávací paměť, která bude vyhovovat nepředvídatelnému nárůstu zatížení vašich aplikací. Můžete také [Povolit funkci autogrow úložiště](howto-auto-grow-storage-portal.md) zapnuto, aby se zajistilo, že služba automaticky škáluje úložiště v blízkosti limitu úložiště. 

* **Konfigurace zálohování**: Povolte [místní nebo geograficky redundantní zálohy](howto-restore-server-portal.md#set-backup-configuration) na základě požadavku firmy. Navíc upravíte dobu uchovávání dat, jak dlouho budou zálohy k dispozici pro provozní kontinuitu. 

* **Zvyšte kapacitu vstupu a výstupu**: Pokud vaše databázová úloha vyžaduje více vstupně-výstupních operací, než jste zřídili, obnovení nebo jiné transakční operace pro vaši databázi budou pomalé. Chcete-li zvýšit kapacitu vstupně-výstupních operací instance serveru, proveďte některou z následujících akcí: 

    * Azure Database for MySQL poskytuje škálování IOPS na základě sazby 3 IOPS za GB zřízené úložiště. [Zvětšete zřízené úložiště](howto-create-manage-server-portal.md#scale-storage-up) pro škálování IOPS pro lepší výkon. 

    * Pokud už úložiště IOPS používáte zřízené, zajistěte [Další kapacitu propustnosti](howto-create-manage-server-portal.md#scale-storage-up). 

* **Škálování výpočetních** prostředků: databázové úlohy můžou být taky omezené kvůli procesoru nebo paměti a to může mít vážný dopad na zpracování transakcí. Počítejte s tím, že výpočetní výkon (cenová úroveň) je možné škálovat nahoru nebo dolů mezi [pro obecné účely nebo paměťově optimalizovanými](concepts-pricing-tiers.md) úrovněmi. 

* **Testování převzetí služeb při selhání**: ruční testování převzetí služeb při selhání pro instanci serveru vám pomůže pochopit, jak dlouho proces trvá pro váš případ použití, a zajistit, aby se aplikace, která přistupuje k vaší instanci serveru, mohla po převzetí služeb při selhání automaticky připojit k nové instanci serveru.

* **Použít primární klíč**: Ujistěte se, že vaše tabulky mají při práci na Azure Database for MySQL primární nebo jedinečný klíč. Pomůže vám to při zálohování, replice atd. a zvyšuje výkon.

* **Konfigurace hodnoty TTL**: Pokud vaše klientská aplikace ukládá do mezipaměti data služby DNS (Domain Name Service) vašich instancí serveru, nastavte hodnotu TTL (Time-to-Live), která je kratší než 30 sekund. Vzhledem k tomu, že se podkladová IP adresa instance serveru může po převzetí služeb při selhání měnit, ukládání dat DNS do mezipaměti po delší dobu může způsobit selhání připojení, pokud se aplikace pokusí připojit k IP adrese, která už není v provozu.

* Použijte sdružování připojení, abyste se vyhnuli [dosažení maximálního počtu omezení připojení](concepts-server-parameters.md#max_connections)a pomocí logiky opakování nedocházelo k problémům s přerušovaným připojením. 

* Pokud používáte repliku, použijte [ProxySQL k vyrovnávání zatížení](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) mezi primárním serverem a čitelným serverem sekundární repliky. Pokyny k instalaci najdete tady. </br> 

* Při zřizování prostředku se ujistěte, že jste [povolili autogrow](howto-auto-grow-storage-portal.md) pro váš Azure Database for MySQL. Nepřidá se žádné další náklady a aplikace bude chránit databázi ze všech kritických bodů úložiště, ke kterým byste mohli běžet. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Použití InnoDB s Azure Database for MySQL

*   Pokud používáte `ibdata1` funkci, která je datovým souborem tabulkového tabulky, nelze zmenšit nebo odstranit vyřazením dat z tabulky nebo přesunutím tabulky do souboru na tabulku `tablespaces` .

* V případě databáze o velikosti větší než 1 TB byste měli vytvořit tabulku v **innodb_file_per_table** `tablespace` . Pro jednu tabulku, která je větší než 1 TB velikosti, byste měli mít tabulku [oddílů](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) .

*   U serveru, který má velký počet `tablespace` , bude spuštění modulu velmi pomalé kvůli kontrole sekvenčního tabulkového prostoru během spouštění nebo převzetí služeb při selhání MySQL. 

* Pokud je celková hodnota tabulky menší než 500, nastavte innodb_file_per_table = ON.

* Pokud máte v databázi více než 500 tabulek, zkontrolujte velikost tabulky pro každou jednotlivou tabulku. Pro velkou tabulku byste měli zvážit použití tabulkového prostoru souborů pro tabulky, abyste se vyhnuli maximálnímu limitu úložiště souborů systémového tabulkového úložiště.

> [!NOTE]
> Pro tabulky s velikostí menší než 5 GB zvažte použití systémového tabulkového prostoru. 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* Vytvořte [oddíly](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) tabulky při vytváření tabulky, pokud máte velmi velkou tabulku, která může být větší než 1 TB.

* Použijte více serverů MySQL a rozprostřete tabulky na tyto servery. Vyhněte se vložení příliš velkého počtu tabulek na jeden server, pokud máte kolem tabulky 10000 nebo více. 

## <a name="next-steps"></a>Další kroky
- [Osvědčený postup pro výkon Azure Database for MySQL](concept-performance-best-practices.md)
- [Osvědčené postupy monitorování Azure Database for MySQL](concept-monitoring-best-practices.md)
