---
title: Synchronizace obsahu ze složky cloudu do služby Azure App Service
description: Informace o nasazení aplikace do služby Azure App Service prostřednictvím synchronizace obsahu ze složky cloudu.
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
ms.openlocfilehash: d456ae2ffbd3745ef976ad94219a3f998838066b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850215"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizace obsahu ze složky cloudu do služby Azure App Service
Tento článek ukazuje, jak synchronizovat svůj obsah do [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) z Dropbox a OneDrive. 

Synchronizace obsahu na vyžádání nasazení používá technologii služby App Service [modul nasazení Kudu](https://github.com/projectkudu/kudu/wiki). Můžete pracovat s kódu aplikace a obsah ve složce určené cloudu a potom synchronizovat do služby App Service s klepnutím na tlačítko. Obsahu synchronizace používá server Kudu sestavení. 

## <a name="enable-content-sync-deployment"></a>Povolit nasazení obsahu synchronizace

Chcete-li povolit obsahu synchronizace, přejděte na stránku aplikace služby App Service v [portál Azure](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum nasazení** > **OneDrive** nebo **Dropbox** > **Authorize**. Postupujte podle pokynů autorizace. 

![](media/app-service-deploy-content-sync/choose-source.png)

Potřebujete jenom jednou autorizaci s OneDrive nebo Dropbox. Pokud již máte oprávnění, stačí kliknout na **pokračovat**. Kliknutím můžete změnit oprávnění účtu OneDrive nebo Dropbox **změnit účet**.

![](media/app-service-deploy-content-sync/continue.png)

V **konfigurace** stránky, vyberte složku, kterou chcete synchronizovat. Tato složka se vytvoří v následující cestě určené obsahu v OneDrive nebo Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Po dokončení klikněte na tlačítko **pokračovat**.

V **Souhrn** , zkontrolujte možnosti a klikněte na tlačítko **Dokončit**.

## <a name="synchronize-content"></a>Obsah synchronizují

Pokud chcete synchronizovat obsah ve složce cloudu službou App Service, přejděte zpět na **centrum nasazení** a klikněte na tlačítko **synchronizace**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Kvůli základním rozdílům v rozhraní API **Onedrivu pro firmy** v tuto chvíli není podporován. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Zakázat synchronizaci obsahu nasazení

Chcete-li zakázat obsahu synchronizaci, přejděte na stránku aplikace služby App Service v [portál Azure](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum nasazení** > **OneDrive** nebo **Dropbox** > **odpojení**.

![](media/app-service-deploy-content-sync/disable.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení z místního úložiště Git](app-service-deploy-local-git.md)
