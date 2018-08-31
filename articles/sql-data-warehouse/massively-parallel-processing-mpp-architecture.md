---
title: Azure SQL Data Warehouse – architektura MPP | Dokumentace Microsoftu
description: Zjistěte, jak Azure SQL Data Warehouse kombinuje paralelního zpracování (MPP) s Azure storage k dosažení vysokého výkonu a škálovatelnosti.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 34b908ef79b0a2479c420675272f7d3f3bf0ff15
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286788"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse – výkonné paralelní zpracování (MPP) architektury
Zjistěte, jak Azure SQL Data Warehouse kombinuje paralelního zpracování (MPP) s Azure storage k dosažení vysokého výkonu a škálovatelnosti. 

## <a name="mpp-architecture-components"></a>Komponenty architektury MPP
SQL Data Warehouse využívá s horizontálním navýšením kapacity architektura distribuovat výpočetní zpracování dat napříč více uzly. Jednotka škálování je abstrakcí výpočetní výkon, který se označuje jako jednotka datového skladu. SQL Data Warehouse odděluje výpočetní ze služby storage, který umožňuje škálovat výpočetní nezávisle na data ve vašem systému.

![Architektura služby SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse používá architekturu založenou na uzlu. Aplikace připojit a vydávat příkazy T-SQL, řídicí uzel, který je jediný bod vstupu pro datový sklad. Řídicí uzel spustí MPP modul, který optimalizuje dotazy pro paralelní zpracování a operace předá výpočetní uzly, aby mohly pracovat paralelně. Výpočetní uzly ukládání dat všech uživatelů ve službě Azure Storage a spouštění paralelních dotazů. Služba pohyb dat (DMS) je interní služby na úrovni systému, který přesouvá data mezi uzly podle potřeby ke spouštění dotazů paralelně a vrátit přesné výsledky. 

Díky oddělenému úložišti a výpočetním prostředkům může SQL Data Warehouse:

* Velikost nezávisle na sobě výpočetní výkon bez ohledu na vaše potřeby při ukládání.
* Zvětšovat a zmenšovat výpočetní výkon bez přesouvání dat
* Pozastavit výpočetní kapacitu a ponechání dat beze změny, takže platíte jenom za úložiště.
* Obnovit výpočetní kapacitu za provozu

### <a name="azure-storage"></a>Úložiště Azure
SQL Data Warehouse využívá úložiště Azure k bezpečnost vašich dat uživatele.  Vzhledem k tomu, že se vaše data ukládají a spravovaných službou Azure storage, SQL Data Warehouse samostatně poplatky za spotřebu úložiště. Vlastní data se horizontálně dělené do **distribuce** za účelem optimalizace výkonu v systému. Můžete zvolit, které model horizontálního dělení na slouží k distribuci dat při definování tabulky. SQL Data Warehouse podporuje tyto vzory horizontálního dělení:

* Hodnota hash
* Kruhové dotazování.
* Replikace

### <a name="control-node"></a>Řídicí uzel

Řídicí uzel je brain datového skladu. Jde o prvek front-end, který komunikuje se všemi aplikacemi a připojeními. Modul MPP běží na uzlu ovládacího prvku k optimalizaci a koordinovat paralelních dotazů. Když odešlete dotaz T-SQL do služby SQL Data Warehouse, řídicí uzel ho transformuje na dotazy, které spustily každé distribuci paralelně.

### <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. Distribuce namapovat na výpočetních uzlech pro zpracování. Protože platíte za další výpočetní prostředky, SQL Data Warehouse znovu mapuje distribucí do výpočetních uzlů k dispozici. Počet výpočetních uzlů v rozmezí od 1 do 60 a je určen podle úrovně služeb pro datový sklad.

Každý výpočetní uzel má ID uzlu, který je viditelný v zobrazeních systému. Tím, že hledají sloupci $node_id v systémových zobrazeních, jejichž názvy začínají řetězcem sys.pdw_nodes uvidíte ID výpočetního uzlu. Seznam těchto zobrazení systému najdete v tématu [MPP systémová zobrazení](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Služba pro přesun dat
Přesun dat Service (DMS) je technologie přenosu dat, která koordinuje přesun dat mezi jednotlivými výpočetními uzly. Některé dotazy vyžadují přesunu dat pro zajištění přesných výsledků vrátí paralelní dotazy. Při přesunu dat je potřeba, DMS zajišťuje že ta správná data načte do správného umístění. 

## <a name="distributions"></a>Distribuce

Rozdělení je základní jednotkou úložiště a zpracování paralelních dotazů, ve kterých běží na distribuovaných datech. Při spuštění dotazu SQL Data Warehouse je práce rozdělena do 60 menších dotazů, které běží paralelně. Každý dotaz na 60 menší běží na jednom z distribuce data. Každý výpočetní uzel spravuje jeden nebo více 60 distribucí. Datový sklad s maximální výpočetních prostředků má k jednomu distribučnímu na výpočetním uzlu. Datový sklad s minimální výpočetních prostředků má distribuce na jeden výpočetní uzel.  

## <a name="hash-distributed-tables"></a>Hodnoty hash distribuované tabulky
Distribuovaná zatřiďovací tabulka dokáže poskytovat nejvyšší výkon dotazů pro spojování a agregaci pro velké tabulky. 

Sdílení dat do tabulky hash distribuované SQL Data Warehouse používá funkce hash pro každý řádek nedeterministicky přidělí k jednomu distribučnímu. V definici tabulky některý ze sloupců je určený jako sloupec distribuce. Funkce hash používá hodnoty ve sloupci distribuce přiřadit každý řádek k distribuci.

Následující diagram znázorňuje, jak úplné (tabulka nedistribuovaná) se uloží jako tabulka hash distribuován. 

![Distribuované tabulky](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuované tabulky")  

* Každý řádek patří k jednomu distribučnímu.  
* Deterministické hashovací algoritmus přiřadí k jednomu distribučnímu každý řádek.  
* Počet řádků tabulky na distribuci se liší podle různých velikostí tabulky.

Některé aspekty výkonu pro výběr distribučního sloupce, jako je například odlišnosti Nerovnoměrná distribuce dat a typy dotazů, které běží na systému.

## <a name="round-robin-distributed-tables"></a>Kruhové dotazování distribuované tabulky
Kruhové dotazování tabulky je nejjednodušší tabulky vytvořte a poskytuje rychlý výkon při použití jako pracovní tabulky pro načtení.

Distribuované tabulky kruhové dotazování rovnoměrně distribuuje data v tabulce, ale bez jakékoli další optimalizace. Rozdělení je nejprve vybrán náhodně a pak vyrovnávací paměti řádků jsou přiřazeny k rozdělení postupně. Je snadné k načtení dat do kruhové dotazování tabulky, ale výkon dotazů může být často vám dá víc tabulek provádět distribuci hodnot hash. Spojení tabulek kruhového vyžadují promísení dat, a to bude vyžadovat čas navíc.


## <a name="replicated-tables"></a>Replikované tabulky
Replikované tabulky. poskytuje nejrychlejší výkon dotazů pro malé tabulky.

Tabulka, která se replikuje ukládá do mezipaměti úplnou kopii tabulky na jednotlivých výpočetních uzlech. V důsledku toho replikuje tabulku eliminuje nutnost provádět přenos dat mezi výpočetní uzly před spojení nebo agregace. Replikované tabulky jsou nejlépe využít s malé tabulky. Dodatečné úložiště je povinný a existují další režie, které se účtují při zápis dat, což mohlo způsobit nepoužitelnost velké tabulky nepraktické.  

Následující diagram znázorňuje replikované tabulky. Pro službu SQL Data Warehouse je do mezipaměti replikované tabulky na první distribuci na jednotlivých výpočetních uzlech.  

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
