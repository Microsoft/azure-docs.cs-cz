---
title: 'Rychlý Start: připojení Azure Průzkumník dat k pracovnímu prostoru Azure synapse Analytics'
description: Připojte cluster Azure Průzkumník dat k pracovnímu prostoru Azure synapse Analytics pomocí Apache Spark pro Azure synapse Analytics.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172276"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Připojení k Azure Data Exploreru s využitím Apache Sparku pro službu Azure Synapse Analytics

Tento článek popisuje, jak získat přístup k databázi Azure Průzkumník dat z synapse studia pomocí Apache Spark pro Azure synapse Analytics.

## <a name="prerequisites"></a>Předpoklady

* [Vytvořte cluster a databázi Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal).
* Vytvořte si existující pracovní prostor služby Azure synapse Analytics nebo vytvořte nový pracovní prostor podle kroků v části [rychlý Start: vytvoření pracovního prostoru Azure synapse](./quickstart-create-workspace.md).
* Mít existující fond Apache Spark nebo vytvořit nový fond podle kroků v části [rychlý Start: Vytvoření fondu Apache Spark pomocí Azure Portal](./quickstart-create-apache-spark-pool-portal.md).
* [Vytvořte aplikaci Azure Active Directory (Azure AD) zřízením aplikace služby Azure AD](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Pomocí postupu v části [Správa oprávnění k databázi azure Průzkumník dat](/azure/data-explorer/manage-database-permissions)udělte aplikaci Azure AD přístup k vaší databázi.

## <a name="go-to-synapse-studio"></a>Přejít na synapse Studio

V pracovním prostoru Azure synapse vyberte **Spustit synapse Studio**. Na domovské stránce synapse studia vyberte **data** , která chcete přejít na **data Průzkumník objektů**.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Připojení databáze Azure Průzkumník dat k pracovnímu prostoru Azure synapse

Připojení databáze Azure Průzkumník dat k pracovnímu prostoru se provádí prostřednictvím propojené služby. S propojenou službou Azure Průzkumník dat můžete procházet a zkoumat data, číst a zapisovat z Apache Spark pro Azure synapse. V kanálu můžete také spouštět úlohy integrace.

V Průzkumník objektů dat postupujte podle těchto kroků a přímo připojte cluster Azure Průzkumník dat:

1. Vyberte **+** ikonu s blízkými **daty**.
1. Vyberte **připojit** a připojte se k externím datům.
1. Vyberte **Azure Průzkumník dat (Kusto)**.
1. Vyberte **Pokračovat**.
1. Pro pojmenování propojené služby použijte popisný název. Název se zobrazí v datovém Průzkumník objektů a použije se modul runtime Azure Synapse k připojení k databázi.
1. Z vašeho předplatného vyberte cluster Azure Průzkumník dat, nebo zadejte identifikátor URI.
1. Zadejte **ID instančního objektu** a **klíč instančního objektu služby**. Zajistěte, aby měl tento instanční objekt přístup k zobrazení v databázi pro operaci čtení a přístup k ingestu pro ingestování dat.
1. Zadejte název databáze služby Azure Průzkumník dat.
1. Vyberte **Test připojení** , abyste měli jistotu, že máte správná oprávnění.
1. Vyberte **Vytvořit**.

    ![Snímek obrazovky, který zobrazuje novou propojenou službu.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Volitelné **Test připojení** neověřuje přístup pro zápis. Ujistěte se, že ID instančního objektu má oprávnění k zápisu do databáze služby Azure Průzkumník dat.

1. Clustery a clustery Azure Průzkumník dat se zobrazí na kartě **propojená** v části **Azure Průzkumník dat** .

    ![Snímek obrazovky, který ukazuje procházení clusterů.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > V aktuální verzi se objekty databáze naplní na základě oprávnění účtu Azure AD v databázích Azure Průzkumník dat. Když spustíte Apache Spark poznámkových blocích nebo integrační úlohy, použije se přihlašovací údaje ve službě propojení (například instanční objekt).

## <a name="quickly-interact-with-code-generated-actions"></a>Rychlá interakce s akcemi generovanými kódem

Po kliknutí pravým tlačítkem myši na databázi nebo tabulku se zobrazí seznam ukázkových poznámkových bloků Spark. Vyberte možnost čtení, zápisu nebo streamování dat do Azure Průzkumník dat.

[![Snímek obrazovky zobrazující nové ukázkové poznámkové bloky](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Tady je příklad čtení dat. Připojte Poznámkový blok k vašemu fondu Spark a spusťte buňku.

[![Snímek obrazovky, který zobrazuje Nový Poznámkový blok pro čtení.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > První spuštění může trvat delší dobu než tři minuty, než se spustí relace Sparku. Další spuštění budou výrazně rychlejší.

## <a name="limitations"></a>Omezení

Konektor Azure Průzkumník dat se v současnosti nepodporuje u virtuálních sítí spravovaných službou Azure synapse.

## <a name="next-steps"></a>Další kroky

* [Ukázka kódu s pokročilými možnostmi](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Konektor Spark pro Azure Průzkumník dat (Kusto)](https://github.com/Azure/azure-kusto-spark)