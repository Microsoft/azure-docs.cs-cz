---
title: Synchronizovat obsah z cloudové složky do služby Azure App Service
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
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: 3796f5c8956b633a4789baaf31a439746dc96b96
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233758"
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

V nabídce vlevo klikněte na tlačítko **Deployment Center** > **Onedrivu** nebo **Dropboxu** > **odpojit**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení z místního úložiště Git](app-service-deploy-local-git.md)
