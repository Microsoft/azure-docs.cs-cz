---
title: Použití Robomongo pro službu Azure Cosmos DB
description: 'Další informace o použití Robomongo pomocí služby Azure Cosmos DB: rozhraní API pro účet MongoDB'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863801"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Použití Robomongo pomocí služby Azure Cosmos DB: rozhraní API pro účet MongoDB
Pro připojení ke službě Azure Cosmos DB: rozhraní API pro účet MongoDB použití Robomongo, musíte:

* Stáhněte a nainstalujte [Robomongo](https://robomongo.org/)
* Mají služby Azure Cosmos DB: rozhraní API pro účet MongoDB [připojovací řetězec](connect-mongodb-account.md) informace

## <a name="connect-using-robomongo"></a>Připojení s využitím Robomongo
Přidání služby Azure Cosmos DB: rozhraní API pro účet MongoDB k připojení Robomongo MongoDB, proveďte následující kroky.

1. Načtení Azure Cosmos DB: rozhraní API pro účet informace o připojení MongoDB pomocí pokynů [tady](connect-mongodb-account.md).

    ![Snímek obrazovky okna připojovací řetězec](./media/mongodb-robomongo/connectionstringblade.png)
2. Spustit *Robomongo.exe*

3. Klikněte na tlačítko připojení v rámci **souboru** ke správě vašeho připojení. Potom klikněte na **vytvořit** v **připojení MongoDB** okno, které se otevře **nastavení připojení** okna.

4. V **nastavení připojení** okna, vyberte název. Potom vyhledejte **hostitele** a **Port** z informací o připojení v kroku 1 a zadejte je do **adresu** a **Port**v uvedeném pořadí.

    ![Snímek obrazovky okna Správa připojení Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. Na **ověřování** klikněte na tlačítko **provádět ověřování**. Zadejte databázi (výchozí hodnota je *správce*), **uživatelské jméno** a **heslo**.
Obě **uživatelské jméno** a **heslo** najdete informace o připojení v kroku 1.

    ![Snímek obrazovky s kartou ověřování Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na **SSL** kartě **protokolu SSL pomocí**, změňte **metodu ověřování** k **certifikát podepsaný svým držitelem**.

    ![Snímek obrazovky s kartou SSL Robomongo](./media/mongodb-robomongo/SSL.png)
7. Nakonec klikněte na tlačítko **testovací** k ověření, že se můžete připojit, pak **Uložit**.

## <a name="next-steps"></a>Další postup
* Prozkoumejte službu Azure Cosmos DB: Rozhraní API pro MongoDB [ukázky](mongodb-samples.md).
