---
title: Synchronizovat obsah z cloudové složky – Azure App Service
description: Naučte se, jak nasadit aplikaci pro Azure App Service přes synchronizaci obsahu z cloudové složky.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 033a9adfd81dc52858ab58e3170e69af6f56c323
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070626"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizovat obsah z cloudové složky do Azure App Service
V tomto článku se dozvíte, jak synchronizovat obsah pro [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z Dropboxu a OneDrivu. 

Nasazení synchronizace obsahu na vyžádání využívá [modul nasazení App Service Kudu](https://github.com/projectkudu/kudu/wiki). Můžete pracovat s kódem a obsahem vaší aplikace v určené složce cloudu a pak je synchronizovat s App Service pomocí kliknutí na tlačítko. Synchronizace obsahu používá server sestavení Kudu. 

## <a name="enable-content-sync-deployment"></a>Povolit nasazení synchronizace obsahu

Pokud chcete povolit synchronizaci obsahu, přejděte na stránku aplikace App Service v [Azure Portal](https://portal.azure.com).

V levé nabídce klikněte na **nasazení centra** > nasazení**OneDrive** nebo **Dropbox** > **autorizovat**. Postupujte podle výzev k autorizaci. 

![](media/app-service-deploy-content-sync/choose-source.png)

K OneDrivu nebo Dropboxu se stačí autorizovat jenom jednou. Pokud jste již autorizováni, stačí kliknout na tlačítko **pokračovat**. Autorizovaný účet OneDrive nebo Dropbox můžete změnit kliknutím na **změnit účet**.

![](media/app-service-deploy-content-sync/continue.png)

Na stránce **Konfigurace** vyberte složku, kterou chcete synchronizovat. Tato složka se vytvoří pod následující určenou cestou k obsahu na OneDrivu nebo Dropboxu. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Po dokončení klikněte na **pokračovat**.

Na stránce **Souhrn** ověřte své možnosti a klikněte na **Dokončit**.

## <a name="synchronize-content"></a>Synchronizovat obsah

Pokud chcete synchronizovat obsah ve složce cloudu pomocí App Service, vraťte se na stránku **centra nasazení** a klikněte na **synchronizovat**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > **OneDrive pro firmy** se v tuto chvíli nepodporuje kvůli základním rozdílům v rozhraních API. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Zakázat nasazení synchronizace obsahu

Pokud chcete zakázat synchronizaci obsahu, přejděte na stránku aplikace App Service v [Azure Portal](https://portal.azure.com).

V nabídce vlevo klikněte na **centrum** > nasazení**Odpojit**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení z místního úložiště Git](deploy-local-git.md)
