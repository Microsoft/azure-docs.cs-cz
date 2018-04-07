---
title: "Načtení dat z SQL serveru do Azure SQL Data Warehouse (SSIS) | Microsoft Docs"
description: "Ukazuje, jak vytvořit balíček integrační služby SSIS (SQL Server) pro přesun dat z široké škály zdrojů dat do SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Načtení dat z SQL serveru do Azure SQL Data Warehouse služby SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Vytvoření balíčku integrační služby SSIS (SQL Server) k načtení dat z SQL serveru do Azure SQL Data Warehouse. Volitelně můžete změny struktury, transformace a čistí data při průchodu SSIS datového toku.

V tomto kurzu provedete následující:

* V sadě Visual Studio vytvořte nový projekt integrační služby.
* Připojení ke zdrojům dat, včetně systému SQL Server (jako zdroj) a SQL Data Warehouse (jako cíl).
* Návrh SSIS balíčku, který načte data ze zdrojové do cílové.
* Spusťte služby SSIS balíček, který chcete načíst data.

Tento kurz používá jako zdroj dat systému SQL Server. SQL Server může být spuštěn místně nebo v virtuální počítač Azure.

## <a name="basic-concepts"></a>Základní koncepty
Balíček je jednotka práce v SSIS. Související balíčky jsou seskupené v projektech. Vytváření projektů a balíčků návrhu v sadě Visual Studio s SQL Server Data Tools. Proces návrhu je visual proces, ve kterém můžete přetahování součásti z panelu nástrojů na návrhovou plochu, připojte je a nastavte jejich vlastnosti. Po dokončení vašeho balíčku, můžete volitelně nasadit ho do systému SQL Server pro komplexní správu, monitorování a zabezpečení.

## <a name="options-for-loading-data-with-ssis"></a>Možnosti pro načítání dat pomocí služby SSIS
Integrace služby SSIS (SQL Server) je flexibilní sadu nástrojů, který poskytuje celou řadu možností pro připojení k a načítání dat do SQL Data Warehouse.

