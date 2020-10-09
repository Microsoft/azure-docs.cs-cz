---
title: Pomocí Robo 3T se připojte k Azure Cosmos DB
description: Přečtěte si, jak se připojit k Azure Cosmos DB pomocí rozhraní Azure Cosmos DB 3T API pro MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 657f3cf204411bd51541437f05ff13a3f9b929c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85263037"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Použití Robo 3T s rozhraním API služby Azure Cosmos DB pro MongoDB

Pokud se chcete připojit k účtu Cosmos pomocí Robo 3T, musíte:

* Stažení a instalace [Robo 3T](https://robomongo.org/)
* Informace o [připojovacím řetězci](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> V současné době jsou Robo 3T v 1.2 a nižší verze podporované rozhraním API Cosmos DB pro MongoDB.

## <a name="connect-using-robo-3t"></a>Připojení s využitím Robo 3T

Pokud chcete přidat účet Cosmos do Správce připojení Robo 3T, proveďte následující kroky:

1. Načtěte informace o připojení pro účet Cosmos nakonfigurovaný pomocí rozhraní API služby Azure Cosmos DB MongoDB pomocí pokynů [zde](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Snímek obrazovky okna připojovací řetězec":::
2. Spusťte aplikaci *Robomongo* .

3. Pokud chcete spravovat připojení, klikněte na tlačítko připojení v části **soubor** . Pak v okně **připojení MongoDB** klikněte na **vytvořit** a otevře se okno **nastavení připojení** .

4. V okně **nastavení připojení** vyberte název. Pak vyhledejte **hostitele** a **port** z informací o připojení v kroku 1 a zadejte je do pole **adresa** a **port**(v uvedeném pořadí).

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Snímek obrazovky okna připojovací řetězec":::
5. Na kartě **ověřování** klikněte na **provést ověřování**. Pak zadejte svou databázi (výchozí nastavení je *správce*), **uživatelské jméno** a **heslo**.
**Uživatelské jméno** a **heslo** najdete v informacích o připojení v kroku 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Snímek obrazovky okna připojovací řetězec":::
6. Na kartě **SSL** zaškrtněte **použít protokol SSL**a pak změňte **metodu ověřování** na **certifikát podepsaný svým držitelem**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Snímek obrazovky okna připojovací řetězec":::
7. Nakonec klikněte na **test** , abyste ověřili, že se můžete připojit a pak **Uložit**.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
