---
title: Úprava rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí služby API Management (APIM) upravit rozhraní API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6be36493fabce07838991c789e111e918a9a826d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "70072157"
---
# <a name="edit-an-api"></a>Úprava rozhraní API

Kroky v tomto kurzu popisují, jak pomocí služby API Management (APIM) upravit rozhraní API. 

+ Můžete to provést přidáním, odstraněním nebo přejmenováním operací v instanci APIM. 
+ Můžete upravit swagger vašeho rozhraní API.

## <a name="prerequisites"></a>Požadavky

+ [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
+ [Import a publikování vašeho prvního rozhraní API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Úprava rozhraní API ve službě APIM

![Úprava rozhraní API](./media/edit-api/edit-api001.png)

1. Klikněte na kartu **Rozhraní API**.
2. Vyberte některé z rozhraní API, která jste předtím naimportovali.
3. Vyberte kartu **Návrh**.
4. Vyberte operaci, kterou chcete upravit.
5. Pokud chcete operaci přejmenovat, vyberte **tužku** v okně **Front-end**.

## <a name="update-the-swagger"></a>Aktualizace swaggeru

Back-end rozhraní API můžete upravit na webu Azure Portal pomocí následujících kroků:

1. Vyberte **Všechny operace**.
2. Klikněte na tužku v okně **Front-end**.

    ![Úprava rozhraní API](./media/edit-api/edit-api002.png)

    Zobrazí se swagger vašeho rozhraní API.

    ![Úprava rozhraní API](./media/edit-api/edit-api003.png)

3. Aktualizujte swagger.
4. Stiskněte **Uložit**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ukázky zásad APIM](policy-samples.md)
> [transformují a chrání publikované rozhraní API](transform-api.md)