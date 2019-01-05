---
title: Vytvořit odběry ve službě Azure API Management | Dokumentace Microsoftu
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
ms.openlocfilehash: 1393e548c46c23f6b50c1b18a274febb74914ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054489"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Vytvořit odběry ve službě Azure API Management

Při publikování rozhraní API prostřednictvím služby Azure API Management je snadné a společné pro zabezpečený přístup k těmto rozhraním API pomocí klíče předplatného. Klientské aplikace, které potřebují využívat publikovaných rozhraní API musí obsahovat platné předplatné klíč v požadavcích HTTP při provádění volání těchto rozhraní API. Pokud chcete získat klíč předplatného pro přístup k rozhraní API, se vyžaduje předplatné. Další informace o předplatných najdete v tématu [předplatných ve službě Azure API Management](api-management-subscriptions.md).

Tento článek vás provede kroky pro vytvoření předplatných na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

Provést kroky v tomto článku, jsou následující požadavky:

+ [Vytvoření instance API managementu](get-started-create-service-instance.md).
+ Vysvětlení [předplatných ve službě API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Vytvořte nové předplatné

1. Vyberte **předplatná** v nabídce na levé straně.
2. Vyberte **přidat předplatné**.
3. Zadejte název předplatného a vyberte obor.
4. Vyberte **Uložit**.

![Flexibilní předplatná](./media/api-management-subscriptions/flexible-subscription.png)

Po vytvoření předplatného jsou k dispozici dva klíče rozhraní API pro přístup k rozhraní API. Jeden z nich je primární a jedna je sekundární. 

## <a name="next-steps"></a>Další postup
Získejte další informace o službě API Management:

+ Přečtěte si další [koncepty](api-management-terminology.md) ve službě API Management.
+ Postupujte podle našich [kurzy](import-and-publish.md) Další informace o službě API Management.
+ Zkontrolujte naše [stránku s nejčastějšími dotazy](api-management-faq.md) pro běžné dotazy.