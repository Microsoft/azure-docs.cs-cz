---
title: Přidání ukládání do mezipaměti ke zlepšení výkonu služby Azure API Management | Dokumentace Microsoftu
description: Naučte se zlepšit latenci, spotřebu šířky pásma a načítání webových služeb při volání služby API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94638729"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Přidání ukládání do mezipaměti ke zlepšení výkonu služby Azure API Management

Pro rozhraní API a operace v API Management lze nakonfigurovat ukládání odpovědí do mezipaměti. Ukládání odpovědí do mezipaměti může významně snížit latenci pro volající rozhraní API a zatížení back-endu pro poskytovatele rozhraní API.

> [!IMPORTANT]
> Integrovaná mezipaměť je nestálá a sdílí se všemi jednotkami ve stejné oblasti ve stejné API Management službě.

Podrobnější informace o ukládání do mezipaměti najdete v popisu [zásad ukládání do mezipaměti API Management](api-management-caching-policies.md) a [vlastního ukládání do mezipaměti Azure API Management](api-management-sample-cache-by-key.md).

![zásady ukládání do mezipaměti](media/api-management-howto-cache/cache-policies.png)

Naučíte se:

> [!div class="checklist"]
> * Přidat ukládání odpovědí do mezipaměti pro vaše rozhraní API
> * Ověřit ukládání do mezipaměti v akci

## <a name="availability"></a>Dostupnost

> [!NOTE]
> Interní mezipaměť není k dispozici v úrovni **spotřeby** služby Azure API Management. Místo toho můžete [použít externí mezipaměť Azure pro Redis](api-management-howto-cache-external.md) .

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

+ [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
+ [Import a publikování rozhraní API](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>Přidání zásad ukládání do mezipaměti

S použitím zásad ukládání do mezipaměti zobrazených v tomto příkladu vrátí první požadavek na operaci **GetSpeakers** odpověď z back-endové služby. Tato odpověď se uloží do mezipaměti, kam bude zadaná podle určených hlaviček a parametrů řetězce dotazu. Následující volání operace (s odpovídající parametry) bude vracet odpověď uloženou v mezipaměti až do okamžiku vypršení doby uložení v mezipaměti.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Přejděte k vaší instanci APIM.
3. Vyberte kartu **Rozhraní API**.
4. V seznamu rozhraní API klikněte na **Demo Conference API**.
5. Vyberte **GetSpeakers**.
6. V horní části obrazovky vyberte kartu **Návrh**.
7. V části **Zpracování na vstupu** klikněte na ikonu **</>**.

    ![editor kódu](media/api-management-howto-cache/code-editor.png)

8. V elementu **inbound** přidejte následující zásadu:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. V elementu **outbound** přidejte následující zásadu:

   ```
   <cache-store duration="20" />
   ```

    **Doba trvání** určuje dobu vypršení uložení odpovědí v mezipaměti. V tomto příkladu je délka intervalu **20** sekund.

> [!TIP]
> Pokud používáte externí mezipaměť, jak je popsáno v tématu [použití externí mezipaměti Azure pro Redis v Azure API Management](api-management-howto-cache-external.md), možná budete chtít zadat `caching-type` atribut zásad ukládání do mezipaměti. Další podrobnosti najdete v tématu [zásady ukládání do mezipaměti API Management](api-management-caching-policies.md) .

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Volání operace a testování ukládání do mezipaměti
Abyste viděli ukládání do mezipaměti v akci, vyvolejte operaci z portálu pro vývojáře.

1. Na webu Azure Portal přejděte k vaší instanci APIM.
2. Vyberte kartu **rozhraní API** .
3. Vyberte rozhraní API, ke kterému jste přidali zásady ukládání do mezipaměti.
4. Vyberte operaci **GetSpeakers**.
5. V pravé horní nabídce klikněte na kartu **Test**.
6. Stiskněte **Odeslat**.

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky
* Další informace o zásadách ukládání do mezipaměti najdete v části [Zásady ukládání do mezipaměti][Caching policies] v článku [Zásady API Managementu][API Management policy reference].
* Informace o ukládání položek do mezipaměti podle klíče pomocí výrazů zásad najdete v článku [Vlastní ukládání do mezipaměti ve službě Azure API Management](api-management-sample-cache-by-key.md).
* Další informace o použití externí mezipaměti Azure pro Redis najdete v tématu [použití externí mezipaměti Azure pro Redis v Azure API Management](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
