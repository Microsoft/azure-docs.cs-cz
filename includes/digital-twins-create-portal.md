---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 12/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: f286676cba3d1333db52fb1125279772d518dd88
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584758"
---
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. Vyhledejte **digital twins** a vyberte **Digital Twins (Preview)**. Vyberte **vytvořit** k zahájení procesu nasazení.

   ![Výběry pro vytvoření nové instance digitální dvojče](./media/create-digital-twins-portal/create-digital-twins.png)

1. V podokně **Digital Twins** zadejte následující informace:
   * **Název prostředku**: Vytvořte jedinečný název pro vaši instanci digitální dvojče.
   * **Předplatné**: Vyberte předplatné, které chcete použít k vytvoření této instance digitální dvojče. 
   * **Skupina prostředků**: Vyberte nebo vytvořte [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) digitální dvojče instance.
   * **Umístění**: Vyberte nejbližší umístění pro vaše zařízení.

    ![Digitální dvojče podokno pomocí zadaných informací](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Zkontrolujte informace o digitální dvojče a pak vyberte **vytvořit**. Vaše digitální dvojče instance může trvat několik minut, který se má vytvořit. Průběh můžete sledovat v podokně **Oznámení**.

1. Otevřete podokno **Přehled** vaší instance služby Digital Twins. Všimněte si, na odkaz v části **rozhraní API pro správu**.

   **Rozhraní API pro správu** adresa URL je formátován jako `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Tato adresa URL vás přesměruje na dokumentaci k rozhraní REST API služby Azure Digital Twins odpovídající vaší instanci. Informace o tom, jak číst a používat tuto dokumentaci k rozhraní API, najdete v článku [Jak používat Swagger pro službu Azure Digital Twins](../articles/digital-twins/how-to-use-swagger.md).

    Upravit **rozhraní API pro správu** adresa URL tento formát `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Vaše aplikace bude tuto upravenou adresu URL používat jako základní adresu URL pro přístup k vaší instanci. Zkopírujte tuto upravenou adresu URL do dočasného souboru. Budete potřebovat v další části.

    ![Rozhraní API pro správu](./media/create-digital-twins-portal/digital-twins-management-api.png)