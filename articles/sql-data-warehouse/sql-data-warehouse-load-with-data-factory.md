---
title: "Načtení dat do Azure SQL Data Warehouse – Data Factory | Microsoft Docs"
description: "V tomto kurzu načte data do Azure SQL Data Warehouse pomocí Azure Data Factory a používá jako zdroj dat databázi systému SQL Server."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Načtení dat do SQL Data Warehouse pomocí služby Data Factory

Azure Data Factory můžete použít k načtení dat do Azure SQL Data Warehouse z jakéhokoli z [podporovanými úložišti dat zdroj](../data-factory/copy-activity-overview.md). Například můžete načtení dat z Azure SQL database nebo databáze Oracle do SQL data warehouse pomocí služby Data Factory. Kurz v tomto článku se dozvíte, jak načíst data z databáze SQL serveru místně do SQL data warehouse.

**Odhad času**: v tomto kurzu dokončení po splnění předpokladů trvá asi 10 až 15 minut.

## <a name="prerequisites"></a>Požadavky

- Je nutné **databáze systému SQL Server** s tabulkami, které obsahují data, která mají být zkopírovali do SQL data warehouse.  

- Je třeba online **SQL Data Warehouse**. Pokud již datový sklad nemáte, přečtěte si postup [vytvoření Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).

- Budete potřebovat **účet úložiště Azure**. Pokud již nemáte účet úložiště, přečtěte si postup [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md). Pro nejlepší výkon vyhledejte účet úložiště a datového skladu ve stejné oblasti Azure.

