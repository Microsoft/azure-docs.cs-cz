---
title: Osvědčené postupy pro vývoj aplikací – Azure Database for MySQL
description: Seznamte se s osvědčenými postupy při sestavování aplikace pomocí Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259246"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Osvědčené postupy pro sestavování aplikací pomocí Azure Database for MySQL 

Tady jsou některé osvědčené postupy, které vám pomohou při vytváření aplikací připravených pro cloud pomocí Azure Database for MySQL, které mohou zkrátit dobu vývoje aplikace. 

## <a name="application-and-database-resource-configuration"></a>Konfigurace prostředků aplikace a databáze

### <a name="application-and-database-in-the-same-region"></a>Aplikace a databáze ve stejné oblasti
Když nasadíte aplikaci v Azure, ujistěte se, že **jsou všechny závislosti ve stejné oblasti**  . Rozprostření instancí mezi oblasti nebo zóny dostupnosti vytváří latenci sítě, což může mít vliv na celkový výkon vaší aplikace. 

### <a name="keep-your-mysql-server-secure"></a>Udržujte svůj server MySQL jako zabezpečený
Server MySQL by měl být nakonfigurovaný tak, aby byl [zabezpečený](https://docs.microsoft.com/azure/mysql/concepts-security) a není veřejně přístupný. K zabezpečení serveru použijte některou z těchto možností: 
- [Pravidla brány firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) nebo
- [Virtuální sítě](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) nebo 
- [Privátní propojení](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

Z důvodu zabezpečení se musíte ke svému serveru MySQL připojit přes SSL a nakonfigurovat server MySQL a aplikaci tak, aby používala **protokol** **TLS 1.2**. Přečtěte si téma [Konfigurace protokolu SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Vyladění parametrů serveru
Pro úlohy pro čtení s vysokým zatížením vyladění těchto parametrů tmp_table_size a max_heap_table_size můžou přispět k optimalizaci pro lepší výkon. Pokud chcete vypočítat hodnoty požadované pro tmp_table_size a max_heap_table_size, podívejte se na celkový počet hodnot paměti pro připojení a základní paměť. Součet parametrů paměti pro připojení, kromě tmp_table_size, v kombinaci se základními účty paměti pro celkovou paměť serveru.

Chcete-li vypočítat největší možnou velikost tmp_table_size a max_heap_table_size, použijte následující vzorec:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Celková paměť označuje celkovou velikost paměti, kterou server obsahuje v rámci virtuální jádra zřízené.  Například na serveru Pro obecné účely 2 vCore Azure Database for MySQL bude celková paměť 5 GB * 2.  Další podrobnosti o paměti pro každou vrstvu najdete v dokumentaci k [cenové úrovni](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) .
> Základní paměť označuje proměnné paměti, jako query_cache_size a innodb_buffer_pool_size, které MySQL Inicializuje a přidělí při spuštění serveru.  Paměť pro připojení, jako je například sort_buffer_size a join_buffer_size, je paměť, která je přidělena pouze v případě, že ji vyžaduje dotaz.

### <a name="create-a-non-admin-user"></a>Vytvoření uživatele bez role správce 
Pro každou databázi [Vytvořte uživatele, kteří nejsou správci](https://docs.microsoft.com/azure/mysql/howto-create-users) . Obvykle jsou jména uživatelů identifikována jako názvy databází.

### <a name="resetting-your-password"></a>Resetování hesla
Heslo pro server MySQL můžete [resetovat](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) pomocí Azure Portal. 

Resetování hesla serveru pro produkční databázi může přinést aplikaci. Je dobrým vzorem resetování hesla pro jakékoli provozní úlohy mimo špičku, aby se minimalizoval dopad na koncové uživatele aplikace.

## <a name="performance-and-resiliency"></a>Výkon a odolnost 
Tady je několik nástrojů a vzorů, které můžete použít k ladění problémů s výkonem ve vaší aplikaci.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Povolit protokoly pomalých dotazů identifikovat problémy s výkonem
Na serveru můžete povolit protokoly [pomalých dotazů](https://docs.microsoft.com/azure/mysql/concepts-server-logs) a [protokoly auditu](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) . Analýza pomalých protokolů dotazů může přispět k identifikaci problémových míst výkonu při odstraňování potíží. 

Protokoly auditu jsou dostupné taky prostřednictvím diagnostických protokolů Azure v Azure Monitor protokoly, Event Hubs a účet úložiště. Podívejte [se, jak řešit problémy s výkonem dotazů](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Použití sdružování připojení
Správa připojení k databázi může mít významný dopad na výkon aplikace jako celek. Pro optimalizaci výkonu musíte snížit počet navázání připojení a čas pro vytvoření připojení v cestách kódu klíče.  [Sdružování připojení](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) použijte pro připojení k Azure Database for MySQL ke zlepšení odolnosti a výkonu. 

[ProxySQL](https://proxysql.com/), což je Pooler připojení, se dá efektivně použít ke správě připojení. Použití připojení Pooler může snížit nečinné připojení a opakované použití existujících připojení pomůže vyhnout se problémům. Další informace najdete v tématu [jak nastavit ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Logika opakování pro zpracování přechodných chyb
Vaše aplikace by mohla docházet k [přechodným chybám](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) , při kterých se připojení k databázi neúčtují nebo ztrácejí přerušovaně. V takových situacích je server v provozu po jednom až dvou opakovaných pokusech během 5-10 sekund. 

Dobrým způsobem, jak se bude opakovat, je počkat 5 sekund před prvním opakováním a pak postupovat podle každého opakování, a to tak, že se počká postupně až 60 sekund. Omezte maximální počet opakovaných pokusů, při kterých aplikace nebere v úvahu operaci, takže se můžete dále prozkoumat. Další informace najdete v tématu [řešení chyb připojení](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) . 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Povolením replikace pro čtení zmírnit převzetí služeb při selhání
[Replikaci dat](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) můžete použít pro scénáře převzetí služeb při selhání. Při použití replik pro čtení nedochází k automatizovanému převzetí služeb při selhání mezi hlavními servery a servery repliky. Vzhledem k tomu, že replikace je asynchronní, si všimněte prodlevy mezi hlavním serverem a replikou. Prodleva sítě může být ovlivněná mnoha faktory, jako je například to, jak velké zatížení na hlavním serveru běží a latence mezi datovými centry. Ve většině případů se prodlevy replikují mezi několik sekund až na několik minut.

## <a name="database-deployment"></a>Nasazení databáze 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Konfigurace úlohy Azure Database for MySQL v kanálu nasazení CI/CD
Občas byste museli nasazovat změny do vaší databáze. V takových případech můžete vytvořit možnost použít průběžnou integraci (CI) a průběžné doručování (CD) prostřednictvím [kanálů Azure](https://azure.microsoft.com/services/devops/pipelines/) a pomocí úlohy pro [Server MySQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) aktualizovat databázi spuštěním vlastního skriptu pro vaši databázi.

### <a name="manual-database-deployment"></a>Ruční nasazení databáze 
Při ručním nasazování databáze je dobrým vzorem, jak minimalizovat prostoje nebo snížit riziko neúspěšného nasazení: 

1. Vytvoření kopie provozní databáze v nové databázi pomocí [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) nebo [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 
2. Aktualizujte novou databázi pomocí nových změn schématu nebo aktualizací potřebných pro vaši databázi. 
3. Provozní databáze umístěte do stavu jen pro čtení. Dokud se nasazení nedokončí, neměli byste mít v provozní databázi žádné operace zápisu. 
4. Otestujte aplikaci pomocí nově aktualizované databáze z kroku 1.
5. Nasaďte změny aplikace a ujistěte se, že aplikace teď používá novou databázi s nejnovějšími aktualizacemi. 
6. Ponechte starou provozní databázi, abyste mohli změny vrátit zpět. Pak se můžete vyhodnotit tak, že odstraníte starou provozní databázi nebo ji v případě potřeby vyexportujete do služby Azure Storage. 

>[!NOTE]
>  - Pokud je aplikace jako aplikace elektronického obchodování, kde ji pravděpodobně nebudete moci vložit do stavu jen pro čtení, pak tyto změny nasaďte přímo do provozní databáze po provedení zálohy.  Změna následujících by se měla objevit v době mimo špičku s malým provozem do aplikace, aby minimze dopad na to, že někteří uživatelé můžou zaznamenat neúspěšné požadavky. 
>  - Ujistěte se, že kód aplikace také zpracovává všechny neúspěšné požadavky.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Pomocí nativních metrik MySQL zjistíte, jestli vaše úlohy přesahují velikosti dočasných tabulek v paměti.
V případě úlohy pro čtení v paměti můžou dotazy prováděné vůči serveru MySQL překročit velikosti dočasných tabulek v paměti. Může dojít k tomu, že váš server přepne na zápis dočasných tabulek na disk, což má vliv na výkon aplikace. Pokud chcete zjistit, jestli váš server zapisuje na disk v důsledku překročení dočasné velikosti tabulky, podívejte se na následující metriky:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
Metrika created_tmp_disk_tables označuje, kolik tabulek bylo na disku vytvořeno, zatímco metrika created_tmp_table oznamuje, kolik dočasných tabulek je nutné vytvořit v paměti dané úlohou. Chcete-li určit, zda bude spuštění konkrétního dotazu používat dočasné tabulky, spusťte příkaz vysvětlit u dotazu. Pokud se dotaz spustí s dočasnými tabulkami, podrobná hodnota ve sloupci "extra" indikuje možnost "použití dočasného".

Pokud chcete vypočítat procento vašich úloh pomocí dotazů na disky, použijte hodnoty metrik ve vzorci níže:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

V ideálním případě by toto procento mělo být menší než 25%. Pokud vidíte, že procento je 25% nebo vyšší, doporučujeme, abyste upravili dva parametry serveru tmp_table_size a max_heap_table_si


## <a name="database-schema-and-queries"></a>Schéma databáze a dotazy

Tady je pár tipů a štychů, které je potřeba vzít v úvahu při sestavování schématu databáze a dotazů.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Vždy použít správný datový typ pro sloupce tabulky
Použití správných datových typů na základě typu dat, která chcete uložit, může optimalizovat úložiště a snížit všechny chyby, ke kterým může dojít z důvodu nesprávných datových typů.

### <a name="use-indexes"></a>Použití indexů
Chcete-li se vyhnout pomalým dotazům, můžete použít indexy. Indexy umožňují rychle najít řádky s konkrétními sloupci. Podívejte [se, jak používat indexy v MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>VYSVĚTLENÍ dotazů SELECT
Pomocí [vysvětlení](https://dev.mysql.com/doc/refman/8.0/en/explain.html) můžete získat přehled o tom, co MySQL dělá ke spuštění dotazu. To vám může pomáhat odhalit slabá místa nebo problémy s vaším dotazem. Podívejte [se, jak používat vysvětlení k profilování výkonu dotazů](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


