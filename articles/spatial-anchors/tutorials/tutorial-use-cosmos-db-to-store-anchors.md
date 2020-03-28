---
title: 'Kurz: Sdílení ukotvení s Azure Cosmos DB'
description: V tomto kurzu se dozvíte, jak sdílet identifikátory Azure Spatial Anchors napříč zařízeními Android/iOS v Unity s back-endovou službou a Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615157"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Kurz: Sdílení prostorových ukotvení Azure napříč relacemi a zařízeními s back-endem Azure Cosmos DB

Tento kurz je pokračovánísdílení [Azure prostorové kotvy napříč relacemi a zařízeními.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Provede vás procesem přidání několika dalších funkcí, aby Azure Cosmos DB sloužil jako back-endové úložiště při sdílení prostorových kotev azure napříč relacemi a zařízeními.

![GIF ilustrující trvalost objektu](./media/persistence.gif)

Stojí za zmínku, že i když budete používat Unity a Azure Cosmos DB v tomto kurzu, je to jen proto, aby vám příklad, jak sdílet identifikátory prostorových ukotvení napříč zařízeními. K dosažení stejného cíle můžete sledovat další jazyky a back-endové technologie. Také ASP.NET webová aplikace Core použitá v tomto kurzu vyžaduje sdk .NET Core 2.2. To běží dobře na Web Apps pro Windows, ale to nebude v současné době běží na Web Apps pro Linux.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Přidejte databázi Azure Cosmos do skupiny prostředků, kterou jste vytvořili dříve.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Zkopírujte, `Connection String` protože ho budete potřebovat.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Provádění drobných změn v souborech Služby SharingService

V **Průzkumníku** `SharingService\Startup.cs`řešení otevřete .

Vyhledejte `#define INMEMORY_DEMO` v horní části souboru a okomentovat tento řádek. Uložte soubor.

V **Průzkumníku** `SharingService\appsettings.json`řešení otevřete .

Vyhledejte `StorageConnectionString` vlastnost a nastavte hodnotu na `Connection String` stejnou hodnotu, kterou jste zkopírovali v [kroku vytvořit databázový účet](#create-a-database-account). Uložte soubor.

Službu sdílení můžete znovu publikovat a spustit ukázkovou aplikaci.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unity-20193"></a>Jednota 2019.3

Kvůli zásadním změnám není unity 2019.3 v současné době podporována. Použijte unity 2019.1 nebo 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili Azure Cosmos DB ke sdílení identifikátorů ukotvení napříč zařízeními. Další informace o tom, jak používat Azure Spatial Anchors v nové aplikaci Unity HoloLens, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Spuštění nové aplikace Unity HoloLens](./tutorial-new-unity-hololens-app.md)
