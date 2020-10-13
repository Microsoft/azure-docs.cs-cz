---
title: 'Kurz: sdílení kotev napříč relacemi a zařízeními'
description: V tomto kurzu se dozvíte, jak sdílet identifikátory prostorové kotvy Azure mezi zařízeními s Androidem/iOS v Unity pomocí back-endové služby.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 440d8af17bccaf8d3fcb92f65e5d91ed969aec31
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971318"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Kurz: sdílení prostorových ukotvení napříč relacemi a zařízeními

Prostorové kotvy Azure jsou Vývojářské služby pro různé platformy, pomocí kterých můžete vytvářet hybridní realitu pomocí objektů, které v průběhu času uchovávají jejich umístění v rámci zařízení. 

V tomto kurzu použijete [prostorové kotvy Azure](../overview.md) k vytvoření kotev během jedné relace a pak je vyhledáte na stejném zařízení nebo na jiném. Stejné kotvy mohou být také umístěny na více zařízeních na jednom místě a zároveň ve stejnou dobu.

![Animace znázorňující prostorové kotvy, které jsou vytvořeny pomocí mobilního zařízení a v průběhu dnů používaly jiné zařízení.](./media/persistence.gif)


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasaďte ASP.NET Core webovou aplikaci v Azure, kterou můžete použít ke sdílení kotev a ukládat kotvy do paměti po zadanou dobu.
> * Pomocí našich šablon rychlý Start můžete nakonfigurovat scénu AzureSpatialAnchorsLocalSharedDemo v rámci ukázky Unity a využít tak výhod webové aplikace sdílení kotev.
> * Nasaďte a spusťte kotvy do jednoho nebo více zařízení.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> V tomto kurzu budete používat Unity a ASP.NET Core webovou aplikaci, ale tento postup vám poskytne jenom příklad, jak sdílet identifikátory prostorových kotev Azure napříč ostatními zařízeními. Stejný cíl můžete dosáhnout pomocí dalších jazyků a back-endové technologie.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Stažení ukázkového projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Nasazení služby pro sdílení kotev

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Otevřete Visual Studio a otevřete projekt ve složce *Sharing\SharingServiceSample* .

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Před nasazením služby v Visual Studio Code potřebujete vytvořit skupinu prostředků a plán App Service.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přejít na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>a přihlaste se k předplatnému Azure.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Pojmenujte skupinu prostředků **myResourceGroup**a pak vyberte **OK**.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Vedle položky **Plán hostování** vyberte **Nový**.

V podokně **Konfigurovat plán hostování** použijte Tato nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu App Service |
| Umístění | USA – západ | Datové centrum, ve kterém je webová aplikace hostovaná |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , která určuje funkce hostování |

Vyberte **OK**.

Otevřete Visual Studio Code a pak otevřete projekt ve složce *Sharing\SharingServiceSample* . 

Pokud chcete službu sdílení nasadit prostřednictvím Visual Studio Code, postupujte podle pokynů v tématu <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">publikování ASP.NET Core aplikace do Azure pomocí Visual Studio Code</a>. Začněte v části otevření v Visual Studio Code. Nevytvářejte další projekt ASP.NET, jak je vysvětleno v předchozím kroku, protože již máte projekt k nasazení a publikování: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Nasazení ukázkové aplikace

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili ASP.NET Core webovou aplikaci v Azure a Vy jste nakonfigurovali a nasadili aplikaci Unity. Vytvořili jste prostorové kotvy s aplikací a sdílíte je s ostatními zařízeními pomocí ASP.NET Core webové aplikace.

Můžete vylepšit ASP.NET Core webové aplikace tak, aby používala Azure Cosmos DB k uchování úložiště vašich sdílených prostorových kotev identifikátorů. Přidáním podpory Azure Cosmos DB můžete mít ASP.NET Core webové aplikace vytvořit kotvu ještě dnes. Pak pomocí identifikátoru kotvy, který je uložený ve vaší webové aplikaci, můžete nechat aplikaci vrátit dny později, aby se kotva znovu našli.

> [!div class="nextstepaction"]
> [Použití Azure Cosmos DB k ukládání kotev](./tutorial-use-cosmos-db-to-store-anchors.md)

