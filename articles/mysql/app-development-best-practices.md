---
title: Osvědčené postupy pro vývoj aplikací – Azure Database for MySQL
description: Seznamte se s osvědčenými postupy pro vytváření aplikací pomocí Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611959"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Osvědčené postupy pro vytváření aplikací s využitím služby Azure Database for MySQL 

Tady jsou některé osvědčené postupy, které vám pomůžou vytvořit aplikaci připravenou pro cloud pomocí Azure Database for MySQL. Tyto osvědčené postupy mohou zkrátit dobu vývoje aplikace. 

## <a name="configuration-of-application-and-database-resources"></a>Konfigurace prostředků aplikace a databáze

### <a name="keep-the-application-and-database-in-the-same-region"></a>Zachovat aplikaci a databázi ve stejné oblasti
Když nasadíte aplikaci v Azure, ujistěte se, že jsou všechny závislosti ve stejné oblasti. Rozprostření instancí mezi oblasti nebo zóny dostupnosti vytváří latenci sítě, což může mít vliv na celkový výkon aplikace. 

### <a name="keep-your-mysql-server-secure"></a>Udržujte svůj server MySQL jako zabezpečený
Nakonfigurujte server MySQL tak, aby byl [zabezpečený](./concepts-security.md) a není veřejně přístupný. K zabezpečení serveru použijte jednu z těchto možností: 
- [Pravidla brány firewall](./concepts-firewall-rules.md)
- [Virtuální sítě](./concepts-data-access-and-security-vnet.md) 
- [Azure Private Link](./concepts-data-access-security-private-link.md)

