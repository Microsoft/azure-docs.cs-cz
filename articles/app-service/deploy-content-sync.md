---
title: Synchronizace obsahu ze složky cloudu
description: Přečtěte si, jak nasadit aplikaci do služby Azure App Service prostřednictvím synchronizace obsahu z cloudové složky, včetně OneDrivu nebo Dropboxu.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482968"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizace obsahu z cloudové složky do služby Azure App Service
V tomto článku se ukazuje, jak synchronizovat obsah se [službou Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z Dropboxu a OneDrivu. 

Nasazení synchronizace obsahu na vyžádání je poháněno modulem pro nasazení služby App Service [Kudu](https://github.com/projectkudu/kudu/wiki). S kódem a obsahem aplikace můžete pracovat v určené složce clouda a pak se synchronizovat se službou App Service kliknutím na tlačítko. Synchronizace obsahu používá server sestavení Kudu. 

## <a name="enable-content-sync-deployment"></a>Povolení nasazení synchronizace obsahu

Pokud chcete povolit synchronizaci obsahu, přejděte na stránku aplikace App Service na [webu Azure Portal](https://portal.azure.com).

V levé nabídce klikněte na **Centrum nasazení** > **OneDrive** nebo **Oprávnění dropboxu** > **Authorize**. Postupujte podle pokynů k autorizaci. 

![](media/app-service-deploy-content-sync/choose-source.png)

Autorizaci s OneDrivem nebo Dropboxem musíte provést pouze jednou. Pokud jste již autorizováni, stačí kliknout na **pokračovat**. Autorizovaný účet OneDrivu nebo Dropboxu můžete změnit kliknutím na **Změnit účet**.

![](media/app-service-deploy-content-sync/continue.png)

Na stránce **Konfigurovat** vyberte složku, kterou chcete synchronizovat. Tato složka se vytvoří pod následující určenou cestou obsahu na OneDrivu nebo Dropboxu. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Po dokončení klepněte na tlačítko **Pokračovat**.

Na stránce **Souhrn** ověřte možnosti a klepněte na tlačítko **Dokončit**.

## <a name="synchronize-content"></a>Synchronizace obsahu

Pokud chcete synchronizovat obsah ve složce cloud pomocí služby App Service, **vraťte** se na stránku Centrum nasazení a klikněte na **Synchronizovat**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Z důvodu základních rozdílů v api **onedrive pro firmy** není podporována v tuto chvíli. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Zakázat nasazení synchronizace obsahu

Pokud chcete zakázat synchronizaci obsahu, přejděte na stránku aplikace App Service na [webu Azure Portal](https://portal.azure.com).

V levé nabídce klepněte na **položku Centrum** > nasazení**Odpojit**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení z místního úložiště Git](deploy-local-git.md)
