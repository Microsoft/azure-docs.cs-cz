---
title: Kurz – sdílení mezi relacemi a zařízeními pomocí prostorových kotvy Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak sdílet identifikátory Azure prostorových ukotvení mezi zařízeními s Androidem a iOS v Unity s back-end služby.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7d9fe58b7db60513eed81aae628ebd7ca754a53a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901300"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Kurz: Sdílení mezi relacemi a zařízeními pomocí prostorových kotev vztahů Azure

Tomto kurzu se dozvíte, jak používat [prostorových kotvy Azure](../overview.md) na:

1. Ukotvení vytvářet v jedné relaci a vyhledejte je v jiné relaci na stejném nebo jiném zařízení. Třeba na jiný den.
2. Vytvoření kotev vztahů, které můžou být umístěné ve více zařízeních na stejném místě ve stejnou dobu.

![Trvalost](./media/persistence.gif)

Azure prostorových kotvy je služba napříč platformami pro vývojáře, která vám umožní vytvořit prostředí hybridní realita s využitím objektů, které se zachovávají jejich umístění na zařízeních v čase. Jakmile budete hotovi, budete mít aplikaci, která se dají nasadit pro dvě nebo více zařízení. Azure prostorových kotvy vytvořené jednu instanci je možné sdílet s ostatními.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nasazení webové aplikace ASP.NET Core v Azure, který slouží ke sdílení ukotvení, jejich ukládání do paměti pro dobu.
> * Nakonfigurujte AzureSpatialAnchorsLocalSharedDemo scény v Unity ukázku z našich šablon rychlý start využívat webovou aplikaci pro sdílení obsahu ukotvení.
> * Nasazení a spuštění jednoho nebo více zařízením.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Je vhodné vašeho povšimnutí, že i když je budete v tomto kurzu pomocí Unity a webové aplikace ASP.NET Core, je pouze k zobrazení příkladu o tom, jak sdílet identifikátory Azure prostorových ukotvení na jiných zařízeních. Ostatní jazyky a back endové technologie, můžete použít k dosažení stejného cíle. Webové aplikace ASP.NET Core v tomto kurzu používá také obsahuje závislost na rozhraní .NET Core 2.2 SDK. Běží bez problémů v pravidelných Azure Web Apps (pro Windows), ale nebudou fungovat aktuálně ve službě Azure Web Apps pro Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Nasazení služby Sdílení kotvy

Otevřít Visual Studio a otevřete projekt v `Sharing\SharingServiceSample` složky.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="open-the-sample-project-in-unity"></a>Otevřete ukázkový projekt v Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasazené webové aplikace ASP.NET Core v Azure a potom konfiguraci a nasazení aplikace Unity. Jste vytvořili prostorových ukotvení s aplikací a pomocí webové aplikace ASP.NET Core je sdílet s jinými zařízeními.

Další informace o tom, jak zlepšit vaše webové aplikace ASP.NET Core tak, aby používal službu Azure Cosmos DB k ukládání vaše sdílené prostorových kotev vztahů, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Kurz: Použití služby Azure Cosmos DB na Store ukotvení](./tutorial-use-cosmos-db-to-store-anchors.md)
