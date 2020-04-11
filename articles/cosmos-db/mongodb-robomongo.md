---
title: Připojení k Azure Cosmos DB pomocí Robo 3T
description: Zjistěte, jak se připojit k Azure Cosmos DB pomocí rozhraní API Robo 3T a Azure Cosmos DB pro MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114196"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Použití Robo 3T s rozhraním API služby Azure Cosmos DB pro MongoDB

Chcete-li se připojit k účtu Cosmos pomocí Robo 3T, musíte:

* Stažení a instalace [Robo 3T](https://robomongo.org/)
* Vyřiďte si informace o [připojovacím řetězci](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> V současné době jsou podporovány robo 3T v1.2 a nižší verze s rozhraním API Cosmos DB pro MongoDB.

## <a name="connect-using-robo-3t"></a>Připojení s využitím Robo 3T

Chcete-li přidat svůj účet Cosmos do správce připojení Robo 3T, proveďte následující kroky:

1. Načtěte informace o připojení pro váš účet Cosmos nakonfigurované pomocí MongoDB rozhraní API Služby Azure Cosmos DB pomocí pokynů [zde](connect-mongodb-account.md).

    ![Snímek obrazovky s čepelí připojovacího řetězce](./media/mongodb-robomongo/connectionstringblade.png)
2. Spusťte aplikaci *Robomongo.*

3. Chcete-li spravovat připojení, klepněte na tlačítko připojení v části **Soubor.** Potom klikněte na **vytvořit** v okně **Připojení MongoDB,** které otevře okno **Nastavení připojení.**

4. V okně **Nastavení připojení** zvolte název. Potom vyhledejte **hostitele** a **port** z informací o připojení v kroku 1 a zadejte je do **adresy** a **portu**.

    ![Snímek obrazovky se správou připojení Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. Na kartě **Ověřování** klepněte na tlačítko **Provést ověřování**. Potom zadejte databázi (výchozí je *Správce*), **Uživatelské jméno** a **heslo**.
**Uživatelské jméno** i **heslo** naleznete v informacích o připojení v kroku 1.

    ![Snímek obrazovky karty Ověřování Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na kartě **SSL** zaškrtněte **políčko Použít protokol SSL**a změňte **metodu ověřování** na **certifikát podepsaný svým držitelem**.

    ![Snímek obrazovky s kartou Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Nakonec klepněte na tlačítko **Testovat,** chcete-li ověřit, zda se můžete připojit, a potom **uložit**.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.
