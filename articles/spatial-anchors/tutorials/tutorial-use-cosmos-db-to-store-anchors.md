---
title: Kurz – sdílení mezi relacemi a zařízeními pomocí prostorových kotev vztahů Azure a back endem služby Azure Cosmos DB | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak sdílet identifikátory Azure prostorových ukotvení mezi zařízeními v Unity s back-end služby a služby Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752131"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Kurz: Sdílení mezi relacemi a zařízeními pomocí prostorových kotev vztahů Azure a back endem služby Azure Cosmos DB

Tomto kurzu se dozvíte, jak používat [prostorových kotvy Azure](../overview.md) na:

1. Ukotvení vytvářet v jedné relaci a vyhledejte je v jiné relaci na stejném nebo jiném zařízení. Třeba na jiný den.
2. Vytvoření kotev vztahů, které můžou být umístěné ve více zařízeních na stejném místě ve stejnou dobu.

![Trvalost](./media/persistence.gif)

[Azure prostorových kotvy](../overview.md) je služba napříč platformami pro vývojáře, která vám umožní vytvořit hybridní realita prostředí s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít aplikaci, která se dají nasadit pro dvě nebo více zařízení. Azure prostorových kotvy vytvořené jednu instanci podělí o jejich identifikátorů ostatním pomocí služby Cosmos DB.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nasazení webové aplikace ASP.NET Core v Azure, který slouží ke sdílení ukotvení, jejich ukládáním do služby Cosmos DB.
> * Nakonfigurujte AzureSpatialAnchorsLocalSharedDemo scény v Unity ukázku z našich šablon rychlý start využívat webovou aplikaci pro sdílení obsahu ukotvení.
> * Nasazení a spuštění jednoho nebo více zařízením.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Je vhodné vašeho povšimnutí, že i když je budete v tomto kurzu pomocí Unity a Azure Cosmos DB, je pouze k zobrazení příkladu o tom, jak sdílet identifikátory Azure prostorových ukotvení na jiných zařízeních. Uživatele můžete ostatní jazyky a back endové technologie k dosažení stejného cíle. Webové aplikace ASP.NET Core v tomto kurzu používá také obsahuje závislost na rozhraní .NET Core 2.2 SDK. Běží bez problémů v pravidelných Azure Web Apps (pro Windows), ale nebudou fungovat aktuálně ve službě Azure Web Apps pro Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Poznamenejte si `Connection String` jak se bude dále používat.

## <a name="deploy-your-sharing-anchors-service"></a>Nasazení služby Sdílení kotvy

Otevřít Visual Studio a otevřete projekt v `Sharing\SharingServiceSample` složky.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Konfigurace služby tak, aby používala Cosmos DB

V **Průzkumníka řešení**, otevřete `SharingService\Startup.cs`.

Vyhledejte `#define INMEMORY_DEMO` řádku v horní části souboru a komentář navýšení kapacity. Uložte soubor.

V **Průzkumníka řešení**, otevřete `SharingService\appsettings.json`.

Vyhledejte `StorageConnectionString` vlastnost a nastavte hodnotu `Connection String` , že jste provedli v si [vytvoření databáze účtu krok](#create-a-database-account). Uložte soubor.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste použili službu Azure Cosmos DB sdílet identifikátory ukotvení napříč zařízeními. Další informace o knihovně prostorových kotvy Azure, i nadále naší příručce o tom, jak vytvořit a vyhledejte ukotvení.

> [!div class="nextstepaction"]
> [Vytvoření a vyhledejte ukotvení pomocí prostorových kotev vztahů Azure](../create-locate-anchors-overview.md)
