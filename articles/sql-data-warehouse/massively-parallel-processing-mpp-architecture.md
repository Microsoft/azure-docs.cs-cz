---
title: Azure SQL Data Warehouse – architektura MPP | Microsoft Docs
description: Zjistěte, jak Azure SQL Data Warehouse kombinuje (MPP) massively parallel processing s Azure storage zajistit vysoký výkon a škálovatelnost.
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/11/2018
ms.author: acomet
ms.reviewer: mausher
ms.openlocfilehash: a0dad8afa87b3424c8561b2aaf44fbe0f5d5dae6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse - masivně paralelní zpracování architektura (MPP)
Zjistěte, jak Azure SQL Data Warehouse kombinuje (MPP) massively parallel processing s Azure storage zajistit vysoký výkon a škálovatelnost. 

## <a name="mpp-architecture-components"></a>Komponenty architektury MPP
SQL Data Warehouse využívá horizontální navýšení kapacity architektura distribuovat výpočetní zpracování dat mezi několika uzly. Jednotka škálování je abstrakcí výpočetní výkon, který se označuje jako jednotek datových skladů. SQL Data Warehouse odděluje výpočetní z úložiště, které umožňuje umožňuje škálovat výpočetní nezávisle na data ve vašem systému.

![Architektura služby SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse používá architekturu založenou na uzlu. Aplikace připojit a vydávat příkazy T-SQL řídicí uzel, který je jediný bod položky pro datový sklad. Řídicí uzel spustí MPP modul, který optimalizuje dotazy pro paralelní zpracování a pak předá operations výpočetních uzlů k práci a současně. Výpočetní uzly ukládání dat všech uživatelů ve službě Azure Storage a spouští paralelní dotazy. Služba přesun dat (DMS) je služba interní úrovni systému, která přesouvá data mezi uzly podle potřeby spouštět dotazy paralelně a vrátíte se přesné výsledky. 

Díky oddělenému úložišti a výpočetním prostředkům může SQL Data Warehouse:

* Velikost nezávisle výpočetního výkonu bez ohledu na požadavky na ukládání.
* Zvětšovat a zmenšovat výpočetní výkon bez přesouvání dat
* Pozastavit výpočetní kapacitu a nechat beze změn, data, takže platíte jenom pro úložiště.
* Obnovit výpočetní kapacitu za provozu

### <a name="azure-storage"></a>Úložiště Azure
SQL Data Warehouse používá úložiště Azure k zabezpečení dat uživatele.  Vzhledem k tomu, že vaše data uložena a spravuje úložiště Azure, SQL Data Warehouse samostatně poplatky za spotřeby vašeho úložiště. Samotná data je horizontálně dělené do **distribuce** za účelem optimalizace výkonu systému. Můžete zvolit, které vzor horizontálního dělení slouží k distribuci dat, když definujete v tabulce. SQL Data Warehouse podporuje tyto vzory horizontálního dělení:

* Hodnota hash
* Kruhové dotazování.
* Replikace

### <a name="control-node"></a>Řídicí uzel

Řídicí uzel je mozku datového skladu. Jde o prvek front-end, který komunikuje se všemi aplikacemi a připojeními. Modul MPP běží na uzlu řízení optimalizace a koordinovat paralelní dotazy. Při odesílání dotazu T-SQL do SQL Data Warehouse řídicí uzel ho transformuje na dotazy, které spouštění každý distribuční paralelně.

### <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. Distribuce mapování na výpočetní uzly pro zpracování. Jako platíte víc zdrojích výpočtů, SQL Data Warehouse znovu mapuje distribuce na dostupné výpočetní uzly. Počet výpočetní uzly v rozmezí od 1 do 60 a je určen podle úrovně služby pro datový sklad.

Každý výpočetní uzel má ID uzlu, který je viditelný v zobrazeních systému. Zobrazí se ID výpočetního uzlu tak, že vyhledá node_id sloupec v systémových zobrazeních, jejichž názvy začínají řetězcem sys.pdw_nodes. Seznam těchto systémových zobrazení najdete v tématu [MPP systémová zobrazení](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Služba pro přesun dat
Služby přesun dat (DMS) je technologie přenosu dat, která koordinuje přesun dat mezi výpočetní uzly. Některé dotazy vyžadují přesun dat zajistit paralelní dotazy přesné výsledky. Při přesunu dat je potřeba, DMS zajistí, že přejdete do správného umístění správná data. 

## <a name="distributions"></a>Distribuce

Distribuce je základní jednotkou úložiště a zpracování paralelní dotazy kterých běží na distribuovaných datech. Při spuštění dotazu SQL Data Warehouse práce je rozdělena na 60 menší dotazy, které spustit souběžně. Každý dotaz na 60 menší spouští v jednom z distribuce data. Každý výpočetní uzel spravuje jeden nebo více 60 distribuce. Datový sklad s maximální výpočetní prostředky má jeden distribuční na výpočetním uzlu. Datový sklad s minimální výpočetní prostředky má všechny distribuce na jednu výpočetním uzlu.  

## <a name="hash-distributed-tables"></a>Distribuovat algoritmu hash tabulky
Zatřiďovací tabulku distribuované může poskytnout nejvyšší výkon dotazů pro spojování a agregaci pro velké tabulky. 

Sdílení dat do tabulky distribuovat algoritmu hash SQL Data Warehouse používá funkce hash nepodmíněně přiřadit k jednomu distribučnímu každý řádek. V definici tabulky jednoho ze sloupců je určený jako distribuční sloupce. Funkce hash používá hodnoty ve sloupci distribuční přiřadit distribuční každý řádek.

Následující diagram znázorňuje, jak získá úplnou (tabulka distribuované) uložené jako tabulku distribuovat algoritmu hash. 

![Tabulka distribuované](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuované tabulky")  

* Každý řádek patří do jednoho distribučního.  
* Algoritmus hash deterministický přiřadí do jednoho distribučního každý řádek.  
* Počet řádků tabulky na distribuční se liší, jak je znázorněno pomocí různých velikostí tabulek.

Existují důležité informace o výkonu pro výběr distribuční sloupce, například odlišnosti, zkosení dat a typy dotazů, které běží na systému.

## <a name="round-robin-distributed-tables"></a>Distribuované tabulky pomocí kruhového dotazování
Tabulka kruhového dotazování je nejjednodušší tabulku, kterou chcete vytvořit a nabízí vysoký výkon, když se použije jako pracovní tabulky, pro zatížení.

Distribuované tabulku kruhového dotazování rovnoměrně distribuuje data v tabulce, ale bez jakékoli další optimalizace. Distribuce nejprve vybrán náhodně a pak vyrovnávací paměti řádků jsou přiřazeny k distribuce postupně. Je rychlý načíst data do tabulky pomocí kruhového dotazování, ale výkonu dotazu může být často lepší s tabulkami distribuovat algoritmu hash. Spojení na kruhového dotazování tabulky vyžadují promísení dat a to bude vyžadovat čas navíc.


## <a name="replicated-tables"></a>Replikované tabulky
Replikované tabulky poskytuje nejrychlejší výkon dotazů pro malé tabulky.

Tabulka, která se replikují ukládá do mezipaměti úplnou kopii v tabulce na každém výpočetním uzlu. V důsledku toho replikace tabulku eliminuje nutnost k přenosu dat mezi výpočetní uzly před spojení nebo agregace. Replikované tabulky jsou využité nejlépe s malé tabulky. Dodatečné úložiště se vyžaduje a neexistují další režie, které je při zápisu dat, které činit velké tabulky nepraktické.  

Následující diagram znázorňuje replikované tabulky. Pro SQL Data Warehouse je replikované tabulky do mezipaměti, první distribuce na každém výpočetním uzlu.  

![Replikované tabulky](media/sql-data-warehouse-distributed-data/replicated-table.png "replikované tabulky") 

## <a name="next-steps"></a>Další postup
Teď, když jste se s SQL Data Warehouse seznámili, můžete zjistit, jak rychle [vytvořit datový sklad SQL Data Warehouse][create a SQL Data Warehouse] a [načíst ukázková data][load sample data]. Pokud s Azure začínáte, může vám být užitečný [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Vytvoření lístku podpory]
* [Fórum MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Vytvoření lístku podpory]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
