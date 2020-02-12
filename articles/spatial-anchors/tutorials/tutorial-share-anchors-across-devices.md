---
title: 'Kurz: sdílení kotev napříč relacemi a zařízeními'
description: V tomto kurzu se dozvíte, jak sdílet identifikátory prostorové kotvy Azure mezi zařízeními s Androidem/iOS v Unity pomocí back-endové služby.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0029fcf8e9efdea529212a7cca49cc8660c623f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276967"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Kurz: sdílení prostorových kotev Azure napříč relacemi a zařízeními

V tomto kurzu se dozvíte, jak pomocí [prostorových kotev Azure](../overview.md) vytvořit kotvy během jedné relace a pak je najít, na stejném zařízení nebo na jiném. Stejné kotvy můžou být také umístěny na více zařízeních na stejném místě a zároveň.

![Dočasné](./media/persistence.gif)

Prostorové kotvy Azure je služba pro vývojáře napříč platformami, která umožňuje vytvářet hybridní prostředí realit pomocí objektů, které v průběhu času trvale uchovávají jejich umístění v rámci zařízení. Až budete hotovi, budete mít aplikaci, která se dá nasadit na dvě nebo víc zařízení. Prostorové kotvy Azure vytvořené jednou instancí lze sdílet s ostatními.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Nasazení webové aplikace v ASP.NET Core v Azure, která se dá použít ke sdílení kotev, jejich ukládání do paměti po dobu určitou dobu.
> * Pomocí našich šablon rychlý Start můžete nakonfigurovat scénu AzureSpatialAnchorsLocalSharedDemo v rámci ukázky Unity a využít tak výhod webové aplikace sdílení kotev.
> * Nasazení a spuštění do jednoho nebo více zařízení.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Je to všímáte, že i když v tomto kurzu budete používat Unity a webovou aplikaci ASP.NET Core, zobrazí se jenom příklad, jak sdílet identifikátory prostorových kotev Azure napříč ostatními zařízeními. Stejný cíl můžete dosáhnout pomocí dalších jazyků a back-endové technologie. Také webová aplikace ASP.NET Core použitá v tomto kurzu má závislost na sadě .NET Core 2,2 SDK. Při běžném Web Apps Azure (pro Windows) to funguje dobře, ale v tuto chvíli nebude fungovat na Azure Web Apps pro Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Stažení ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Nasazení služby pro sdílení kotev

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

Otevřete Visual Studio a otevřete projekt ve složce `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

Před nasazením služby v VS Code budete muset vytvořit skupinu prostředků a plán App Service.

### <a name="sign-in-to-azure"></a>Přihlášení do Azure

Přejděte na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> a přihlaste se k předplatnému Azure.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Skupinu prostředků pojmenujte **myResourceGroup** a vyberte **OK**.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Vedle položky **Plán hostování** vyberte **Nový**.

V dialogovém okně **Konfigurovat plán hostování** použijte Tato nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu služby App Service. |
| Umístění | USA – západ | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , která určuje funkce hostování. |

Vyberte **OK**.

Otevřete Visual Studio Code a otevřete projekt ve složce `Sharing\SharingServiceSample`. Pomocí <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">tohoto kurzu</a> nasaďte službu sdílení prostřednictvím Visual Studio Code. Můžete postupovat podle kroků uvedených v části otevření v Visual Studio Code. Nevytvářejte další projekt MVC, jak je vysvětleno výše v předchozím kroku, protože již máte projekt, který je třeba nasadit a publikovat – SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Nasazení ukázkové aplikace

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili ASP.NET Core webovou aplikaci v Azure a potom jste nakonfigurovali a nasadili aplikaci Unity. Vytvořili jste prostorové kotvy s aplikací a sdílíte je s ostatními zařízeními pomocí ASP.NET Core webové aplikace.

Můžete vylepšit ASP.NET Core webové aplikace tak, aby používala Azure Cosmos DB k uchování úložiště vašich sdílených prostorových kotev identifikátorů. Přidáním podpory Azure Cosmos DB umožníte, aby webová aplikace ASP.NET Core vytvořila kotvu ještě dnes a později se znovu vrátila dny, aby ji bylo možné znovu vyhledat pomocí identifikátoru kotvy uloženého ve vaší webové aplikaci.

> [!div class="nextstepaction"]
> [Použití služby Azure Cosmo DB k ukládání kotev](./tutorial-use-cosmos-db-to-store-anchors.md)

