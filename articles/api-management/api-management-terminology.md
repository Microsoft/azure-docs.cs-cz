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
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "61074050"
---
# <a name="terminology"></a>Terminologie

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

