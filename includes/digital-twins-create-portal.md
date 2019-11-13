---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/12/2019
ms.custom: include file
ms.openlocfilehash: 4ed5be09d952d4d64c269e3eaf698ad7a74fffdd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014143"
---
1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

1. Vyberte domovskou stránku domů a potom **+ vytvořit prostředek**. 

   [![rozbalte postranní panel domů a potom vyberte + vytvořit prostředek.](./media/create-digital-twins-portal/create-a-resource.png)](./media/create-digital-twins-portal/create-a-resource.png#lightbox)

1. Vyhledejte **digitální vlákna**a vyberte **digitální vlákna**. 

   [![výběry pro vytvoření nové instance digitálního vlákna](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

   Případně vyberte **Internet věcí**a vyberte **digitální vlákna (Preview)** .

1. Výběrem možnosti **Vytvořit** zahájíte proces nasazení.

   [![vytvořit a potvrdit nasazení prostředku](./media/create-digital-twins-portal/create-and-confirm-resource.png)](./media/create-digital-twins-portal/create-and-confirm-resource.png#lightbox)

1. V podokně **Digital Twins** zadejte následující informace:
   * **Název prostředku:** Vytvořte jedinečný název vaší instance služby Digital Twins.
   * **Předplatné:** Vyberte předplatné, které chcete použít k vytvoření této instance služby Digital Twins. 
   * **Skupina prostředků:** Vyberte nebo vytvořte [skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) pro instanci služby Digital Twins.
   * **Umístění:** Vyberte pro zařízení nejbližší umístění.

     [![podokno Digital revláken s zadanými informacemi](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Projděte si informace o digitálním zaznamenání a pak vyberte **vytvořit**. Vytvoření instance digitálního vlákna může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

1. Otevřete podokno **Přehled** vaší instance služby Digital Twins. Poznamenejte si odkaz v části **rozhraní API pro správu**. Adresa URL **pro správu rozhraní API** je naformátovaná takto: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Tato adresa URL vás přesměruje na dokumentaci k rozhraní REST API služby Azure Digital Twins odpovídající vaší instanci. Informace o tom, jak číst a používat tuto dokumentaci k rozhraní API, najdete v článku [Jak používat Swagger pro službu Azure Digital Twins](../articles/digital-twins/how-to-use-swagger.md). Zkopírujte a upravte adresu URL pro **správu rozhraní API pro správu** v tomto formátu: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Vaše aplikace bude tuto upravenou adresu URL používat jako základní adresu URL pro přístup k vaší instanci. Zkopírujte tuto upravenou adresu URL do dočasného souboru. Budete ho potřebovat v další části.

   [Přehled rozhraní API pro správu ![](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)