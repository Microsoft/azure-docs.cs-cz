---
title: Terminologie Azure API Management | Microsoft Docs
description: Tento článek obsahuje definice podmínek, které jsou specifické pro API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 5bc76d2526c5585071a240af36b8a31e3de5708f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87024924"
---
# <a name="azure-api-management-terminology"></a>Terminologie služby Azure API Management

Tento článek obsahuje definice podmínek, které jsou specifické pro API Management (APIM).

## <a name="term-definitions"></a>Definice termínů

* **Back-endové rozhraní API** – služba HTTP, která implementuje vaše rozhraní API a jeho operace. 
* **Front-endové rozhraní API** / **Rozhraní APIM API** – rozhraní API pro APIM nehostuje rozhraní API, vytváří Facade pro vaše rozhraní API, aby bylo možné přizpůsobit fasádu podle vašich potřeb, aniž by se museli dotýkat rozhraní API back-endu. Další informace najdete v tématu [Import a publikování rozhraní API](import-and-publish.md).
* **Produkt APIM** -produkt obsahuje jednu nebo více rozhraní API a také kvótu využití a podmínek použití. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Další informace najdete v tématu [Vytvoření a publikování produktu](api-management-howto-add-products.md).
* **Operace rozhraní API pro APIM** – každé rozhraní APIM API představuje sadu operací, které mají vývojáři k dispozici. Každé rozhraní APIM API obsahuje odkaz na službu back-end, která implementuje rozhraní API, a jeho operace se mapují na operace implementované službou back-end. Další informace najdete v tématu vydaných [odpovědí rozhraní API](mock-api-responses.md).
* **Verze** – někdy chcete některým uživatelům publikovat nové nebo jiné funkce rozhraní API, ale ostatní chtějí využít rozhraní API, které pro ně aktuálně funguje. Další informace najdete v tématu [publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md).
* **Revize** – Jakmile bude vaše rozhraní API připravené k používání vývojáři, obvykle je potřeba vzít v úvahu změny v tomto rozhraní API a zároveň Nerušit volající vašeho rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. Další informace najdete v tématu [použití revizí](api-management-get-started-revise-api.md).
* **Portál pro vývojáře** – vaši zákazníci (vývojáři) by měli používat portál pro vývojáře pro přístup k rozhraním API. Portál pro vývojáře se dá přizpůsobit. Další informace najdete v tématu [přizpůsobení portálu pro vývojáře](api-management-customize-styles.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření instance](get-started-create-service-instance.md)

