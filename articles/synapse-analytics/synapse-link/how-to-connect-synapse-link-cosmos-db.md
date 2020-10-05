---
title: Připojení ke službě Azure synapse Link (Preview) pro Azure Cosmos DB
description: Jak připojit Azure Cosmos DB k pracovnímu prostoru synapse pomocí Azure synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f453c074126e448eb54d432532b9064b3eb3cb35
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91287888"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Připojení ke službě Azure synapse Link (Preview) pro Azure Cosmos DB

Tento článek popisuje, jak získat přístup k databázi Azure Cosmos DB z Azure synapse Analytics studia pomocí Azure synapse Link.

## <a name="prerequisites"></a>Předpoklady

Před připojením databáze Azure Cosmos DB k pracovnímu prostoru budete potřebovat:

> [!IMPORTANT]
> Odkaz na službu Azure synapse pro Azure Cosmos DB je aktuálně podporován pro pracovní prostory, které nemají povolenou spravovanou virtuální síť. 

* Existující databáze Azure Cosmos DB nebo vytvořit nový účet za tímto [rychlým startem](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Existující pracovní prostor synapse nebo vytvořit nový pracovní prostor za tímto [rychlým startem](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Povolit Azure Cosmos DB analytické úložiště

Pro spouštění rozsáhlých analýz do Azure Cosmos DB, aniž by to ovlivnilo provozní výkon, doporučujeme, abyste pro Azure Cosmos DB povolili odkaz na synapse. Odkaz synapse přináší schopnost HTAP kontejneru a integrovanou podporu v Azure synapse.

## <a name="navigate-to-synapse-studio"></a>Přejít na synapse Studio

V pracovním prostoru synapse vyberte **Spustit synapse Studio**. Na domovské stránce synapse Studio vyberte * * data, která vás převezmou na **Průzkumník objektů dat**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Připojení databáze Azure Cosmos DB k pracovnímu prostoru synapse

Připojení databáze Azure Cosmos DB se provádí jako propojená služba. Propojená služba Azure Cosmos DB umožňuje uživatelům procházet a zkoumat data, číst a zapisovat z Apache Spark pro Azure synapse Analytics nebo SQL do Azure Cosmos DB.

Z Průzkumník objektů dat se můžete přímo připojit k databázi Azure Cosmos DB pomocí následujících kroků:

1. Výběr ***+*** ikony poblíž dat
2. Vyberte **připojit k externím datům**
3. Vyberte rozhraní API, ke kterému se chcete připojit: SQL API nebo API pro MongoDB
4. Vyberte ***Pokračovat***.
5. Pojmenujte propojenou službu. Název se zobrazí v Průzkumník objektů a synapse za běhu pro připojení k databázi a kontejnerům. Doporučujeme použít popisný název.
6. Vyberte **název účtu Azure Cosmos DB** a **název databáze** .
7. Volitelné Pokud není zadána žádná oblast, synapse operace za běhu budou směrovány směrem k nejbližší oblasti, kde je analytické úložiště povoleno. Můžete ale ručně nastavit, kterou oblast chcete, aby uživatelé měli přístup k Azure Cosmos DB analytickému obchodu. Vyberte **Další vlastnosti připojení** a pak **Nový**. V části **název vlastnosti**napište ***PreferredRegions*** a nastavte **hodnotu** na požadovanou oblast (například: WestUS2, mezi slovy a číslem není žádné místo).
8. Vyberte ***Vytvořit***.

Azure Cosmos DB databáze jsou viditelné na kartě **propojené** v části Azure Cosmos DB. Pomocí Azure Cosmos DB můžete odlišit kontejner s povoleným HTAP z kontejneru OLTP pouze pomocí následujících ikon:

**OLTP kontejner**:

![Kontejner OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Kontejner s povoleným HTAP**:

![Kontejner HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Rychlá interakce s akcemi generovanými kódem

Kliknutím pravým tlačítkem na kontejner zobrazíte seznam gest, která spustí Spark nebo SQL runtime. Zápis do kontejneru se provede prostřednictvím transakčního úložiště Azure Cosmos DB a bude využívat jednotky žádosti.  

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, co je podporováno mezi synapse a Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Naučte se dotazovat analytické úložiště pomocí Sparku.](./how-to-query-analytical-store-spark.md)