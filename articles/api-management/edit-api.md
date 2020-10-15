---
title: Úprava rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: Naučte se používat API Management (APIM) pro úpravu rozhraní API. Přidejte, odstraňte nebo přejmenujte operace v instanci APIM nebo upravte Swagger rozhraní API.
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
ms.openlocfilehash: d3ce7318266f3fbaf818e5bcfa245ce5f64d74ba
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076925"
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
> [Ukázky](./policy-reference.md) 
>  zásad APIM [Transformace a ochrana publikovaného rozhraní API](transform-api.md)