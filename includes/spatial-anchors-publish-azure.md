---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174696"
---
### <a name="open-the-publish-wizard"></a>Otevřít Průvodce publikováním

V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **SharingService** a vyberte **publikovat**.

Spustí se Průvodce publikováním. Vyberte **App Service**  >  **publikovat** a otevřete dialogové okno **vytvořit App Service** .

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

V dialogovém okně **vytvořit App Service** vyberte **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud jste už přihlášení, v rozevíracím seznamu vyberte požadovaný účet.

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

V dialogovém okně **Konfigurovat plán hostování** použijte Tato nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu služby App Service. |
| Umístění | USA – západ | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , která určuje funkce hostování. |

Vyberte **OK**.

### <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

Do pole **název aplikace**zadejte jedinečný název aplikace (platné znaky jsou `a-z` , `0-9` a `-` ) nebo přijměte automaticky generovaný jedinečný název. Adresa URL webové aplikace je `https://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

Po dokončení průvodce publikuje ASP.NET Core webovou aplikaci do Azure a pak aplikaci otevře ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Název aplikace, který jste použili v této části, se použije jako předpona adresy URL ve formátu `https://<app_name>.azurewebsites.net` . Tuto adresu URL si poznamenejte, protože ji budete potřebovat.
