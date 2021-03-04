---
title: Synchronizovat obsah z cloudové složky
description: Naučte se, jak nasadit aplikaci pro Azure App Service přes synchronizaci obsahu z cloudové složky, včetně OneDrivu nebo Dropboxu.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051213"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizovat obsah z cloudové složky do Azure App Service
V tomto článku se dozvíte, jak synchronizovat obsah pro [Azure App Service](./overview.md) z Dropboxu a OneDrivu. 

S přístupem k synchronizaci obsahu je vaše práce s kódem a obsahem vaší aplikace v určené složce cloudu, aby se zajistilo, že je ve stavu připraveno k nasazení, a pak se synchronizace s App Service po kliknutí na tlačítko. 

**OneDrive pro firmy** se v tuto chvíli nepodporuje kvůli základním rozdílům v rozhraních API.

> [!NOTE]
> Stránka **centra pro vývoj (Classic)** v Azure Portal, což je staré prostředí pro nasazení, bude zastaralá v březnu 2021. Tato změna nebude mít vliv na žádná existující nastavení nasazení v aplikaci a můžete pokračovat ve správě nasazení aplikace na stránce **centra nasazení** .

## <a name="enable-content-sync-deployment"></a>Povolit nasazení synchronizace obsahu

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku pro správu vaší aplikace App Service.

1. V nabídce vlevo klikněte na nastavení **centra nasazení**  >  . 

1. Ve **zdroji** vyberte **OneDrive** nebo **Dropbox**.

1. Klikněte na **autorizovat** a postupujte podle výzev k autorizaci. 

    ![Ukazuje, jak autorizovat OneDrive nebo Dropbox v centru nasazení v Azure Portal.](media/app-service-deploy-content-sync/choose-source.png)

    Pro váš účet Azure se jenom dá autorizovat jenom pro OneDrive nebo Dropbox. Pokud chcete autorizovat jiný účet OneDrive nebo Dropbox pro aplikaci, klikněte na **změnit účet**.

1. V části **Složka** vyberte složku, kterou chcete synchronizovat. Tato složka se vytvoří pod následující určenou cestou k obsahu na OneDrivu nebo Dropboxu. 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. Klikněte na **Uložit**.

## <a name="synchronize-content"></a>Synchronizovat obsah

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku pro správu vaší aplikace App Service.

1. V nabídce vlevo klikněte na **centrum nasazení**  >  **znovu nasadit/synchronizovat**. 

    ![Ukazuje, jak synchronizovat složku v cloudu s App Service.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Kliknutím na **OK** potvrďte synchronizaci.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Spusťte synchronizaci spuštěním následujícího příkazu a nahrazením \<group-name> a \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Spusťte synchronizaci spuštěním následujícího příkazu a nahrazením \<group-name> a \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Zakázat nasazení synchronizace obsahu

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku pro správu vaší aplikace App Service.

1. V nabídce vlevo klikněte na nastavení **centrum nasazení**  >    >  **Odpojit**. 

    ![Ukazuje, jak odpojit svou cloudovou složku s vaší aplikací App Service v Azure Portal.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení z místního úložiště Git](deploy-local-git.md)