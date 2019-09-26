---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 5b88e3f17c1bbf60d38763f7fb349302ae4a920b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310505"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. Vyhledejte **digitální vlákna**a vyberte **digitální vlákna**. Vyberte **vytvořit** a zahajte proces nasazení.

   [![Výběry pro vytvoření nové instance digitálního vlákna](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. V podokně **Digital Twins** zadejte následující informace:
   * **Název prostředku**: Vytvořte jedinečný název instance digitálního vlákna.
   * **Předplatné:** Vyberte předplatné, které chcete použít k vytvoření této instance digitálního vlákna. 
   * **Skupina prostředků**: Vyberte nebo vytvořte [skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) pro instanci digitálních vláken.
   * **Umístění**: Vyberte nejbližší umístění zařízení.

     [![Podokno digitální vlákna s zadanými informacemi](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Projděte si informace o digitálním zaznamenání a pak vyberte **vytvořit**. Vytvoření instance digitálního vlákna může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

1. Otevřete podokno **Přehled** vaší instance služby Digital Twins. Poznamenejte si odkaz v části **rozhraní API pro správu**.

   Adresa URL **pro správu rozhraní API** je `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`formátována jako. Tato adresa URL vás přesměruje na dokumentaci k rozhraní REST API služby Azure Digital Twins odpovídající vaší instanci. Informace o tom, jak číst a používat tuto dokumentaci k rozhraní API, najdete v článku [Jak používat Swagger pro službu Azure Digital Twins](../articles/digital-twins/how-to-use-swagger.md).

    Změňte adresu URL pro **správu rozhraní API** na `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`tento formát. Vaše aplikace bude tuto upravenou adresu URL používat jako základní adresu URL pro přístup k vaší instanci. Zkopírujte tuto upravenou adresu URL do dočasného souboru. Budete ho potřebovat v další části.

    [![Rozhraní API pro správu](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)