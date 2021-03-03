---
title: Připojení k Azure Synapse Linku pro Azure Cosmos DB
description: Naučte se připojit databázi Azure Cosmos DB k pracovnímu prostoru Azure synapse s odkazem na Azure synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 5850644aac9c3324d9ffb17817195cbaaa6020ae
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667552"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Připojení k Azure Synapse Linku pro Azure Cosmos DB

Tento článek popisuje, jak získat přístup k databázi Azure Cosmos DB z Azure synapse Analytics studia pomocí Azure synapse Link.

## <a name="prerequisites"></a>Požadavky

Před připojením databáze Azure Cosmos DB k pracovnímu prostoru budete potřebovat:

* Existující databáze Azure Cosmos DB nebo vytvořte nový účet pomocí postupu v části [rychlý Start: Správa účtu Azure Cosmos DB](../../cosmos-db/how-to-manage-database-account.md).
* Stávající pracovní prostor Azure synapse nebo vytvořte nový pracovní prostor podle kroků v části [rychlý Start: vytvoření pracovního prostoru synapse](../quickstart-create-workspace.md).

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Povolit synapse odkaz na účet databáze Azure Cosmos DB

Pro spouštění rozsáhlých analýz do Azure Cosmos DB, aniž by to ovlivnilo provozní výkon, doporučujeme, abyste pro Azure Cosmos DB povolili odkaz na synapse. Odkaz synapse přináší schopnost HTAP kontejneru a integrovanou podporu v Azure synapse.

## <a name="go-to-synapse-studio"></a>Přejít na synapse Studio

V pracovním prostoru Azure synapse vyberte **Spustit synapse Studio**. Na domovské stránce synapse Studio vyberte **data**, která vás přesměrují na data Průzkumník objektů.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Připojení databáze Azure Cosmos DB k pracovnímu prostoru Azure synapse

Připojení databáze Azure Cosmos DB se provádí jako propojená služba. Díky propojené službě Azure Cosmos DB můžete procházet a zkoumat data, číst a zapisovat z Apache Spark pro Azure synapse Analytics nebo SQL do Azure Cosmos DB.

Z Průzkumník objektů dat se můžete přímo připojit k databázi Azure Cosmos DB pomocí následujících kroků:

1. Vyberte **+** ikonu s blízkými **daty**.
1. Vyberte **připojit k externím datům**.
1. Vyberte rozhraní API, ke kterému se chcete připojit, například **rozhraní SQL API** nebo **rozhraní API pro MongoDB**.
1. Vyberte **Pokračovat**.
1. Pro pojmenování propojené služby použijte popisný název. Název se zobrazí v datovém Průzkumník objektů a modul runtime Azure synapse ho použije pro připojení k databázi a kontejnerům.
1. Vyberte **název účtu Azure Cosmos DB** a **název databáze**.
1. Volitelné Pokud není zadaná žádná oblast, budou operace Azure synapse runtime směrovány směrem k nejbližší oblasti, kde je analytické úložiště povolené. Můžete také ručně nastavit oblast, kterou mají uživatelé používat pro přístup k Azure Cosmos DB analytickému obchodu. Vyberte **Další vlastnosti připojení** a pak vyberte **Nový**. V části **název vlastnosti** zadejte **PreferredRegions**. Nastavte **hodnotu** na požadovanou oblast, například **WestUS2**. (Mezi slovy a číslem neexistují žádná mezera.)
1. Vyberte **Vytvořit**.

Azure Cosmos DB databáze se zobrazí na kartě **propojení** v části **Azure Cosmos DB** . Pomocí Azure Cosmos DB můžete odlišit kontejner s povoleným HTAP z kontejneru pouze OLTP pomocí následujících ikon:

**OLTP kontejner**:

![Vizualizace, která zobrazuje ikonu kontejneru OLTP.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Kontejner s povoleným HTAP**:

![Vizualizace, která zobrazuje ikonu kontejneru HTAP.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Rychlá interakce s akcemi generovanými kódem

Kliknutím pravým tlačítkem na kontejner zobrazíte seznam gest, která aktivuje prostředí Spark nebo SQL runtime. Zápis do kontejneru se provede prostřednictvím transakčního úložiště Azure Cosmos DB a bude využívat jednotky žádosti.  

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, co je podporováno v rámci Azure synapse a Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Konfigurace privátních koncových bodů pro Azure Cosmos DB analytické úložiště](../cosmos-db/analytical-store-private-endpoints.md)
* [Naučte se dotazovat analytické úložiště pomocí Sparku.](./how-to-query-analytical-store-spark.md)