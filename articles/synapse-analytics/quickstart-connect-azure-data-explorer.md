---
title: 'Rychlý Start: připojení služby Azure Průzkumník dat k pracovnímu prostoru synapse'
description: Jak připojit cluster Azure Průzkumník dat k pracovnímu prostoru synapse pomocí Azure synapse Apache Spark
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 53092f5c49073098f28d2fd06f38391e858b2b9d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946385"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>Připojení k Azure Průzkumník dat pomocí synapse Apache Spark

Tento článek popisuje, jak získat přístup k databázím Azure Průzkumník dat ze synapse studia pomocí Apache Spark synapse. 

## <a name="prerequisites"></a>Požadavky

* [Vytvořte cluster a databázi Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal).
* Existující pracovní prostor synapse nebo vytvořit nový pracovní prostor za tímto [rychlým startem](./quickstart-create-workspace.md) 
* Existující fond Apache Spark synapse nebo vytvořit nový fond po tomto [rychlém](./quickstart-create-apache-spark-pool-portal.md) startu
* [Vytvořte aplikaci Azure AD tím, že zřídíte aplikaci Azure AD.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Udělte vaší aplikaci Azure AD přístup k databázi po [správě oprávnění azure Průzkumník dat Database](/azure/data-explorer/manage-database-permissions) .

## <a name="navigate-to-synapse-studio"></a>Přejít na synapse Studio

V pracovním prostoru synapse vyberte **Spustit synapse Studio**. Na domovské stránce synapse studia vyberte **data**, která vás zajímají do **Průzkumník objektů dat**.

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>Připojení databáze Azure Průzkumník dat k pracovnímu prostoru synapse

Připojení databáze služby Azure Průzkumník dat k pracovnímu prostoru se provádí prostřednictvím propojené služby. Propojená služba Azure Data prozkoumává umožňuje uživatelům procházet a zkoumat data, číst a zapisovat z Apache Spark pro analýzy Azure synapse a spouštět úlohy integrace v kanálu.

V Průzkumník objektů dat postupujte podle těchto kroků a přímo připojte cluster Azure Průzkumník dat:

1. Výběr **+** ikony poblíž dat
2. Vyberte **připojit** k externím datům
3. Vyberte **Azure Průzkumník dat (Kusto)**
5. Vyberte **Pokračovat**.
6. Pojmenujte propojenou službu. Název se zobrazí v Průzkumník objektů a bude používán synapse dobou běhu pro připojení k databázi. Doporučujeme použít popisný název.
7. V předplatném vyberte cluster Azure Data Prozkoumejte nebo zadejte identifikátor URI.
8. Zadejte ID instančního objektu a klíč instančního objektu (zajistěte, aby měl tento instanční objekt přístup k zobrazení v databázi pro operaci čtení a přístup k ingestu pro ingestování dat).
9. Zadejte název databáze služby Azure Průzkumník dat.
10. Klikněte na **Test připojení** a ujistěte se, že máte správná oprávnění.
11. Vyberte **Vytvořit**.

    ![Nová propojená služba](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Volitelné Test Connection neověřuje přístup pro zápis. Ujistěte se, že ID instančního objektu má oprávnění k zápisu do databáze služby Azure Průzkumník dat.

12. Clustery a databáze Azure Průzkumník dat jsou viditelné na kartě  **propojená** v části Azure Průzkumník dat. 

    ![Procházet clustery](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > V aktuální verzi se databázové objekty naplní na základě oprávnění účtu AAD v databázích Azure Průzkumník dat. Když spustíte Apache Spark poznámkových blocích nebo integrační úlohy, použije se přihlašovací údaje ve službě Link (tj. instanční objekt).


## <a name="quickly-interact-with-code-generated-actions"></a>Rychlá interakce s akcemi generovanými kódem

* Když kliknete pravým tlačítkem na databázi nebo tabulku, budete mít k dispozici seznam gest, které aktivují ukázkový Poznámkový blok Spark pro čtení dat, zápis dat a streamování dat do Azure Průzkumník dat. 
    [![Nové ukázkové poznámkové bloky](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* Tady je příklad čtení dat. Připojit Poznámkový blok ke svojí fondu Spark a spustit [ ![ novou buňku pro čtení poznámkového bloku](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE] 
   > První spuštění relace Spark může trvat déle než tři minuty. Další spuštění budou výrazně rychlejší.  


## <a name="limitations"></a>Omezení
Služba Azure Průzkumník dat Connector se v současné době nepodporuje ve spravované virtuální síti Azure synapse.


## <a name="next-steps"></a>Další kroky

* [Ukázka kódu s pokročilými možnostmi](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Konektor Spark pro Azure Průzkumník dat (Kusto)](https://github.com/Azure/azure-kusto-spark)