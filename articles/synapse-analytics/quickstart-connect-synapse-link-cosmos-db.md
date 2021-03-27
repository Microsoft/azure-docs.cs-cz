---
title: 'Rychlý Start: připojení ke službě Azure synapse Link pro Azure Cosmos DB'
description: Postup připojení Azure Cosmos DB k pracovnímu prostoru synapse s odkazem synapse
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 7d77431f5caa1a2ac67428326dcd6d4ce75a4a93
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625836"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Rychlý Start: připojení ke službě Azure synapse Link pro Azure Cosmos DB

Tento článek popisuje, jak získat přístup k databázi Azure Cosmos DB z Azure synapse Analytics Studio s odkazem na synapse. 

## <a name="prerequisites"></a>Požadavky

Před připojením účtu Azure Cosmos DB k vašemu pracovnímu prostoru budete potřebovat několik věcí.

* Existující účet Azure Cosmos DB nebo vytvoření nového účtu za tímto [rychlým startem](../cosmos-db/how-to-manage-database-account.md)
* Existující pracovní prostor synapse nebo vytvořit nový pracovní prostor za tímto [rychlým startem](./quickstart-create-workspace.md) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Povolit Azure Cosmos DB analytické úložiště

Pro spouštění rozsáhlých analýz do Azure Cosmos DB, aniž by to ovlivnilo provozní výkon, doporučujeme, abyste pro Azure Cosmos DB povolili odkaz na synapse. Tato funkce přináší do kontejneru možnost HTAP a integrovanou podporu v Azure synapse. Pomocí tohoto rychlého startu povolte synapse odkaz pro kontejnery Cosmos DB.

## <a name="navigate-to-synapse-studio"></a>Přejít na synapse Studio

V pracovním prostoru synapse vyberte **Spustit synapse Studio**. Na domovské stránce synapse Studio vyberte * * data, která vás převezmou na **Průzkumník objektů dat**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Připojení databáze Azure Cosmos DB k pracovnímu prostoru synapse

Připojení databáze Azure Cosmos DB se provádí jako propojená služba. Propojená služba Cosmos DB umožňuje uživatelům procházet a zkoumat data, číst a zapisovat z Apache Spark pro Azure synapse Analytics nebo SQL do Azure Cosmos DB.

Z Průzkumník objektů dat můžete Azure Cosmos DB databázi připojit přímo pomocí následujících kroků:

1. Výběr ***+*** ikony poblíž dat
2. Vyberte **připojit k externím datům**
3. Vyberte rozhraní API, ke kterému se chcete připojit: SQL nebo MongoDB
4. Vyberte ***Pokračovat***.
5. Pojmenujte propojenou službu. Název se zobrazí v Průzkumník objektů a synapse za běhu pro připojení k databázi a kontejnerům. Doporučujeme použít popisný název.
6. Vyberte **název účtu Cosmos DB** a **název databáze** .
7. Volitelné Pokud není zadána žádná oblast, synapse operace za běhu budou směrovány směrem k nejbližší oblasti, kde je analytické úložiště povoleno. Můžete ale ručně nastavit, kterou oblast chcete, aby uživatelé měli přístup k Cosmos DB analytickému obchodu. Vyberte **Další vlastnosti připojení** a pak **Nový**. V části **název vlastnosti** napište **_PreferredRegions_*_ a nastavte hodnotu _*** na požadovanou oblast (například: WestUS2, mezi slovy a čísly není žádné místo).
8. Vyberte ***Vytvořit***.

Azure Cosmos DB databáze jsou viditelné na kartě **propojené** v části Azure Cosmos DB. HTAP s povoleným kontejnerem Azure Cosmos DB lze odlišit z kontejneru OLTP pouze pomocí následujících ikon:

**Synapse kontejner**:

![Kontejner HTAP](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**OLTP kontejner**:

![Kontejner OLTP](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Rychlá interakce s akcemi generovanými kódem

Když kliknete pravým tlačítkem na kontejner, budete mít k dispozici seznam gest, které spustí Spark nebo SQL runtime. Zápis do kontejneru se provede prostřednictvím transakčního úložiště Azure Cosmos DB a bude využívat jednotky žádosti.  

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, co je podporováno mezi synapse a Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Naučte se dotazovat analytické úložiště pomocí Apache Spark pro Azure synapse Analytics.](synapse-link/how-to-query-analytical-store-spark.md)