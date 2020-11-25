---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998429"
---
### <a name="open-the-publish-wizard"></a>Otevřít Průvodce publikováním

V **Průzkumník řešení** klikněte pravým tlačítkem na projekt **SharingService** a pak vyberte **publikovat**.

Spustí se Průvodce publikováním. 

Vyberte **App Service**  >  **publikovat** a otevřete tak podokno **vytvořit App Service** .

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal.

V podokně **vytvořit App Service** vyberte **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud jste už přihlášení, v rozevíracím seznamu vyberte požadovaný účet.

   > [!NOTE]
   > Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
   >

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Pojmenujte skupinu prostředků **myResourceGroup** a pak vyberte **OK**.

### <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](app-service-plan.md)]

Vedle položky **Plán hostování** vyberte **Nový**.

V podokně **Konfigurovat plán hostování** použijte Tato nastavení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| MySharingServicePlan | Název plánu App Service |
| Umístění | USA – západ | Datové centrum, ve kterém je webová aplikace hostovaná |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , která určuje funkce hostování |

Vyberte **OK**.

### <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

Do **název aplikace** zadejte jedinečný název aplikace. Platné znaky jsou a-z, 0-9 a spojovníky (-), nebo přijměte automaticky generovaný jedinečný název. Adresa URL webové aplikace je `https://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

   Po dokončení průvodce publikuje ASP.NET Core webovou aplikaci do Azure a pak aplikaci otevře ve výchozím prohlížeči.

  ![Snímek obrazovky publikované webové aplikace v ASP.NET v Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Název aplikace, který jste použili v této části, se použije jako předpona adresy URL ve formátu `https://<app_name>.azurewebsites.net` . Zkopírujte tuto adresu URL do textového editoru pro pozdější použití.
