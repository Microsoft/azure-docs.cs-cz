---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963322"
---
### <a name="launch-the-publish-wizard"></a>Spuštění průvodce publikováním

V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **SharingService** projektu a vyberte **publikovat**.

Průvodce publikováním se spustí automaticky. Výběrem **App Service** > **Publikovat** otevřete dialogové okno **Vytvořit plán App Service**.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

V dialogovém okně **Vytvoření služby App Service** klikněte na **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud už jste přihlášení, vyberte z rozevíracího seznamu požadovaný účet.

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

V **konfigurace plánu hostování** dialogového okna, použijte nastavení v tabulce.

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu služby App Service. |
| Umístění | Západní USA | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

Vyberte **OK**.

### <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

V části **Název aplikace** zadejte jedinečný název aplikace (platné znaky jsou `a-z`, `0-9` a `-`) nebo přijměte automaticky vygenerovaný jedinečný název. Adresa URL webové aplikace je `https://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

Průvodce po dokončení publikuje webovou aplikaci ASP.NET Core do Azure a potom tuto aplikaci spustí ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Název aplikace zadaný v [kroku vytvoření a publikování](#create-and-publish-the-web-app) se použije jako předpona adresy URL ve formátu `https://<app_name>.azurewebsites.net`. Poznamenejte si tuto adresu URL jako se bude dále používat.