Z důvodu zabezpečení se musíte ke svému serveru MySQL vždycky připojit přes SSL a nakonfigurovat server MySQL a aplikaci tak, aby používala TLS 1,2. Přečtěte si téma [Konfigurace protokolu SSL/TLS](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Vyladění parametrů serveru
Pro úlohy s vyladěním pro čtení a zvýšení `tmp_table_size` výkonu, které `max_heap_table_size` mohou optimalizovat. Chcete-li vypočítat hodnoty požadované pro tyto proměnné, podívejte se na celkový počet hodnot paměti pro připojení a základní paměť. Součet parametrů paměti pro připojení s výjimkou v `tmp_table_size` kombinaci se základními účty paměti pro celkovou paměť serveru.

Chcete-li vypočítat největší možnou velikost `tmp_table_size` a `max_heap_table_size` , použijte následující vzorec:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Celková paměť označuje celkovou velikost paměti, kterou server obsahuje v rámci zřízené virtuální jádra.  Například v Pro obecné účely vCore Azure Database for MySQL serveru bude celková velikost paměti 5 GB × 2. Další podrobnosti o paměti pro každou vrstvu najdete v dokumentaci k [cenové úrovni](./concepts-pricing-tiers.md) .
>
> Základní paměť označuje proměnné paměti, jako je `query_cache_size` a `innodb_buffer_pool_size` , které MySQL Inicializuje a přidělí při spuštění serveru. Paměť vázaná na připojení, například `sort_buffer_size` a `join_buffer_size` , je paměť, která je přidělena pouze v případě, že ji vyžaduje dotaz.

### <a name="create-non-admin-users"></a>Vytváření uživatelů bez rolí správce 
Vytvořte pro každou databázi [uživatele, kteří nejsou správci](./howto-create-users.md) . Obvykle jsou jména uživatelů identifikována jako názvy databází.

### <a name="reset-your-password"></a>Resetování hesla
Heslo pro server MySQL můžete [resetovat](./howto-create-manage-server-portal.md#update-admin-password) pomocí Azure Portal. 

Resetování hesla serveru pro produkční databázi může přinést aplikaci. Dobrým postupem je resetování hesla pro jakékoli provozní úlohy mimo špičku, aby se minimalizoval dopad na uživatele vaší aplikace.

## <a name="performance-and-resiliency"></a>Výkon a odolnost 
Tady je několik nástrojů a postupů, které můžete použít k ladění problémů s výkonem ve vaší aplikaci.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Povolit protokoly pomalých dotazů k identifikaci problémů s výkonem
Na serveru můžete povolit protokoly [pomalých dotazů](./concepts-server-logs.md) a [protokoly auditu](./concepts-audit-logs.md) . Analýza pomalých protokolů dotazů může přispět k identifikaci problémových míst výkonu při odstraňování potíží. 

Protokoly auditu jsou dostupné taky prostřednictvím protokolů Azure Diagnostics v protokolech Azure Monitor, Azure Event Hubs a účtů úložiště. Podívejte [se, jak řešit problémy s výkonem dotazů](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>Použití sdružování připojení
Správa připojení k databázi může mít významný dopad na výkon aplikace jako celek. Pokud chcete optimalizovat výkon, musíte snížit počet navázání připojení a čas pro navázání připojení v cestách kódu klíče. [Sdružování připojení](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) použijte pro připojení k Azure Database for MySQL ke zlepšení odolnosti a výkonu. 

K efektivní správě připojení můžete použít Pooler připojení [ProxySQL](https://proxysql.com/) . Použití připojení Pooler může snížit nečinné připojení a znovu použít existující připojení, což vám pomůže vyhnout se problémům. Další informace najdete v tématu [jak nastavit ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Logika opakování pro zpracování přechodných chyb
V aplikaci může docházet k [přechodným chybám](./concepts-connectivity.md#handling-transient-errors) , při kterých se připojení k databázi neúčtují nebo ztratí přerušovaně. V takových situacích je server v provozu po jednom až dvou opakovaných pokusech za 5 až 10 sekund. 

Dobrým postupem je počkat na 5 sekund před prvním opakováním. Pak postupujte podle každého opakování tak, že postupně rozrostete, až 60 sekund. Omezte maximální počet opakovaných pokusů, při kterých aplikace nebere v úvahu operaci, takže se můžete dále prozkoumat. Další informace najdete v tématu [řešení chyb připojení](./howto-troubleshoot-common-connection-issues.md) . 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Povolením replikace pro čtení zmírnit převzetí služeb při selhání
Pro scénáře převzetí služeb při selhání můžete použít [replikace vstupních dat](./howto-data-in-replication.md) . Pokud používáte repliky čtení, nedochází k automatizovanému převzetí služeb při selhání mezi zdrojovým serverem a serverem repliky. 

Všimnete si prodlevy mezi zdrojem a replikou, protože replikace je asynchronní. Prodleva sítě může být ovlivněná mnoha faktory, jako je velikost úlohy spuštěné na zdrojovém serveru a latence mezi datovými centry. Ve většině případů prodleva repliky vychází z několika sekund až do několika minut.

## <a name="database-deployment"></a>Nasazení databáze 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Konfigurace úlohy Azure Database for MySQL v kanálu nasazení CI/CD
V některých případech je nutné nasadit změny do vaší databáze. V takových případech můžete použít průběžnou integraci (CI) a průběžné doručování (CD) prostřednictvím [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) a pomocí úlohy pro [Server MySQL](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment) aktualizovat databázi spuštěním vlastního skriptu.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Použití efektivního procesu ručního nasazení databáze 
Při ručním nasazování databáze můžete pomocí těchto kroků minimalizovat prostoje nebo snížit riziko neúspěšného nasazení: 

1. Pomocí [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) nebo [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)vytvoříte kopii provozní databáze v nové databázi. 
2. Aktualizujte novou databázi pomocí nových změn schématu nebo aktualizací potřebných pro vaši databázi. 
3. Produkční databáze umístěte do stavu jen pro čtení. Dokud se nasazení nedokončí, neměli byste mít v provozní databázi žádné operace zápisu. 
4. Otestujte aplikaci pomocí nově aktualizované databáze z kroku 1.
5. Nasaďte změny aplikace a ujistěte se, že aplikace teď používá novou databázi s nejnovějšími aktualizacemi. 
6. Ponechte starou provozní databázi, abyste mohli změny vrátit zpět. Pak se můžete vyhodnotit tak, že odstraníte starou provozní databázi nebo ji v případě potřeby vyexportujete do Azure Storage. 

>[!NOTE]
>Pokud je aplikace jako aplikace pro elektronické obchodování a nemůžete ji vložit do stavu jen pro čtení, nasaďte změny přímo do provozní databáze po vytvoření zálohy. Změna následujících by se měla provádět v době mimo špičku s malým provozem do aplikace, aby se minimalizoval dopad, protože někteří uživatelé můžou narazit na neúspěšné požadavky. 
>
>Ujistěte se, že kód aplikace také zpracovává všechny neúspěšné požadavky.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Pomocí nativních metrik MySQL zjistíte, jestli vaše úlohy přesahují velikosti dočasných tabulek v paměti.
V případě úlohy pro čtení v paměti můžou dotazy běžící vůči serveru MySQL překročit velikosti dočasné tabulky v paměti. Úloha pro čtení v těžkých případech může způsobit, že váš server přepne na disk, který má za následek psaní dočasných tabulek, což má vliv na výkon aplikace. Pokud chcete zjistit, jestli váš server zapisuje na disk v důsledku překročení dočasné velikosti tabulky, podívejte se na následující metriky:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`Metrika indikuje, kolik tabulek bylo na disku vytvořeno. `created_tmp_table`Metrika obsahuje informace o tom, kolik dočasných tabulek je potřeba v paměti naformátovat za dané zatížení. Chcete-li určit, zda bude spuštění konkrétního dotazu používat dočasné tabulky, spusťte příkaz [vysvětlit](https://dev.mysql.com/doc/refman/8.0/en/explain.html) v dotazu. Podrobnosti ve `extra` sloupci označují, `Using temporary` zda se dotaz spustí pomocí dočasných tabulek.

Pokud chcete vypočítat procento vašich úloh pomocí dotazů na disky, použijte hodnoty metriky v následujícím vzorci:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

V ideálním případě by toto procento mělo být menší než 25%. Pokud vidíte, že procento je 25% nebo vyšší, doporučujeme, abyste upravili dva parametry serveru tmp_table_size a max_heap_table_size.

## <a name="database-schema-and-queries"></a>Schéma databáze a dotazy

Tady je několik tipů, které byste měli mít na paměti při sestavování schématu databáze a vašich dotazů.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Použití správného datového typu pro sloupce tabulky
Použití správného datového typu na základě typu dat, která chcete uložit, může optimalizovat úložiště a snížit počet chyb, které mohou nastat kvůli nesprávným datovým typům.

### <a name="use-indexes"></a>Použití indexů
Chcete-li se vyhnout pomalým dotazům, můžete použít indexy. Indexy umožňují rychle najít řádky s konkrétními sloupci. Podívejte [se, jak používat indexy v MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Vysvětlete pro vaše dotazy SELECT.
Pomocí `EXPLAIN` příkazu můžete získat přehled o tom, co MySQL dělá ke spuštění dotazu. Může vám to usnadnit detekci kritických bodů nebo problémů s vaším dotazem. Podívejte [se, jak používat vysvětlení k profilování výkonu dotazů](./howto-troubleshoot-query-performance.md).