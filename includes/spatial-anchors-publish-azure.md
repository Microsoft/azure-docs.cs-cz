---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67174696"
---
### <a name="open-the-publish-wizard"></a>Otevření průvodce publikováním

V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **SharingService** a vyberte **publikovat**.

Spustí se Průvodce publikováním. Výběrem**možnosti Publikování** **služby App Service** > otevřete dialogové okno **Vytvořit službu App Service.**

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

V dialogovém okně Vytvořit **službu** App **Service** vyberte Přidat účet a přihlaste se k předplatnému Azure. Pokud jste již přihlášeni, vyberte v rozevíracím seznamu požadovaný účet.

> [!NOTE]
> Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
>

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Skupinu prostředků pojmenujte **myResourceGroup** a vyberte **OK**.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](app-service-plan.md)]

Vedle položky **Plán hostování** vyberte **Nový**.

V dialogovém okně **Konfigurovat hostitelský plán** použijte tato nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu služby App Service. |
| Umístění | USA – západ | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň,](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) která určuje funkce hostování. |

Vyberte **OK**.

### <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

V **části Název aplikace**zadejte jedinečný `a-z`název `0-9`aplikace `-`(platné znaky jsou , a ), nebo přijměte automaticky generovaný jedinečný název. Adresa URL webové aplikace je `https://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

Po dokončení průvodce publikuje ASP.NET webové aplikace Core do Azure a pak ji otevře ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Název aplikace, který jste použili v této části, `https://<app_name>.azurewebsites.net`se používá jako předpona adresy URL ve formátu . Tuto adresu URL si poznamenejte, protože ji budete potřebovat.