## <a name="configure-a-data-factory"></a>Konfigurace služby data factory
1. Přihlaste se k portálu [Azure Portal][].
2. Vyhledejte datového skladu a kliknutím ho otevřete.
3. V hlavním okně klikněte na **načítání dat** > **Azure Data Factory**.

    ![Spustí Průvodce načítání dat](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Pokud nemáte objekt pro vytváření dat ve vašem předplatném Azure, uvidíte **nový objekt pro vytváření dat** dialogové okno na samostatné kartě prohlížeče. Zadejte požadované informace a klikněte na tlačítko **vytvořit**. Po vytvoření objektu pro vytváření dat **nový objekt pro vytváření dat** dialog se zavře a zobrazí **vyberte objekt pro vytváření dat** dialogové okno.

    Pokud máte jeden nebo více datové továrny už v rámci předplatného Azure, uvidíte **vyberte objekt pro vytváření dat** dialogové okno. V tomto dialogovém můžete vybrat existující datovou továrnu nebo klikněte na tlačítko **vytvořit nový objekt pro vytváření dat** vytvořit nový.

    ![Konfigurace objektu pro vytváření dat](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. V **vyberte objekt pro vytváření dat** dialogové okno, **načíst data** ve výchozím nastavení je vybraná možnost. Klikněte na tlačítko **Další** chcete začít vytvářet dat načítání úloh.

## <a name="configure-the-data-factory-properties"></a>Konfigurovat vlastnosti objektu pro vytváření dat
Teď, když jste vytvořili objekt pro vytváření dat, dalším krokem je konfigurace dat načítání plánu.

1. Pro **název úlohy**, zadejte **DWLoadData fromSQLServer**.
2. Použít výchozí **spustit jednou nyní** možnost, klikněte na tlačítko **Další**.

    ![Nakonfigurujte plán zatížení](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Konfigurace zdrojového úložiště dat a brány
Objekt pro vytváření dat se teď říct o databázi systému SQL Server na místě, ze kterého chcete načíst data.

1. Zvolte **systému SQL Server** z podporované zdrojové dat ukládání katalogu a klikněte na tlačítko **Další**.

    ![Vyberte zdroj systému SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. A **zadejte místní databázi systému SQL Server** otevře se dialogové okno. První **název připojení** je automaticky vyplněno pole. Druhé pole požádá o název **brány**. Pokud používáte existující datovou továrnu, která již má bránu, můžete znovu použít bránu vyberete v rozevíracím seznamu. Klikněte **vytvořit bránu** odkaz pro vytvoření Brána pro správu dat.  

    > [!NOTE]
    > Pokud zdrojového úložiště dat je místně nebo na virtuálním počítači Azure IaaS, brána pro správu dat je požadovaná. Brána je v relaci 1-1 pomocí služby data factory. Nedá se použít z jiného objektu pro vytváření dat, ale mohou být využívána dat více načítání úlohy s ve stejném objektu pro vytváření dat. Brána slouží k připojení k několika úložišť dat při spuštění úlohy načítání dat.
    >
    > Podrobné informace o bráně najdete v tématu [Brána pro správu dat](../data-factory/v1/data-factory-data-management-gateway.md) článku.

3. A **vytvořit bránu** zobrazí se dialogové okno. Název zadejte **GatewayForDWLoading**a klikněte na tlačítko **vytvořit**.

4. A **konfigurace brány** zobrazí se dialogové okno. Klikněte na tlačítko **spusťte Expresní instalace na tomto počítači** automaticky stáhnout, nainstalovat a zaregistrovat brány pro správu dat na počítači aktuální. Průběh se zobrazí v místním okně. Pokud se počítač nemůže připojit k úložišti dat, můžete ručně [stáhnout a nainstalovat bránu](https://www.microsoft.com/download/details.aspx?id=39717) na počítači, který může připojit k datům uložit a potom pomocí klíče k registraci.
    > [!NOTE]
    > Expresní instalace nativně pracuje s Microsoft Edge a prohlížeče Internet Explorer. Pokud používáte Google Chrome, nejprve nainstalujte rozšíření ClickOnce z internetového obchodu Chrome.

    ![Spusťte instalační program Express](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Počkejte, než instalační program brány k dokončení. Jakmile se brána je úspěšně zaregistrovaný a je online, místní okno se zavře a nové bráně se zobrazí v poli brána. Potom vyplňte zbývající požadovaná pole následujícím způsobem, pak klikněte na tlačítko **Další**.
    - **Název serveru**: název místního serveru SQL.
    - **Název databáze**: databáze systému SQL Server.
    - **Přihlašovací údaje šifrování**: použijte výchozí "ve webovém prohlížeči".
    - **Typ ověřování**: Zvolte typ ověřování, kterou používáte.
    - **Uživatelské jméno** a **heslo**: Zadejte uživatelské jméno a heslo pro uživatele, který má oprávnění ke zkopírování dat.

    ![Spusťte instalační program Express](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. Dalším krokem je vybrat tabulky, ze kterého chcete zkopírovat data. Tabulky můžete filtrovat pomocí klíčových slov. A můžete zobrazit náhled schéma dat a tabulkou v dolním panelu. Po dokončení váš výběr, klikněte na tlačítko **Další**.

    ![Výběr tabulek](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Nakonfigurujte cíl, datový sklad SQL

Objekt pro vytváření dat se teď říct o informace o cíli.

1. Informace o připojení SQL Data Warehouse se vyplní automaticky. Zadejte heslo pro uživatelské jméno. a klikněte na tlačítko **Další**.

    ![Konfigurace cílového](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Mapování u inteligentního tabulky se zobrazí, že mapy zdrojové do cílové tabulky podle názvů tabulek. Pokud tabulka neexistuje v cílovém umístění, ve výchozím nastavení ADF vytvoří jednu se stejným názvem (to platí pro SQL Server nebo Azure SQL Database jako zdroj). Můžete také mapovat k existující tabulce. Zkontrolujte a klikněte na **Další**.

    ![Mapování tabulek](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Zkontrolujte mapování schématu a vyhledejte chyby nebo upozornění. Inteligentní mapování je založena na název sloupce. Pokud konverzi nepodporovaný datový typ mezi zdrojovým a cílovým sloupcem se zobrazí chybová zpráva spolu s odpovídající tabulky. Pokud si zvolíte umožníte Data Factory automaticky vytvářet tabulky, převod typu správné dat může dojít, pokud je nutná k vyřešení nekompatibilita mezi zdrojovým a cílovým úložišti.

    ![Schéma mapování](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Klikněte na **Další**.

## <a name="configure-the-performance-settings"></a>Konfigurovat nastavení výkonu
V konfiguraci výkonu nakonfigurujete účet úložiště Azure používají pro pracovní data předtím, než se načte do SQL Data Warehouse pomocí performantly [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Po dokončení kopírování průběžných dat v úložišti se automaticky vyčistí.

Vyberte stávající účet úložiště Azure a klikněte na **Další**.

![Konfigurovat pracovní objektů blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Zkontrolujte souhrnné informace a nasaďte kanál

Zkontrolujte konfiguraci a klikněte na tlačítko **Dokončit** tlačítko nasaďte kanál.

![Nasazení služby data factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Probíhá načítání dat monitorování

Zobrazí se průběh nasazení a výsledkem **nasazení** stránky.

1. Po dokončení nasazení klikněte na odkaz, která uvádí, že **kliknutím sem monitorování kopie kanálu** sledovat data průběhu načítání.

    ![Monitorování kanálu](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. Nově vytvořený **DWLoadData fromSQLServer** kanálu načítání dat se automaticky vybere ze levé straně **Průzkumníka prostředků**.

    ![Zobrazení kanálu](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Klikněte do kanálu v panelu střední zobrazit podrobný stav pro každou tabulku, která se mapuje na aktivitu.

    ![Zobrazení tabulky aktivity](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Dále klikněte na aktivity a zobrazit data načítání podrobností o v pravém panelu včetně velikost dat, řádky, propustnost, atd.

    ![Zobrazit podrobnosti o aktivitě tabulky](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Chcete-li spustit tento zobrazení monitorování později, přejděte do SQL Data Warehouse, klikněte na tlačítko **načítání dat > Azure Data Factory**, vyberete továrnu a zvolit **monitorování existující načítání úlohy**.

## <a name="next-steps"></a>Další kroky

Migrace databáze do SQL Data Warehouse, najdete v části [Přehled migrace](sql-data-warehouse-overview-migrate.md).

Další informace o Azure Data Factory a jeho funkce pro přesun dat, naleznete v následujících článcích:

- [Úvod do Azure Data Factory](../data-factory/introduction.md)
- [Přesun dat pomocí aktivity kopírování](../data-factory/copy-activity-overview.md)
- [Přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](../data-factory/connector-azure-sql-data-warehouse.md)

Chcete-li prozkoumat vaše data v SQL Data Warehouse, najdete v následujících článcích:

- [Připojení k SQL Data Warehouse pomocí sady Visual Studio a rozšíření SSDT](sql-data-warehouse-query-visual-studio.md)
- [Vizuální data s Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Azure Portal]: https://portal.azure.com