1. Použijte určení NET ADO pro připojení k SQL Data Warehouse. Tento kurz používá určení NET ADO, protože má nejmenší počet možností konfigurace.
2. Použijte cílové OLE DB pro připojení k SQL Data Warehouse. Tato možnost může poskytovat mírně lepší výkon než cílový NET ADO.
3. Příprava data v Azure Blob Storage pomocí úlohy nahrát objektů Blob Azure. Pak spusťte skript Polybase, který načte data do SQL Data Warehouse pomocí úlohy služby SSIS provést SQL. Tato možnost poskytuje nejlepší výkon ze tří zde uvedené možnosti. Chcete-li získat nahrát objekt Blob Azure úloh, stáhnout [Microsoft SQL Server 2016 integrační služby Feature Pack pro Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Další informace o používání funkce Polybase, najdete v části [Průvodce funkcí PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Než začnete
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

1. **SQL Server Integration Services (služby SSIS)**. Služby SSIS je součástí systému SQL Server a vyžaduje zkušební verze nebo verze na licencovanou verzi systému SQL Server. Chcete-li získat zkušební verze systému SQL Server 2016 ve verzi Preview, přečtěte si téma [SQL serveru hodnocení][SQL Server Evaluations].
2. Sadu **Visual Studio**. Bezplatná edice Community Visual Studio najdete [Visual Studio Community][Visual Studio Community].
3. **Nástroje SQL Server Data Tools pro sadu Visual Studio (SSDT)**. SQL Server Data Tools pro sadu Visual Studio najdete [stáhnout SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Ukázková data**. Tento kurz používá ukázková data uložená v systému SQL Server v ukázkové databázi AdventureWorks jako zdroj dat má být načten do SQL Data Warehouse. Chcete-li získat ukázkovou databázi AdventureWorks, přečtěte si téma [AdventureWorks 2014 ukázkové databáze][AdventureWorks 2014 Sample Databases].
5. **Databáze SQL Data Warehouse a oprávnění**. V tomto kurzu se připojí k instanci SQL Data Warehouse a načte data do ní. Musíte mít oprávnění k vytvoření tabulky a načíst data.
6. **Pravidlo brány firewall**. Budete muset vytvořit pravidlo brány firewall v SQL Data Warehouse s IP adresou místního počítače, než můžete uložit data do SQL Data Warehouse.

## <a name="step-1-create-a-new-integration-services-project"></a>Krok 1: Vytvoření nového projektu integrační služby
1. Spusťte sadu Visual Studio.
2. Na **soubor** nabídce vyberte možnost **nový | Projekt**.
3. Přejděte do **nainstalován | Šablony | Business Intelligence | Integrační služby** typy projektů.
4. Vyberte **integračních služeb projektu**. Zadejte hodnoty pro **název** a **umístění**a potom vyberte **OK**.

Visual Studio otevře a vytvoří nový projekt Integration Services (SSIS). Pak Visual Studio otevře návrháře jednoho nového balíčku služby SSIS (Package.dtsx) v projektu. Zobrazí následující obrazovka oblastí:

* Na levé straně **sada nástrojů** součásti služby SSIS.
* Uprostřed klikněte na návrhovou plochu, s několik karet. Obvykle se používá minimálně **tok řízení** a **toku dat** karty.
* Na pravé straně **Průzkumníku řešení** a **vlastnosti** podokna.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Krok 2: Vytvoření základní datový tok
1. Přetáhněte úlohu toku dat z panelu nástrojů k centru návrhové ploše (na **tok řízení** karta).
   
    ![][02]
2. Dvakrát klikněte na úlohu toku dat přejděte na kartu toku dat.
3. Ze seznamu jiných zdrojů v panelu nástrojů přetáhněte na zdroj ADO.NET na plochu návrháře. S adaptérem zdroj vybraný, změňte její název **zdroje systému SQL Server** v **vlastnosti** podokně.
4. Z jiných seznamu v panelu nástrojů a přetáhněte na návrhovou plochu pod zdrojem ADO.NET cíl pro technologii ADO.NET. Cílový adaptér, vybraný, změňte její název **SQL DW cílové** v **vlastnosti** podokně.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Krok 3: Konfigurace adaptér zdroje
1. Poklikejte na adaptér zdroj otevřete **Editor zdroje ADO.NET**.
   
    ![][03]
2. Na **Správce připojení** kartě **Editor zdroje ADO.NET**, klikněte na tlačítko **nový** vedle položky **Správce připojení ADO.NET** seznamu Chcete-li otevřít **nakonfigurovat správce připojení ADO.NET** dialogové okno a vytvořte nastavení připojení pro databázi systému SQL Server, ze kterého v tomto kurzu načte data.
   
    ![][04]
3. V **nakonfigurovat správce připojení ADO.NET** dialogové okno, klikněte na tlačítko **nový** tlačítko Otevřít **Správce připojení** dialogové okno a vytvořte nové datové připojení.
   
    ![][05]
4. V **Správce připojení** dialogové okno pole, provádět následující akce.
   
   1. Pro **zprostředkovatele**, vyberte poskytovatel dat SqlClient.
   2. Pro **název serveru**, zadejte název serveru SQL Server.
   3. V **Přihlaste se k serveru** vyberte nebo zadejte informace o ověřování.
   4. V **připojit k databázi** vyberte ukázkovou databázi AdventureWorks.
   5. Klikněte na tlačítko **testovací připojení**.
      
       ![][06]
   6. V dialogovém okně, které hlásí výsledky testu připojení, klikněte na **OK** se vrátíte do **Správce připojení** dialogové okno.
   7. V **Správce připojení** dialogové okno, klikněte na tlačítko **OK** se vrátíte do **nakonfigurovat správce připojení ADO.NET** dialogové okno.
5. V **nakonfigurovat správce připojení ADO.NET** dialogové okno, klikněte na tlačítko **OK** se vrátíte do **Editor zdroje ADO.NET**.
6. V **Editor zdroje ADO.NET**v **název tabulky nebo zobrazení** seznamu, vyberte **Sales.SalesOrderDetail** tabulky.
   
    ![][07]
7. Klikněte na tlačítko **Preview** zobrazíte prvních 200 řádků dat ve zdrojové tabulce v **náhled výsledků dotazu** dialogové okno.
   
    ![][08]
8. V **náhled výsledků dotazu** dialogové okno, klikněte na tlačítko **Zavřít** se vrátíte do **Editor zdroje ADO.NET**.
9. V **Editor zdroje ADO.NET**, klikněte na tlačítko **OK** konfiguraci zdroje dat dokončíte.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Krok 4: Připojte adaptér zdrojového na cílový adaptér
1. Vyberte zdroj adaptéru na návrhovou plochu.
2. Vyberte modrou šipkou, který rozšiřuje z adaptéru zdroje a přetáhněte jej do editoru cílové, dokud ho přichytí.
   
    ![][10]
   
    V typické SSIS balíčku použít několik dalších součásti ze sady nástrojů SSIS mezi zdroj a cíl změny struktury, transformace a čistí data při průchodu tok dat služby SSIS. Aby tento příklad co nejjednodušší, jsme připojujete, přímo do cílového umístění zdroje.

## <a name="step-5-configure-the-destination-adapter"></a>Krok 5: Konfigurace cílový adaptér
1. Poklikejte na adaptér cílové otevřete **ADO.NET cílové Editor**.
   
    ![][11]
2. Na **Správce připojení** kartě **ADO.NET cílové Editor**, klikněte na tlačítko **nový** vedle položky **Správce připojení** se seznam Otevřete **nakonfigurovat správce připojení ADO.NET** dialogové okno a vytvořte nastavení připojení pro databázi Azure SQL Data Warehouse, do kterého v tomto kurzu načte data.
3. V **nakonfigurovat správce připojení ADO.NET** dialogové okno, klikněte na tlačítko **nový** tlačítko Otevřít **Správce připojení** dialogové okno a vytvořte nové datové připojení.
4. V **Správce připojení** dialogové okno pole, provádět následující akce.
   1. Pro **zprostředkovatele**, vyberte poskytovatel dat SqlClient.
   2. Pro **název serveru**, zadejte název serveru SQL datového skladu.
   3. V **Přihlaste se k serveru** vyberte **ověřování použít SQL Server** a zadejte informace o ověřování.
   4. V **připojit k databázi** vyberte existující databázi SQL Data Warehouse.
   5. Klikněte na tlačítko **testovací připojení**.
   6. V dialogovém okně, které hlásí výsledky testu připojení, klikněte na **OK** se vrátíte do **Správce připojení** dialogové okno.
   7. V **Správce připojení** dialogové okno, klikněte na tlačítko **OK** se vrátíte do **nakonfigurovat správce připojení ADO.NET** dialogové okno.
5. V **nakonfigurovat správce připojení ADO.NET** dialogové okno, klikněte na tlačítko **OK** se vraťte do **ADO.NET cílové Editor**.
6. V **ADO.NET cílové Editor**, klikněte na tlačítko **nový** vedle **použití tabulky nebo zobrazení** seznamu otevřete **Create Table** dialogové okno vytvořit nové cílové tabulky se seznam sloupců, která odpovídá zdrojové tabulky.
   
    ![][12a]
7. V **Create Table** dialogové okno pole, provádět následující akce.
   
   1. Změnit název cílové tabulky pro **podrobnosti prodejní objednávky**.
   2. Odeberte **rowguid** sloupce. **Uniqueidentifier** datový typ není podporován v SQL Data Warehouse.
   3. Změnit datový typ **LineTotal** sloupec, který se **peníze**. **Decimal** datový typ není podporován v SQL Data Warehouse. Informace o podporované datové typy najdete v tématu [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Klikněte na tlačítko **OK** pro vytvoření tabulky a návrat na **ADO.NET cílové Editor**.
8. V **ADO.NET cílové Editor**, vyberte **mapování** zjistit, jak sloupce ve zdroji jsou mapované na sloupce v cílovém.
   
    ![][13]
9. Klikněte na tlačítko **OK** konfiguraci zdroje dat dokončíte.

## <a name="step-6-run-the-package-to-load-the-data"></a>Krok 6: Spuštění balíček, který chcete načíst data
Spusťte balíček kliknutím **spustit** tlačítka na panelu nástrojů nebo výběrem jedné z **spustit** možnosti na **ladění** nabídky.

Jako balíček začne spustit, zobrazí žlutý souborů Wheel roztočený udávajících aktivit a také počet řádků, pokud zpracovat.

![][14]

Pokud balíček bylo dokončeno, uvidíte zelené značky zaškrtnutí indikující úspěch a také celkový počet řádků dat načtených ze zdrojové do cílové.

![][15]

Blahopřejeme! SQL Server Integration Services jste úspěšně použili k načtení dat do Azure SQL Data Warehouse.

## <a name="next-steps"></a>Další kroky
* Další informace o toku dat SSIS. Začněte zde: [toku dat][Data Flow].
* Informace o ladění a odstraňování potíží v prostředí návrhu vaší balíčky správné. Začněte zde: [řešení potíží s nástrojů pro vývoj balíček][Troubleshooting Tools for Package Development].
* Informace o nasazení služby SSIS projekty a balíčky Server integračních služeb nebo do jiného umístění úložiště. Začněte zde: [projekty nasazení a balíčky][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
