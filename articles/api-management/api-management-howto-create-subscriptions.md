---
title: Vytváření předplatných v Azure API Management | Microsoft Docs
description: Naučte se vytvářet předplatná v Azure API Management.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "70073530"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Vytváření předplatných ve službě Azure API Management

Když publikujete rozhraní API prostřednictvím Azure API Management, je snadné a běžné zabezpečení přístupu k těmto rozhraním API pomocí klíčů předplatného. Klientské aplikace, které potřebují spotřebovat publikovaná rozhraní API, musí při volání do těchto rozhraní API zahrnovat platný klíč předplatného v požadavcích HTTP. Pokud chcete získat klíč předplatného pro přístup k rozhraním API, vyžaduje se předplatné. Další informace o předplatných najdete [v tématu předplatná v Azure API Management](api-management-subscriptions.md).

Tento článek vás provede kroky pro vytvoření předplatných v Azure Portal.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto článku jsou nezbytné tyto požadavky:

+ [Vytvořte instanci API Management](get-started-create-service-instance.md).
+ Pochopení [předplatných v API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Vytvoření nového předplatného

1. V nabídce na levé straně vyberte **odběry** .
2. Vyberte **přidat odběr**.
3. Zadejte název předplatného a vyberte obor.
4. Volitelně můžete zvolit, zda má být předplatné přidruženo k uživateli.
5. Vyberte **Uložit**.

![Flexibilní odběry](./media/api-management-subscriptions/flexible-subscription.png)

Po vytvoření předplatného jsou k dispozici dva klíče rozhraní API pro přístup k rozhraním API. Jeden klíč je primární a jeden je sekundární. 

## <a name="next-steps"></a>Další kroky
Získat další informace o API Management:

+ Přečtěte si další [Koncepty](api-management-terminology.md) v API Management.
+ Další informace o API Management najdete v našich [kurzech](import-and-publish.md) .
+ Nejčastější dotazy najdete na [stránce s nejčastějšími dotazy](api-management-faq.md) .