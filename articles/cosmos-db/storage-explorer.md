---
title: Správa služby Azure Cosmos DB v Průzkumníku služby Azure Storage
description: Zjistěte, jak spravovat službu Azure Cosmos DB v Průzkumníku služby Azure Storage.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 18f580f1eae31c9bf3626e100217467bb48ca881
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Správa služby Azure Cosmos DB v Průzkumníku služby Azure Storage (Preview)

Použití služby Azure Cosmos DB v Průzkumníku služby Azure Storage umožňuje uživatelům spravovat entity Azure Cosmos DB, manipulovat s daty, aktualizovat uložené procedury a triggery, stejně jako další entity Azure, jako jsou objekty blob a fronty služby Storage. Nyní můžete pomocí jednoho nástroje spravovat různé entity Azure na jednom místě. V současné době podporuje Průzkumník služby Azure Storage účty SQL, MongoDB, Graph a Table.

V tomto článku se dozvíte, jak použít Průzkumníka služby Storage ke správě služby Azure Cosmos DB.


## <a name="prerequisites"></a>Požadavky

Účet služby Azure Cosmos DB pro rozhraní SQL API <!--or MongoDB API-->. Pokud účet nemáte, můžete si ho vytvořit na webu Azure Portal, jak je popsáno v tématu [Azure Cosmos DB: Vytvoření webové aplikace SQL API s využitím .NET a webu Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalace

Nainstalujte nejnovější části Průzkumníka služby Azure Storage: [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). V současné době podporujeme verze pro Windows, Linux a Mac.

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

1. Po instalaci **Průzkumníka služby Azure Storage** klikněte na ikonu modulu **plug-in** na levé straně, jak je znázorněno na následujícím obrázku:
       
   ![Ikona modulu plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Vyberte **Přidat účet Azure** a pak klikněte na **Přihlásit**.

   ![Připojení k předplatnému Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. V dialogovém okně **Přihlášení k Azure** vyberte **Přihlásit** a zadejte své přihlašovací údaje Azure.

    ![Přihlášení](./media/storage-explorer/sign-in.png)

3. Vyberte ze seznamu své předplatné a klikněte na **Použít**.

    ![Použít](./media/storage-explorer/apply-subscription.png)

    Podokno Průzkumníka se aktualizuje a zobrazí účty ve vybraném předplatném.

    ![Seznam účtů](./media/storage-explorer/account-list.png)

    Právě jste úspěšně propojili svůj **účet služby Cosmos DB** se svým předplatným Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce

Alternativním způsobem, jak se připojit ke službě Azure Cosmos DB, je použít připojovací řetězec. Pomocí následujícího postupu se připojte pomocí připojovacího řetězce.

1. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Cosmos DB** a zvolte **Připojit ke službě Cosmos DB...**

    ![Připojení ke službě Cosmos DB pomocí připojovacího řetězce](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Aktuálně se podporují pouze rozhraní SQL a Table API. Zvolte rozhraní API, vložte **Připojovací řetězec**, zadejte **Popisek účtu**, klikněte na **Další** a zkontrolujte souhrn a pak se kliknutím na **Připojit** připojte k účtu služby Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Připojovací řetězec](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Připojení ke službě Azure Cosmos DB pomocí místního emulátoru
Pomocí následujících kroků se připojte ke službě Azure Cosmos DB pomocí emulátoru, který aktuálně podporuje pouze účet SQL.
1. Nainstalujte emulátor a spusťte ho. Pokyny k instalaci emulátoru najdete v tématu [Emulátor služby Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator).
2. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Cosmos DB** a zvolte **Připojit k emulátoru služby Cosmos DB...**

    ![Připojení ke službě Cosmos DB pomocí emulátoru](./media/storage-explorer/emulator-entry.png)

3. Aktuálně se podporuje pouze rozhraní SQL API. Vložte **Připojovací řetězec**, zadejte **Popisek účtu**, klikněte na **Další** a zkontrolujte souhrn a pak se kliknutím na **Připojit** připojte k účtu služby Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Dialogové okno Připojení ke službě Cosmos DB pomocí emulátoru](./media/storage-explorer/emulator-dialog.png)



## <a name="azure-cosmos-db-resource-management"></a>Správa prostředků Azure Cosmos DB

Účet služby Azure Cosmos DB můžete spravovat pomocí následujících operací:
* Otevření účtu na webu Azure Portal
* Přidání prostředku do seznamu Rychlý přístup
* Vyhledávání a aktualizace prostředků
* Vytvářet a odstraňovat databáze
* Vytvářet a odstraňovat kolekce
* Vytváření, úpravy, odstraňování a filtrování dokumentů
* Správa uložených procedur, triggerů a funkcí definovaných uživatelem

### <a name="quick-access-tasks"></a>Úlohy rychlého přístupu

Po kliknutí pravým tlačítkem na předplatné v podokně Průzkumníka můžete provádět řadu úloh rychlého přístupu:

* Po kliknutí pravým tlačítkem na účet nebo databázi Azure Cosmos DB můžete zvolit **Otevřít na portálu** a spravovat prostředek v prohlížeči na webu Azure Portal.

     ![Otevřít na portálu](./media/storage-explorer/open-in-portal.png)

* Účet, databázi nebo kolekci Azure Cosmos DB můžete také přidat do seznamu **Rychlý přístup**.
* Možnost **Hledat odsud** umožňuje hledat klíčová slova v zadané cestě.

    ![Hledat odsud](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Správa databází a kolekcí
#### <a name="create-a-database"></a>Vytvoření databáze 
-   Klikněte pravým tlačítkem na účet služby Azure Cosmos DB, zvolte **Vytvořit databázi**, zadejte název databáze a stisknutím klávesy **Enter** dokončete vytváření.
       
    ![Vytvoření databáze](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Odstranění databáze
- Klikněte pravým tlačítkem na databázi, klikněte na **Odstranit databázi** a v automaticky otevíraném okně klikněte na **Ano**. Uzel databáze se odstraní a účet služby Azure Cosmos DB se automaticky aktualizuje.

    ![Odstranění databáze 1](./media/storage-explorer/delete-database1.png)  

    ![Odstranění databáze 2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Vytvoření kolekce
1. Klikněte pravým tlačítkem na svou databázi, zvolte **Vytvořit kolekci** a zadejte následující informace, jako jsou **ID kolekce**, **Kapacita úložiště** atd. Kliknutím na **OK** vytváření dokončete. 

    ![Vytvoření kolekce 1](./media/storage-explorer/create-collection.png)

    ![Vytvoření kolekce 2](./media/storage-explorer/create-collection2.png) 

2. Vyberte **Neomezená**, abyste mohli zadat klíč oddílu, a kliknutím na **OK** vytváření dokončete.

    Pokud se při vytváření kolekce použije klíč oddílu, po vytvoření se hodnota klíče oddílu pro kolekci nedá změnit. Informace o nastavení klíče oddílu najdete v tématu popisujícím [Návrh pro dělení](partition-data.md#designing-for-partitioning).

    ![Klíč oddílu](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Odstranění kolekce
- Klikněte pravým tlačítkem na kolekci, klikněte na **Odstranit kolekci** a v automaticky otevíraném okně pak klikněte na **Ano**. 

    Uzel kolekce se odstraní a databáze se automaticky aktualizuje.

    ![Odstranění kolekce](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Správa dokumentů

#### <a name="create-and-modify-documents"></a>Vytváření a úpravy dokumentů
- Pokud chcete vytvořit nový dokument, otevřete v levém okně **Dokumenty**, klikněte na **Nový dokument**, v pravém podokně upravte obsah a pak klikněte na **Uložit**. Můžete také aktualizovat existující dokument a pak kliknout na **Uložit**. Změny můžete zahodit kliknutím na **Zahodit**.

    ![Dokument](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Odstranění dokumentu
- Kliknutím na tlačítko **Odstranit** odstraňte vybraný dokument.

#### <a name="query-for-documents"></a>Dotazování dokumentů
- Upravte filtr dokumentů zadáním [příkazu jazyka SQL](sql-api-sql-query.md) a pak klikněte na **Použít**.

    ![Filtr dokumentů](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Správa grafů

#### <a name="create-and-modify-vertex"></a>Vytvoření a úprava vrcholu
1. Pokud chcete vytvořit nový vrchol, v levém okně otevřete **Graph**, klikněte na **Nový vrchol**, upravte obsah a pak klikněte na **OK**.    
2. Pokud chcete upravit existující vrchol, klikněte na ikonu pera v pravém podokně.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Odstranění grafu
- Pokud chcete odstranit vrchol, klikněte na ikonu koše vedle názvu vrcholu.

#### <a name="filter-for-graph"></a>Filtrování grafu
- Upravte filtr grafu zadáním [dotazu Gremlin](gremlin-support.md) a pak klikněte na **Použít filtr**.

    ![Filtr grafu](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Správa tabulek

#### <a name="create-and-modify-table"></a>Vytvoření a úprava tabulky
1. Pokud chcete vytvořit novou tabulku, v levém okně otevřete **Entity**, klikněte na **Přidat**, upravte obsah dialogového okna **Přidat entitu**, přidejte vlastnost kliknutím na tlačítko **Přidat vlastnost** a pak klikněte na **Vložit**.
2. Pokud chcete upravit tabulku, klikněte na **Upravit**, upravte obsah a pak klikněte na **Aktualizovat**.

    ![Table](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Import a export tabulky
1. Pokud chcete importovat tabulku, klikněte na tlačítko **Importovat** a zvolte existující tabulku.
2. Pokud chcete exportovat tabulku, klikněte na tlačítko **Exportovat** a zvolte cíl.

    ![Import a export tabulky](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Odstranění entit
- Vyberte entity a klikněte na tlačítko **Odstranit**.

    ![Odstranění tabulky](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Dotaz na tabulku
- Klikněte na tlačítko **Dotaz**, zadejte podmínky dotazu a pak klikněte na tlačítko **Provést dotaz**. Zavřete podokno Dotaz kliknutím na tlačítko **Zavřít dotaz**.

    ![Dotaz na tabulku](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Správa uložených procedur, triggerů a funkcí definovaných uživatelem
* Pokud chcete vytvořit uloženou proceduru, v levém stromu klikněte pravým tlačítkem na **Uložená procedura**, zvolte **Vytvořit uloženou proceduru**, v levém okně zadejte název, v pravém okně zadejte skripty uložené procedury a pak klikněte na **Vytvořit**. 
* Můžete také upravovat existující uložené procedury tak, že na ně dvakrát kliknete, provedete aktualizaci a pak kliknutím na **Aktualizovat** uložíte změny nebo je kliknutím na **Zahodit** zrušíte.

    ![Uložená procedura](./media/storage-explorer/stored-procedure.png)
* Operace týkající se **triggerů** a **funkcí definovaných uživatelem** jsou podobné **uloženým procedurám**.

## <a name="next-steps"></a>Další kroky

* Podívejte se na následující video a zjistěte, jak používat službu Azure Cosmos DB v Průzkumníku služby Azure Storage: [Použití služby Azure Cosmos DB v Průzkumníku služby Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Další informace o Průzkumníku služby Storage a připojení dalších služeb najdete v tématu [Začínáme s Průzkumníkem služby Storage (Preview)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

