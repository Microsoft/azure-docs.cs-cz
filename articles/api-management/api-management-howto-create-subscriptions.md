---
title: Jak vytvořit odběry ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit odběry ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621790"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Jak vytvořit odběry ve službě Azure API Management

Při publikování rozhraní API pomocí Azure API Management (APIM), je nejběžnější a nejjednodušší způsob, jak zabezpečit přístup k těmto rozhraním API pomocí klíče předplatného. Jinými slovy klientské aplikace, které potřebují využívat publikovaných rozhraní API musí obsahovat platný klíč předplatného v požadavcích HTTP při volání těchto rozhraních API. Pokud chcete získat klíč předplatného pro přístup k rozhraní API, se vyžaduje předplatné. Další informace o předplatných najdete v tématu [předplatných ve službě Azure API Management](api-management-subscriptions.md)

Tento článek vás provede kroky pro vytvoření předplatných na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku, budete muset:

+ [Vytvoření instance APIM](get-started-create-service-instance.md)
+ Vysvětlení [předplatných služby APIM](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Vytvořte nové předplatné

1. Klikněte na **předplatná** v nabídce na levé straně
2. Klikněte na tlačítko **přidat předplatné**
3. Zadejte název předplatného a výběr rozsahu
4. Klikněte na **Uložit**.

![Flexibilní předplatná](./media/api-management-subscriptions/flexible-subscription.png)

Po vytvoření předplatného dvojici klíčů rozhraní API (primární i sekundární) připravené pro přístup k rozhraní API.

## <a name="next-steps"></a>Další postup
Další informace o službě API Management:

+ Přečtěte si další [koncepty](api-management-terminology.md) ve službě API Management
+ Postupujte podle našich [kurzy](import-and-publish.md) Další informace o službě API Management
+ Zkontrolujte naše [stránku s nejčastějšími dotazy](api-management-faq.md) pro běžné dotazy