---
title: Synchronizovat obsah z cloudové složky - službě Azure App Service
description: Zjistěte, jak nasadit aplikaci do služby Azure App Service prostřednictvím synchronizace obsahu z cloudové složky.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 65f372196671e95f7d9af7f47011e9ca1f9de316
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551338"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizovat obsah z cloudové složky do služby Azure App Service
V tomto článku se dozvíte, jak synchronizovat obsah, abyste [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z Dropboxu a OneDrive. 

Synchronizace obsahu na vyžádání nasazení využívá služby App Service [modul nasazení Kudu](https://github.com/projectkudu/kudu/wiki). Můžete pracovat kódu aplikace a obsah ve složce určené cloudu a potom kliknutím na tlačítko Synchronizovat do služby App Service. Synchronizace obsahu používá Kudu serveru sestavení. 

## <a name="enable-content-sync-deployment"></a>Povolení synchronizace obsahu nasazení

Povolení synchronizace obsahu, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **Onedrivu** nebo **Dropboxu** > **Authorize**. Postupujte podle výzev ověřování. 

![](media/app-service-deploy-content-sync/choose-source.png)

Stačí jednou autorizovat s OneDrive nebo Dropbox. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu Onedrivu nebo Dropboxu **změnit účet**.

![](media/app-service-deploy-content-sync/continue.png)

V **konfigurovat** stránky, vyberte složku, kterou chcete synchronizovat. Tato složka se vytvoří na následující cestě určené obsahu v Onedrivu nebo Dropboxu. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Až budete hotovi, klikněte na tlačítko **pokračovat**.

V **Souhrn** stránky, zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

## <a name="synchronize-content"></a>Synchronizovat obsah

Pokud chcete synchronizovat obsah ve složce cloudu pomocí služby App Service, přejděte zpět na **Deployment Center** stránky a klikněte na tlačítko **synchronizace**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Kvůli základním rozdílům v rozhraních API **OneDrive pro firmy** není v tuto chvíli nepodporuje. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Zakázání synchronizace obsahu nasazení

Zakázání synchronizace obsahu, přejděte na stránku aplikace služby App Service v [webu Azure portal](https://portal.azure.com).

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **odpojit**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení z místního úložiště Git](deploy-local-git.md)
