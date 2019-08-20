---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 87599c6e0c4bd4313eac154b4d07110cbe4389a0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622934"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. Vyhledejte **digitální vlákna**a vyberte **digitální vlákna**. Vyberte **vytvořit** a zahajte proces nasazení.

   ![Výběry pro vytvoření nové instance digitálního vlákna](./media/create-digital-twins-portal/create-digital-twins.png)

1. V podokně **Digital Twins** zadejte následující informace:
   * **Název prostředku**: Vytvořte jedinečný název instance digitálního vlákna.
   * **Předplatné**: Vyberte předplatné, které chcete použít k vytvoření této instance digitálního vlákna. 
   * **Skupina prostředků**: Vyberte nebo vytvořte [skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) pro instanci digitálních vláken.
   * **Umístění**: Vyberte nejbližší umístění zařízení.

     ![Podokno digitální vlákna s zadanými informacemi](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Projděte si informace o digitálním zaznamenání a pak vyberte **vytvořit**. Vytvoření instance digitálního vlákna může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

1. Otevřete podokno **Přehled** vaší instance služby Digital Twins. Poznamenejte si odkaz v části **rozhraní API pro správu**.

   Adresa URL **pro správu rozhraní API** je `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`formátována jako. Tato adresa URL vás přesměruje na dokumentaci k rozhraní REST API služby Azure Digital Twins odpovídající vaší instanci. Informace o tom, jak číst a používat tuto dokumentaci k rozhraní API, najdete v článku [Jak používat Swagger pro službu Azure Digital Twins](../articles/digital-twins/how-to-use-swagger.md).

    Změňte adresu URL pro **správu rozhraní API** na `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`tento formát. Vaše aplikace bude tuto upravenou adresu URL používat jako základní adresu URL pro přístup k vaší instanci. Zkopírujte tuto upravenou adresu URL do dočasného souboru. Budete ho potřebovat v další části.

    ![Rozhraní API pro správu](./media/create-digital-twins-portal/digital-twins-management-api.png)