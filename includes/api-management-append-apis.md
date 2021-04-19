---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601918"
---
## <a name="append-other-apis"></a>Připojení dalších rozhraní API

Můžete vytvořit rozhraní API rozhraní API vystavené různými službami, včetně:
* Specifikace OpenAPI
* ROZHRANÍ SOAP API
* Funkce API Apps Azure App Service
* Function App Azure
* Azure Logic Apps
* Azure Service Fabric

Pomocí následujících kroků přidejte k vašemu existujícímu rozhraní API jiné rozhraní API. 

>[!NOTE] 
> Při importu dalšího rozhraní API se k aktuálnímu rozhraní API připojí příslušné operace.

1. Na webu Azure Portal přejděte ke své instanci služby Azure API Management.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Přejít do instance Azure API pro správu":::

1. V nabídce na levé straně vyberte **Rozhraní API**.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="Vybrat rozhraní API":::

1. Klikněte na **…** vedle rozhraní API, ke kterému chcete připojit jiné rozhraní API.
1. V rozevírací nabídce vyberte **Importovat**.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Vybrat Import":::

1. Vyberte službu, ze které se má importovat rozhraní API.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Vybrat službu":::