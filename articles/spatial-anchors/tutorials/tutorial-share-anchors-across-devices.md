---
title: Kurz – sdílené složky Azure prostorových kotev vztahů mezi relacemi a zařízeními | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak sdílet identifikátory Azure prostorových ukotvení mezi zařízeními s Androidem a iOS v Unity s back-end služby.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff9868dd7347812eb6ef566288ec364bc89b6955
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681538"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Kurz: Sdílené složky Azure prostorových kotev vztahů mezi relacemi a zařízeními

V tomto kurzu se naučíte používat [prostorových kotvy Azure](../overview.md) ukotvení vytvářet během jedné relace a vyhledejte jejich na stejné zařízení nebo na jiný. Tyto stejné kotev vztahů může také umístěny ve více zařízeních na stejném místě a ve stejnou dobu.

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

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasazené webové aplikace ASP.NET Core v Azure a potom konfiguraci a nasazení aplikace Unity. Jste vytvořili prostorových ukotvení s aplikací a pomocí webové aplikace ASP.NET Core je sdílet s jinými zařízeními.

Další informace o tom, jak zlepšit vaše webové aplikace ASP.NET Core tak, aby používal službu Azure Cosmos DB k ukládání vaše sdílené identifikátory prostorových ukotvení, pokračujte k dalšímu kurzu. Azure Cosmos DB vám poskytne stálost k vaší webové aplikace ASP.NET Core. To uděláte tak vám umožní vaší aplikaci vytvářet ukotvení ještě dnes a vraťte dní později budete moci znovu, vyhledávat pomocí identifikátorů ukotvení uložených ve vaší webové aplikaci.

> [!div class="nextstepaction"]
> [Kurz: Použití služby Azure Cosmos DB na Store ukotvení](./tutorial-use-cosmos-db-to-store-anchors.md)
