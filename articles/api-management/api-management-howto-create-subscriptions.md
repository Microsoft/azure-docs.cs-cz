---
title: Vytváření předplatných ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit předplatná ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073530"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Vytváření předplatných ve službě Azure API Management

Když publikujete rozhraní API prostřednictvím Azure API Management, je snadné a běžné zabezpečit přístup k těmto rozhraním API pomocí klíčů předplatného. Klientské aplikace, které potřebují využívat publikovaná řešení API, musí obsahovat platný klíč předplatného v požadavcích HTTP při volání těchto řešení API. Chcete-li získat klíč předplatného pro přístup k api, je vyžadováno předplatné. Další informace o předplatných najdete [v tématu Předplatná ve správě rozhraní AZURE API](api-management-subscriptions.md).

Tento článek vás provede kroky pro vytváření předplatných na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto článku, předpoklady jsou následující:

+ [Vytvořte instanci správy rozhraní API](get-started-create-service-instance.md).
+ Principy [odběrů ve správě rozhraní API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Vytvoření nového předplatného

1. V nabídce vlevo vyberte **Předplatná.**
2. Vyberte **Přidat předplatné**.
3. Zadejte název předplatného a vyberte obor.
4. Volitelně zvolte, jestli má být předplatné přidruženo k uživateli.
5. Vyberte **Uložit**.

![Flexibilní předplatná](./media/api-management-subscriptions/flexible-subscription.png)

Po vytvoření předplatného jsou k dispozici dva klíče rozhraní API pro přístup k rozhraní API. Jeden klíč je primární a druhý je sekundární. 

## <a name="next-steps"></a>Další kroky
Získejte další informace o správě rozhraní API:

+ Další [koncepty](api-management-terminology.md) se dozvíte ve správě rozhraní API.
+ Další informace o správě rozhraní API najdete v [našich kurzech.](import-and-publish.md)
+ Běžné otázky najdete na [stránce s nejčastějšími](api-management-faq.md) dotazy.