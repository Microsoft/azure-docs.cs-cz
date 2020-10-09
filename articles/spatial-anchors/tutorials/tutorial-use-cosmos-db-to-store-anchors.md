---
title: 'Kurz: sdílení kotev pomocí Azure Cosmos DB'
description: V tomto kurzu se dozvíte, jak sdílet identifikátory prostorových kotev Azure v zařízeních s Androidem/iOS v Unity pomocí back-endové služby a Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ff431c27dd53e82f9003b658c82ceb3efb5d320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810016"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Kurz: sdílení prostorových kotev Azure napříč relacemi a zařízeními pomocí back-endu Azure Cosmos DB

Tento kurz je pokračováním v [sdílení prostorových kotev Azure napříč relacemi a zařízeními.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Provede vás procesem přidání několika dalších možností, které Azure Cosmos DB sloužit jako back-end úložiště při sdílení prostorových kotev Azure napříč relacemi a zařízeními.

![OBRÁZEK, který ilustruje trvalost objektů](./media/persistence.gif)

Je potřeba si všimnout, že i když v tomto kurzu budete používat Unity a Azure Cosmos DB, stačí vám poskytnout příklad, jak sdílet identifikátory prostorových kotev napříč zařízeními. Stejný cíl můžete dosáhnout i pro jiné jazyky a back-endové technologie uživatelů.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Přidejte databázi Azure Cosmos do skupiny prostředků, kterou jste vytvořili dříve.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Zkopírujte si `Connection String` ho, protože ho budete potřebovat.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Udělejte drobné změny v souborech SharingService.

V **Průzkumník řešení**otevřete `SharingService\Startup.cs` .

Vyhledejte `#define INMEMORY_DEMO` v horní části souboru a odkomentujte tento řádek. Uložte soubor.

V **Průzkumník řešení**otevřete `SharingService\appsettings.json` .

Vyhledejte `StorageConnectionString` vlastnost a nastavte ji jako `Connection String` hodnotu, která je stejná jako hodnota, kterou jste zkopírovali v [kroku vytvoření databázového účtu](#create-a-database-account). Uložte soubor.

Službu sdílení můžete znovu publikovat a spustit ukázkovou aplikaci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili Azure Cosmos DB ke sdílení identifikátorů kotvy napříč zařízeními. Pokud se chcete dozvědět víc o tom, jak používat prostorové kotvy Azure v nové aplikaci HoloLens pro Unity, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Spuštění nové aplikace Unity HoloLens](./tutorial-new-unity-hololens-app.md)
