---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.custom: include file
ms.openlocfilehash: 7b2df437833f270a6e102257693426f4cc65b9d2
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949730"
---
1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. Vyhledejte **digitální vlákna**a vyberte **digitální vlákna**. Vyberte **vytvořit** a zahajte proces nasazení.

   [@no__t – 1Selections pro vytvoření nové instance digitálního vlákna](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

1. V podokně **digitální vlákna** zadejte následující informace:
   * **Název prostředku**: Vytvořte jedinečný název instance digitálního vlákna.
   * **Předplatné**: vyberte předplatné, které chcete použít k vytvoření této instance digitálního vlákna. 
   * **Skupina prostředků**: vyberte nebo vytvořte [skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) pro instanci digitálních vláken.
   * **Umístění**: Vyberte nejbližší umístění pro vaše zařízení.

     [1Digital – podokno s dvojitou čárkou s zadanými informacemi @no__t](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Projděte si informace o digitálním zaznamenání a pak vyberte **vytvořit**. Vytvoření instance digitálního vlákna může trvat několik minut. Průběh můžete sledovat v podokně **oznámení** .

1. Otevřete podokno **přehledu** instance digitálního vlákna. Poznamenejte si odkaz v části **rozhraní API pro správu**.

   Adresa URL **pro správu rozhraní API** je naformátovaná jako `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger`. Tato adresa URL vás přesměruje na digitální vlákna Azure REST API dokumentaci, která se vztahuje na vaši instanci. Přečtěte si, jak [používat službu Azure Digital](../articles/digital-twins/how-to-use-swagger.md) props Swagger k získání informací o tom, jak tuto dokumentaci k rozhraní API přečíst a používat.

    Upravte adresu URL pro **správu rozhraní API** na tento formát `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Vaše aplikace použije upravenou adresu URL jako základní adresu URL pro přístup k vaší instanci. Zkopírujte tuto upravenou adresu URL do dočasného souboru. Budete ho potřebovat v další části.

    [@no__t – rozhraní API pro 1Management](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)