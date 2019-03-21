---
title: Kurz – sdílení mezi relacemi a zařízeními pomocí prostorových kotev vztahů Azure a Azure Cosmos DB back-endu | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak sdílet identifikátory prostorových kotvy Azure napříč zařízeními s Androidem a iOS v Unity s back-end služby a služby Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286393"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Kurz: Sdílení mezi relacemi a zařízeními pomocí prostorových kotev vztahů Azure a Azure Cosmos DB back-endu

V tomto kurzu se naučíte používat [prostorových kotvy Azure](../overview.md) na:

- Ukotvení vytvářet během jedné relace a najděte během jiná relace, na stejné zařízení nebo na jiný. Například může být druhý relace na jiný den.
- Vytvoření kotev vztahů, které můžou být umístěné ve více zařízeních na stejném místě a ve stejnou dobu.

![Trvalost ilustrující objektu ve formátu GIF](./media/persistence.gif)

[Azure prostorových kotvy](../overview.md) je služba pro vývojáře pro různé platformy, můžete použít k vytvoření prostředí pro hybridní realitu se objekty, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít aplikaci, která se dají nasadit pro dvě nebo více zařízení. Prostorové kotvy vytvořené jedna instance bude sdílet jejich identifikátorů s ostatními s využitím služby Azure Cosmos DB.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nasazení webové aplikace ASP.NET Core v Azure, který slouží ke sdílení ukotvení, ukládání ve službě Azure Cosmos DB.
> * Nakonfigurujte AzureSpatialAnchorsLocalSharedDemo scény v Unity ukázku z rychlí průvodci Azure využívat webovou aplikaci pro sdílení obsahu ukotvení.
> * Nasazení aplikace do jedné nebo více zařízení a spustíme ji.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Je vhodné poznamenat, že i když je budete v tomto kurzu pomocí Unity a Azure Cosmos DB, je jenom abych vám příklad toho, jak sdílet identifikátory prostorových kotvy napříč zařízeními. Uživatele můžete ostatní jazyky a back endové technologie k dosažení stejného cíle. Webové aplikace ASP.NET Core v tomto kurzu používá také vyžaduje .NET Core 2.2 SDK. Poběží bez problémů webové aplikace pro Windows, ale nebude spuštěna aktuálně ve službě Web Apps pro Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopírovat `Connection String` vzhledem k tomu, že ho budete potřebovat.

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Nasazení ve sdílené službě kotvy

Otevřete projekt v sadě Visual Studio a otevřít `Sharing\SharingServiceSample` složky.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Konfigurace služby pro databázi Azure Cosmos DB

V **Průzkumníka řešení**, otevřete `SharingService\Startup.cs`.

Vyhledejte `#define INMEMORY_DEMO` v horní části souboru a komentáře, které výstup. Uložte soubor.

V **Průzkumníka řešení**, otevřete `SharingService\appsettings.json`.

Vyhledejte `StorageConnectionString` vlastnost a být stejný jako hodnotu nastavte `Connection String` hodnotu, která jste si zkopírovali v [vytvoření databáze účtu krok](#create-a-database-account). Uložte soubor.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste použili službu Azure Cosmos DB sdílet identifikátory ukotvení napříč zařízeními. Další informace o knihovně prostorových kotvy Azure, i nadále naší příručce o tom, jak vytvořit a vyhledejte ukotvení.

> [!div class="nextstepaction"]
> [Vytvoření a vyhledejte ukotvení pomocí prostorových kotev vztahů Azure](../create-locate-anchors-overview.md)
