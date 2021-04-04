---
title: Import specifikace OpenAPI pomocí webu Azure Portal | Microsoft Docs
description: Naučte se importovat specifikaci OpenAPI pomocí API Management a pak otestovat rozhraní API na portálech Azure a vývojářích.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994680"
---
# <a name="import-an-openapi-specification"></a>Import specifikace OpenAPI

Tento článek ukazuje, jak importovat back-endové rozhraní API „specifikace OpenAPI“ nacházející se na stránce https://conferenceapi.azurewebsites.net?format=json. Toto back-endové rozhraní API poskytuje Microsoft a hostuje ho na Azure. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import back-endového rozhraní API „specifikace OpenAPI“
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Import a publikování back-endu rozhraní API

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .
2. Ze seznamu **Přidat nové rozhraní API** vyberte **Specifikace OpenAPI**.

    ![Specifikace OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Zadejte nastavení rozhraní API. Hodnoty můžete nastavit během vytváření nebo je nakonfigurovat později tak, že na kartu **Nastavení** kliknete. Nastavení jsou vysvětlena v kurzu [importu a publikování prvního rozhraní API](import-and-publish.md#import-and-publish-a-backend-api) .
4. Vyberte **Vytvořit**.

> [!NOTE]
> Omezení importu rozhraní API jsou zdokumentována v [jiném článku](api-management-api-import-restrictions.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
