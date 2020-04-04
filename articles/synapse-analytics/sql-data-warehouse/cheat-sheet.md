---
title: Tahák pro Azure Synapse Analytics (dříve SQL DW)
description: Najděte odkazy a doporučené postupy pro rychlé sestavení řešení Azure Synapse Analytics (dříve SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 55b00af9afeafb2a3fa7992cc457819dc1dcb2b2
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631289"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Tahák pro Azure Synapse Analytics (dříve SQL DW)

Tento tahák obsahuje užitečné tipy a osvědčené postupy pro vytváření řešení Azure Synapse.

Následující obrázek znázorňuje proces návrhu datového skladu:

![Nákres](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Dotazy a operace napříč tabulkami

Pokud předem znáte hlavní operace a dotazy, které se ve vašem datovém skladu budou spouštět, můžete nastavit priority architektury datového skladu pro tyto operace. Mezi tyto dotazy a operace můžou patřit:

* Propojení jedné nebo dvou tabulek faktů s tabulkami dimenzí, filtrování zkombinované tabulky a následné připojení výsledků do datového tržiště.
* Provádění velkých nebo malých aktualizací tabulek faktů.
* Připojení pouze dat k tabulkám.

Znalost typů operací předem vám pomůže optimalizovat návrh tabulek.

## <a name="data-migration"></a>Migrace dat

Nejdřív načtěte data do [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nebo Azure Blob Storage. Dále použijte PolyBase k načtení dat do pracovních tabulek. Použijte následující konfiguraci:

| Návrh | Doporučení |
|:--- |:--- |
| Distribuce | Kruhové dotazování. |
| Indexování | Halda |
| Dělení | Žádný |
| Třída prostředku | largerc nebo xlargerc |

Další informace o [migraci dat](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), [načítání dat](design-elt-data-loading.md) a [procesu extrakce, načítání a transformace (ELT)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Distribuované nebo replikované tabulky

Použijte následující strategie v závislosti na vlastnostech tabulek:

| Typ | Skvěle se hodí pro...| Na co si dát pozor|
|:--- |:--- |:--- |
| Replikované | * Malé rozměrové tabulky ve schématu hvězdy s méně než 2 GB úložiště po kompresi (~ 5x komprese) |* Mnoho transakcí zápisu jsou na stole (například vložit, upsert, odstranit, aktualizovat)<br></br>* Často měníte zřizování jednotek datového skladu (DWU)<br></br>* Používáte pouze 2-3 sloupce, ale vaše tabulka má mnoho sloupců<br></br>* Indexovat replikovanou tabulku |
| Kruhové dotazování (výchozí) | * Dočasná/pracovní tabulka<br></br> * Žádné zřejmé spojující klíč nebo dobrý kandidát sloupec |* Výkon je pomalý kvůli pohybu dat |
| Hodnota hash | * Tabulky faktů<br></br>* Velké rozměrové tabulky |* Distribuční klíč nelze aktualizovat |

**Tipy:**

* Začněte strategií kruhového dotazování, ale usilujte o strategii distribuce hodnot hash, abyste mohli využít výkonnou paralelní architekturu.
* Ujistěte se, že společné klíče hash mají stejný formát dat.
* Nedistribuujte ve formátu varchar.
* U tabulek dimenzí se společným klíčem hash jako tabulka faktů s častými operacemi spojení je možné provádět distribuci hodnot hash.
* Pomocí *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)* můžete analyzovat případné zkreslení dat.
* Pomocí *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)* můžete analyzovat přesuny dat vyvolané dotazy, monitorovat všesměrové vysílání času a prohazování prováděné operacemi. To je užitečné při kontrole distribuční strategie.

Další informace o [replikovaných tabulkách](design-guidance-for-replicated-tables.md) a [distribuovaných tabulkách](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indexování tabulky

Indexování je užitečné pro rychlé čtení tabulek. Existuje jedinečná sada technologií, které můžete použít podle svých potřeb:

| Typ | Skvěle se hodí pro... | Na co si dát pozor|
|:--- |:--- |:--- |
| Halda | * Pracovní /dočasná tabulka<br></br>* Malé stoly s malými vyhledáváními |* Jakékoliv vyhledávání skenuje celý stůl |
| Clusterovaný index | * Tabulky s až 100 miliony řádků<br></br>* Velké tabulky (více než 100 milionů řádků) s pouze 1-2 sloupci silně používané |* Používá se na replikované tabulce<br></br>* Máte složité dotazy zahrnující více připojit a seskupit podle operací<br></br>* Uděláte aktualizace na indexované sloupce: to trvá paměť |
| Clusterovaný index columnstore (CCI) (výchozí) | * Velké tabulky (více než 100 milionů řádků) | * Používá se na replikované tabulce<br></br>* Uděláte masivní aktualizace operací na vašem stole<br></br>* Přerozděleny tabulka: skupiny řádků nejsou rozloženy napříč různými distribučními uzly a oddíly |

**Tipy:**

* Kromě clusterovaného indexu můžete chtít přidat neclusterovaný index pro sloupec, který se často používá k filtrování.
* Při správě paměti v tabulce s CCI buďte opatrní. Při načítání dat chcete, aby uživatel (nebo dotaz) využíval výhod velké třídy prostředků. Nezapomeňte se vyhnout ořezávání a vytváření mnoha malých komprimovaných skupin řádků.
* V Gen2 se tabulky CCI ukládají do mezipaměti místně do výpočetních uzlů za účelem maximalizace výkonu.
* U CCI může docházet ke snížení výkonu kvůli špatné kompresi skupin řádků. Pokud k tomu dojde, znovu svůj CCI sestavte nebo ho reorganizujte. Každá komprimovaná skupina řádků by měla obsahovat alespoň 100 000 řádků. Ideálně by skupina řádků měla obsahovat 1 milion řádků.
* Reorganizaci nebo opětovné sestavení indexů byste měli automatizovat na základě frekvence a velikosti přírůstkového načítání. Jarní úklid je vždy užitečný.
* Pokud chcete oříznout skupinu řádků, myslete strategicky. Jak velké jsou otevřené skupiny řádků? Kolik dat očekáváte, že se bude v nadcházejících dnech načítat?

Další informace o [indexech](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Dělení

Pokud máte velkou tabulku faktů (více než 1 miliarda řádků), můžete ji rozdělit do oddílů. V 99 % případů by klíč oddílu měl být založený na datu. Buďte opatrní, abyste nevytvořili příliš mnoho oddílů, zejména pokud máte clusterovaný index columnstore.

Výhody dělení můžete využívat u pracovních tabulek, které vyžadují ELT. Usnadňuje to správu životního cyklu dat.
Buďte opatrní, abyste nevytvořili příliš mnoho oddílů dat, zejména u clusterovaného indexu columnstore.

Přečtěte si další informace o [oddílech](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Přírůstkové načítání

Pokud se chystáte přírůstkově načítat data, nejprve se ujistěte, že pro načítání dat přidělujete větší třídy prostředků.  To je zvláště důležité při načítání do tabulek s clusterovanými indexy columnstore.  Další podrobnosti naleznete ve [třídách prostředků.](resource-classes-for-workload-management.md)  

Doporučujeme používat PolyBase a ADF V2 pro automatizaci elt kanálů do datového skladu.

Pro velkou dávku aktualizací v historických datech zvažte použití [CTAS](sql-data-warehouse-develop-ctas.md) k zápisu dat, která chcete zachovat v tabulce, nikoli pomocí funkce INSERT, UPDATE a DELETE.

## <a name="maintain-statistics"></a>Udržujte statistiky

 Dokud nejsou automatické statistiky obecně dostupné, je vyžadována ruční údržba statistik. Statistiky je důležité aktualizovat, když dojde k *významným* změnám vašich dat. Pomáhá to optimalizovat plány dotazů. Pokud zjistíte, že údržba vašich statistik trvá příliš dlouho, pečlivěji zvažte, které sloupce mají statistiku mít.

Můžete také definovat frekvenci aktualizací. Například můžete chtít každý den aktualizovat sloupce s datem, do kterých se můžou přidávat nové hodnoty. Nejvíce výhod získáte tak, že budete mít statistiky pro sloupce používané ve spojeních, sloupce používané v klauzuli WHERE a sloupce používané v příkazu GROUP BY.

Další informace o [statistikách](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Třída prostředků

Skupiny prostředků se používají jako způsob přidělení paměti dotazům. Pokud ke zrychlení dotazů nebo načítání potřebujete více paměti, měli byste přidělit vyšší třídy prostředků. Na druhou stranu, použití větších tříd prostředků má vliv na souběžnost. Na to byste měli brát ohled před přesunem všech vašich uživatelů do větší třídy prostředků.

Pokud si všimnete, že dotazy trvají příliš dlouho, zkontrolujte, jestli vaši uživatelé nepoužívají velké třídy prostředků. Velké třídy prostředků využívají velké množství slotů souběžnosti. Můžou způsobit hromadění dalších dotazů ve frontě.

Nakonec pomocí Gen2 [fondu SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse), každá třída prostředků získá 2,5 krát více paměti než Gen1.

Další informace o práci s [třídami prostředků a souběžností](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Snížení nákladů

Klíčovou vlastností Azure Synapse je schopnost [spravovat výpočetní prostředky](sql-data-warehouse-manage-compute-overview.md). Fond SQL můžete pozastavit, když ho nepoužíváte, což zastaví fakturaci výpočetních prostředků. Prostředky můžete škálovat s ohledem na své požadavky na výkon. K pozastavení můžete použít [Azure Portal](pause-and-resume-compute-portal.md) nebo [PowerShell](pause-and-resume-compute-powershell.md). Ke škálování můžete použít [Azure Portal](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), jazyk [T-SQL](quickstart-scale-compute-tsql.md) nebo rozhraní [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Automatické škálování teď můžete provádět kdykoli díky Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimalizace výkonu architektury

Služby SQL Database a Azure Analysis Services doporučujeme zvážit v hvězdicovité architektuře. Toto řešení může zajistit izolaci úloh mezi různými skupinami uživatelů a zároveň využívat pokročilé funkce zabezpečení ve službách SQL Database a Azure Analysis Services. Tímto způsobem můžete uživatelům poskytnout neomezenou souběžnost.

Přečtěte si další informace o [typických architekturách, které využívají azure synapse](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Nasaďte své paprsky jedním kliknutím v databázích SQL z fondu SQL:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>
