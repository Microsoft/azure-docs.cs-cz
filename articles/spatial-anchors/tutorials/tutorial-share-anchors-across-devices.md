---
title: 'Kurz: Sdílení kotev napříč relacemi a zařízeními'
description: V tomto kurzu se dozvíte, jak sdílet identifikátory Azure Spatial Anchor mezi zařízeními Android/iOS v Unity s back-endovou službou.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615471"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Kurz: Sdílení prostorových ukotvení Azure napříč relacemi a zařízeními

V tomto kurzu se dozvíte, jak pomocí [Azure prostorové kotvy](../overview.md) vytvořit kotvy během jedné relace a pak je vyhledejte, na stejném zařízení nebo na jiném. Stejné kotvy mohou být také umístěny více zařízeními na stejném místě a současně.

![Uchování](./media/persistence.gif)

Azure Spatial Anchors je vývojářská služba napříč platformami, která umožňuje vytvářet prostředí smíšené reality pomocí objektů, které v průběhu času napříč zařízeními přetrvávají. Po dokončení budete mít aplikaci, kterou lze nasadit na dvě nebo více zařízení. Azure Prostorové kotvy vytvořené jednou instancí lze sdílet s ostatními.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nasazení ASP.NET Core Web App v Azure, které lze použít ke sdílení kotvy, jejich ukládání do paměti po dobu trvání.
> * Nakonfigurujte scénu AzureSpatialAnchorsLocalSharedDemo v rámci ukázky Unity z našich rychlých startů, abyste využili výhod webové aplikace Kotvy pro sdílení.
> * Nasaďte a spouštěte na jednom nebo více zařízeních.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Stojí za zmínku, že i když budete používat Unity a ASP.NET Core Web App v tomto kurzu, je pouze ukázat příklad o tom, jak sdílet identifikátory Azure spatial anchor mezi jinými zařízeními. Ke splnění stejného cíle můžete použít jiné jazyky a back-endové technologie. Také ASP.NET Core Web App použitý v tomto kurzu má závislost na .NET Core 2.2 SDK. Funguje dobře na běžných Azure Web Apps (pro Windows), ale v současné době nebude fungovat na Azure Web Apps pro Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Stažení ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Nasazení služby Kotvy sdílení

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Otevřete Visual Studio a otevřete projekt ve `Sharing\SharingServiceSample` složce.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Před nasazením služby v kódu VS budete muset vytvořit skupinu prostředků a plán služby App Service.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přejděte na <a href="https://portal.azure.com/" target="_blank">portál Azure</a> a přihlaste se ke svému předplatnému Azure.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Skupinu prostředků pojmenujte **myResourceGroup** a vyberte **OK**.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Vedle položky **Plán hostování** vyberte **Nový**.

V dialogovém okně **Konfigurovat hostitelský plán** použijte tato nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu služby App Service. |
| Umístění | USA – západ | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň,](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) která určuje funkce hostování. |

Vyberte **OK**.

Otevřete kód sady Visual Studio `Sharing\SharingServiceSample` a otevřete projekt ve složce. Postupujte <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">podle tohoto kurzu</a> k nasazení služby sdílení prostřednictvím kódu Sady Visual Studio. Můžete postupovat podle kroků od části "Otevřít pomocí kódu sady Visual Studio". Nevytvářejte další projekt mvc, jak je vysvětleno v kroku výše, jak již máte projekt, který je třeba nasadit a publikovat- SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Nasazení ukázkové aplikace

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unity-20193"></a>Jednota 2019.3

Kvůli zásadním změnám není unity 2019.3 v současné době podporována. Použijte unity 2019.1 nebo 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili ASP.NET Core Web App v Azure a pak nakonfigurovali a nasadili unity app. Vytvořili jste prostorové kotvy s aplikací a sdíleli je s jinými zařízeními pomocí ASP.NET Core Web App.

Můžete vylepšit ASP.NET Core Web App tak, aby pomocí Azure Cosmos DB zachovat úložiště sdílené identifikátory prostorové kotvy. Přidání podpory Azure Cosmos DB umožní vaší ASP.NET Core Web App vytvořit kotvu dnes a vrátit se o několik dní později, aby bylo možné ji znovu najít pomocí identifikátoru kotvy uloženého ve vaší webové aplikaci.

> [!div class="nextstepaction"]
> [Použití Azure Cosmo DB k ukládání kotev](./tutorial-use-cosmos-db-to-store-anchors.md)

