---
title: Spravovat databáze v Azure SQL Data Warehouse | Microsoft Docs
description: Přehled správy databází SQL Data Warehouse. Zahrnuje nástroje pro správu, Dwu a Škálováním na více systémů, řešení potíží s výkon dotazů, vytvoření zásad zabezpečení a obnovení databáze z poškození dat nebo z místní výpadku výkonu.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: ''
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: acf521bdc15dfab4c7e43081159bc1385768838e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198488"
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Spravovat databáze v Azure SQL Data Warehouse
SQL Data Warehouse automatizuje mnoho aspektů správy vašich databází. Například škálování výkonu stačí upravit platit pro správnou úroveň výpočetní prostředky a potom umožní provádět všechny operace škálování a škálování zpět SQL Data Warehouse.

Nepochybně můžete sledovat vaše úlohy identifikace potřeb výkonu a také vyřešit dlouho běžící dotazy. Musíte také provést několik úloh zabezpečení ke správě oprávnění pro uživatele a přihlášení.

Tento přehled popisuje tyto aspekty správy SQL Data Warehouse.

* Nástroje pro správu
* Škálování výpočetní kapacity
* Pozastavení a obnovení
* Osvědčené postupy z hlediska výkonu
* Monitorování dotazu
* Zabezpečení
* Zálohování a obnovení

## <a name="management-tools"></a>Nástroje pro správu
Můžete použít celou řadu nástrojů pro správu databází v SQL Data Warehouse. Jak budete spravovat databáze, budete vyvíjet předvoleb nástroje pro každý typ úlohy, kterou je třeba provést.

### <a name="azure-portal"></a>Azure Portal
[Portál Azure] [ Azure portal] je webový portál, kde můžete vytvářet, aktualizovat a odstranit databáze a monitorovat prostředky v databázi. Tento nástroj je skvělé, pokud jste se právě Začínáme s Azure, Správa malý počet datového skladu databáze, nebo musí rychle něco udělat.

Chcete-li začít používat portál Azure, přečtěte si téma [vytvoření SQL Data Warehouse (portál Azure)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>Nástroje SQL Server Data Tools v sadě Visual Studio
[SQL Server Data Tools][SQL Server Data Tools] (SSDT) v sadě Visual Studio umožňuje připojit, spravovat a vývoji vaší databáze. Pokud jste vývojář aplikací obeznámeni s Visual Studio nebo jiné integrované vývojové prostředí (integrovaného vývojového prostředí), zkuste použít rozšíření SSDT v sadě Visual Studio.

Rozšíření SSDT zahrnuje Průzkumník objektů SQL serveru, která umožňuje vizualizovat, připojit a spouštět skripty pro databáze SQL Data Warehouse. Chcete-li rychle se připojit k SQL Data Warehouse, můžete jednoduše klikněte na tlačítko **otevřete v sadě Visual Studio** tlačítko v příkazu panelu při zobrazení databáze podrobnosti na portálu Azure.  

Chcete-li začít pracovat s rozšířením SSDT v sadě Visual Studio, přečtěte si téma [dotazu Azure SQL Data Warehouse pomocí sady Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Nástroje příkazového řádku
Nástroje příkazového řádku jsou ideální pro automatizaci vašich úloh.  Prostředí PowerShell a sqlcmd dvěma způsoby skvělé a automatizovat procesy.  Doporučujeme, abyste tyto nástroje pro správu velkého počtu logických serverů a jako úlohy plánování může být skripty a pak automatizované nasazení prostředků změny v produkčním prostředí.

### <a name="dynamic-management-views"></a>Zobrazení dynamické správy
Zobrazení dynamické správy jsou másla a chléb správy SQL Data Warehouse. Téměř všechny informace, které se zobrazí na portálu spoléhá na zobrazení dynamické správy. Seznam SQL Data Warehouse zobrazení dynamické správy najdete v sekci [zobrazení systému SQL Data Warehouse][SQL Data Warehouse system views].

Abyste mohli začít, najdete v části [připojit a zadávat dotazy pomocí sqlcmd][Connect and query with sqlcmd], a [vytvoření databáze (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Škálování výpočetního výkonu
V SQL Data Warehouse můžete rychle škálovat výkonu out nebo zpět zvýšením nebo snížením výpočetní prostředky procesoru, paměti a šířky pásma vstupně-výstupní operace. Škálování výkonu, všechny, které je potřeba je upravit počet jednotky datového skladu (Dwu) které SQL Data Warehouse přiděluje k vaší databázi. SQL Data Warehouse rychle provede změnu a zpracovává všechny základní změny hardwaru nebo softwaru.

Další informace o škálování Dwu najdete v tématu [škálování výkonu].

## <a name="pause-and-resume"></a>Pozastavení a obnovení
Abyste ušetřili náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Například pokud nebudete používat databázi v noci a o víkendech, můžete pozastavit tyto v době a obnovit během dne. Zatímco databáze byla pozastavena, se nezapočítávají pro Dwu.

Další informace najdete v tématu [pozastavit výpočetní][Pause compute], a [obnovit výpočty][Resume compute].

## <a name="performance-best-practices"></a>Osvědčené postupy z hlediska výkonu
Při Začínáme s novou technologií, zjišťování tipy a triky, které fungují nejlepší vpravo od začátku můžete ušetřit spoustu času.  Osvědčené postupy v rámci řadu témata s našimi zjistíte.

Mnoho souhrn nejdůležitější aspekty při vývoji vaše úlohy najdete v sekci [SQL Data Warehouse osvědčené postupy][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Monitorování dotazu
Někdy dotazu běží příliš dlouho, ale nejsou opravdu které z nich je který. SQL Data Warehouse má zobrazení dynamické správy (zobrazení dynamické správy), které vám pomůže pochopit dotazu, který trvá příliš dlouho.

Dlouho běžící dotazy, najdete v tématu [sledovat vaše úlohy pomocí zobrazení dynamické správy][Monitor your workload using DMVs].

## <a name="security"></a>Zabezpečení
K zachování zabezpečení systému, musí být na výstrahu a chránit proti libovolného typu neoprávněného přístupu. Zabezpečení systému je potřeba Ujistěte se, že jsou pravidla brány firewall na místě, takže jenom oprávnění IP adresy se mohou připojit. Musí být správné ověření přihlašovacích údajů uživatele. Jakmile se uživatel připojil k databázi, musí mít uživatel jenom oprávnění k provedení minimální počet akcí. K zabezpečení dat, můžete použít šifrování. Je také důležité, aby byly auditování a sledování tak události můžete vrátit, pokud je podezřelé aktivity.

Další informace o správě zabezpečení, přejděte na [Přehled zabezpečení][Security overview].

## <a name="back-up-and-restore"></a>Zálohování a obnovení
Spolehlivé zálohování dat je nedílnou součást vámi vyžádaných žádné produkční databázi. SQL Data Warehouse zajišťuje dat bezpečné zálohováním automaticky aktivní databáze v pravidelných intervalech. Tyto zálohy umožňují obnovit z scénáře, kde jste poškozená data nebo neúmyslně vyřazen data nebo databáze.  Plán zálohování dat, zásady uchovávání informací a jak obnovit databázi, najdete v části [obnovení ze snímku][Restore from snapshot].

## <a name="next-steps"></a>Další postup
Pomocí funkční databáze návrhu zásad bude bylo snazší spravovat databáze v SQL Data Warehouse. Další informace, přejděte na [přehled vývoje][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: pause-and-resume-compute-portal.md#pause-compute
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: pause-and-resume-compute-portal.md#resume-compute
[škálování výkonu]: quickstart-scale-compute-portal.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
