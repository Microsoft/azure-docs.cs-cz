---
title: 'Kurz: sdílení kotev pomocí Azure Cosmos DB'
description: V tomto kurzu se dozvíte, jak sdílet identifikátory prostorových kotev Azure v zařízeních s Androidem/iOS v Unity pomocí back-endové služby a Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615157"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Kurz: sdílení prostorových kotev Azure napříč relacemi a zařízeními pomocí back-endu Azure Cosmos DB

Tento kurz je pokračováním v [sdílení prostorových kotev Azure napříč relacemi a zařízeními.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Provede vás procesem přidání několika dalších možností, které Azure Cosmos DB sloužit jako back-end úložiště při sdílení prostorových kotev Azure napříč relacemi a zařízeními.

![OBRÁZEK, který ilustruje trvalost objektů](./media/persistence.gif)

Je potřeba si všimnout, že i když v tomto kurzu budete používat Unity a Azure Cosmos DB, stačí vám poskytnout příklad, jak sdílet identifikátory prostorových kotev napříč zařízeními. Stejný cíl můžete dosáhnout i pro jiné jazyky a back-endové technologie uživatelů. Webová aplikace ASP.NET Core použitá v tomto kurzu také vyžaduje sadu .NET Core 2,2 SDK. Na Web Apps pro Windows běží správně, ale v tuto chvíli se v Web Apps pro Linux nespustí.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Přidejte databázi Azure Cosmos do skupiny prostředků, kterou jste vytvořili dříve.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Zkopírujte `Connection String`, protože ho budete potřebovat.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Udělejte drobné změny v souborech SharingService.

V **Průzkumník řešení**otevřete `SharingService\Startup.cs`.

V horní části souboru vyhledejte `#define INMEMORY_DEMO` a odkomentujte tento řádek. Uložte soubor.

V **Průzkumník řešení**otevřete `SharingService\appsettings.json`.

Vyhledejte vlastnost `StorageConnectionString` a nastavte hodnotu tak, aby byla stejná jako hodnota `Connection String`, kterou jste zkopírovali v [kroku vytvoření databázového účtu](#create-a-database-account). Uložte soubor.

Službu sdílení můžete znovu publikovat a spustit ukázkovou aplikaci.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unity-20193"></a>Unity 2019,3

V důsledku zásadních změn se Unity 2019,3 aktuálně nepodporuje. Použijte prosím Unity 2019,1 nebo 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili Azure Cosmos DB ke sdílení identifikátorů kotvy napříč zařízeními. Pokud se chcete dozvědět víc o tom, jak používat prostorové kotvy Azure v nové aplikaci HoloLens pro Unity, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Spouští se nová aplikace HoloLens pro Unity.](./tutorial-new-unity-hololens-app.md)
