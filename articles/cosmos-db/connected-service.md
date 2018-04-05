---
title: Připojená služba sady Visual Studio pro službu Azure Cosmos DB
description: Umožňuje vývojářům snadné připojení účtu služby Azure Cosmos DB a správu prostředků prostřednictvím připojených služeb sady Visual Studio.
services: cosmos-db
documentationcenter: ''
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: 93be368d34f02e64d11abe9a04b11272ce18124d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Připojená služba sady Visual Studio (Preview)

Připojené služby sady Visual Studio umožňují vývojářům snadné připojení účtu služby Azure Cosmos DB a správu prostředků.

Průzkumník dat v připojené službě můžete použít také k vytváření uložených procedur, funkcí UDF a triggerů pro provádění obchodní logiky na straně serveru. Průzkumník dat zpřístupní všechna integrovaná programová data v rozhraních API, ale zajistí jednoduchý přístup k vašim datům.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte následující položky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 
* Účet služby Azure Cosmos DB. Pokud ještě žádný nemáte, vytvořte si ho na portálu Azure Portal pomocí postupu v tématu popisujícím [vytvoření účtu služby Azure Cosmos DB](create-sql-api-dotnet.md) nebo si přečtěte část [Vytvoření účtu služby Azure Cosmos DB v nástroji připojené služby](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Pokud pro účely vývoje chcete používat místní prostředí, můžete použít [Emulátor služby Azure Cosmos DB](local-emulator.md). Prostředí emuluje službu Azure Cosmos DB.
* Sadu [Visual Studio](http://www.visualstudio.com/).
* Nejnovější součásti připojené služby Azure Cosmos DB. Připojenou službu Azure Cosmos DB můžete stáhnout z Visual Studio Marketplace, jak je znázorněno na následujícím snímku obrazovky. Otevřete na svém počítači sadu **Visual Studio**. V nabídce **Nástroje** vyberte **Rozšíření a aktualizace** a pak zvolte **Online** / **Visual Studio Marketplace**. Zadejte **cosmosdb** pro vyhledání souborů.

    Připojenou službu Azure Cosmos DB můžete nainstalovat také z [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Snímek obrazovky se soubory připojené služby ke stažení](./media/connected-service/connected-service-downloadbits.png) 

Po stažení rozšíření připojené služby Azure Cosmos DB zavřete Visual Studio, aby se rozšíření mohlo nainstalovat.

## <a id="SetupVS"></a>Nastavení řešení v sadě Visual Studio
1. Otevřete na svém počítači sadu **Visual Studio**.
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
3. V dialogovém okně **Nový projekt** vyberte **Visual C#** / **Konzolová aplikace (.NET Framework)** nebo **Aplikace WPF (.NET Framework)**, pojmenujte svůj projekt a pak klikněte na **OK**.

    ![Snímek obrazovky okna Nový projekt](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Přidání připojené služby a účtu
1. V Průzkumníku řešení klikněte pravým tlačítkem na uzel projektu a pak vyberte **Přidat** / **Připojená služba**. Nebo klikněte na nabídku **Projekt** a pak vyberte **Přidat připojenou službu**.

    ![Snímek obrazovky okna Přidat připojenou službu](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. Na stránce připojené služby klikněte na **Připojené služby** / **Azure Cosmos DB**. Otevře se stránka **Azure Cosmos DB**.

    ![Snímek obrazovky okna Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Klikněte na šipku dolů a poprvé se přihlaste nebo přidejte účet. Po přihlášení se v prázdné oblasti zobrazí všechny účty služby Azure Cosmos DB. Zvolte jeden účet služby Azure Cosmos DB, který chcete přidat k vašemu projektu.

    ![Snímek obrazovky okna pro přihlášení s uvedeným účtem databáze](./media/connected-service/connected-service-add-db-account.png)
4. Po přidání účtu služby Azure Cosmos DB se do projektu přidá složka připojené služby pro účet služby Azure Cosmos DB. Opakováním kroků 1 až 3 můžete přidat více než jeden účet služby Azure Cosmos DB.

    ![Snímek obrazovky okna se složkou připojené služby](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Po přidání připojené služby Azure Cosmos DB povolte přístup ke službě Azure Cosmos DB upravením projektu jedním z následujících způsobů:

    * Nainstaluje se několik balíčků nuget, které vyžaduje klient Azure Cosmos DB. Můžete je zobrazit v konfiguračním souboru balíčků. 

        ![Nová konfigurace balíčků Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * Do konfiguračního souboru projektu, v tomto případě App.config, se přidá identifikátor URI a klíč pro připojení ke službě Azure Cosmos DB. 

        ![Nová konfigurace aplikace Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Otevření nástroje Azure Cosmos DB Explorer
1. Klikněte pravým tlačítkem na uzel projektu a vyberte **Otevřít Cosmos DB Explorer**.

    ![Snímek obrazovky s pokusem o otevření okna Průzkumníka dat](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. Na stránce **Zvolte účet služby Cosmos DB** klikněte na rozevírací seznam a vyberte jeden účet služby Azure Cosmos DB.

    ![Snímek obrazovky okna připojené služby s přidaným účtem](./media/connected-service/connected-service-open-explorer.png)
3. Klikněte na **Otevřít** a zobrazí se okno Průzkumníka dat.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Vytvoření účtu služby Azure Cosmos DB v nástroji připojené služby
1. Na stránce připojené služby v levé dolní části podokna klikněte na **Vytvořit nový účet služby Cosmos DB** a otevře se stránka **Vytvořit účet služby Cosmos DB**.

    ![Snímek obrazovky otevřeného vstupního okna Vytvořit účet služby Azure Cosmos DB](./media/connected-service/connected-service-click-new-db-account.png)
2. Na stránce **Vytvořit účet služby Cosmos DB** zadejte požadovanou konfiguraci pro tento účet služby Azure Cosmos DB.

    Na stránce **Vytvořit účet služby Cosmos DB** vyplňte pole podle referenčních informací na následujícím snímku obrazovky. 
 
    ![Stránka nové služby Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Kliknutím na **Vytvořit** vytvořte účet.

## <a name="use-data-explorer"></a>Použití Průzkumníka dat

Po otevření Průzkumníka dat můžete provádět následující:
* Vytvářet a odstraňovat databáze
* Vytvářet a odstraňovat kolekce
* Vytvářet, odstraňovat a filtrovat dokumenty
* Vytvářet a odstraňovat uložené procedury
* Vytvářet a odstraňovat triggery a uživatelem definované funkce pro provádění obchodní logiky na straně serveru 

![Stránka nové služby Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Ukázka

Podívejte se na následující video a seznamte se s používáním připojené služby Azure Cosmos DB v sadě Visual Studio: [Použití připojené služby Azure Cosmos DB v sadě Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili následující:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos DB
> * Přidání připojené služby a účtu
> * Otevření nástroje Azure Cosmos DB Explorer
> * Použití Průzkumníka dat

Když teď máte zprovozněné připojené služby se svým účtem služby Azure Cosmos DB, pokračujte na některý z kurzů a začněte s vývojem svého řešení:

* [Vývoj pomocí rozhraní SQL API v .NET](tutorial-develop-sql-api-dotnet.md)
* [Azure Cosmos DB: Úvodní kurz k rozhraní SQL API](sql-api-get-started.md)
* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).

