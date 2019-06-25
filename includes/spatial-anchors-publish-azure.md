---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174696"
---
### <a name="open-the-publish-wizard"></a>Otevřete Průvodce publikováním

V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **SharingService** projektu a vyberte **publikovat**.

Spustí se Průvodce publikováním. Vyberte **služby App Service** > **publikovat** otevřít **vytvořit službu App Service** dialogové okno.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

V **vytvořit službu App Service** dialogu **přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud už jste přihlášení, vyberte z rozevíracího seznamu účet, který chcete.

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

V **konfigurace plánu hostování** dialogovém okně pomocí těchto nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu služby App Service. |
| Location | Západní USA | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , který určuje funkce hostování. |

Vyberte **OK**.

### <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

V **název aplikace**, zadejte jedinečný název aplikace (platné znaky jsou `a-z`, `0-9`, a `-`), nebo přijměte automaticky vygenerovaný jedinečný název. Adresa URL webové aplikace je `https://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

Po dokončení Průvodce publikuje webovou aplikaci ASP.NET Core do Azure a potom se otevře aplikace ve vašem výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Název aplikace, které jsou použity v tomto scénáři se používá jako předponu adresy URL ve formátu `https://<app_name>.azurewebsites.net`. Poznamenejte si tuto adresu URL vzhledem k tomu, že ho budete potřebovat.
