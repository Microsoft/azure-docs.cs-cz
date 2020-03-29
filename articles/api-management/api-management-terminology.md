---
title: Terminologie správy rozhraní Azure API | Dokumenty společnosti Microsoft
description: Tento článek obsahuje definice pro termíny, které jsou specifické pro správu rozhraní API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61074050"
---
# <a name="terminology"></a>Terminologie

Tento článek poskytuje definice pro pojmy, které jsou specifické pro správu rozhraní API (APIM).

## <a name="term-definitions"></a>Definice termínů

* **Back-endové rozhraní API** – služba HTTP, která implementuje vaše rozhraní API a jeho operace. 
* **Rozhraní**/**API ROZHRANÍ** API Rozhraní front-end – rozhraní API API nehostuje rozhraní API, vytváří fasády pro rozhraní API, aby bylo možné přizpůsobit fasádu podle vašich potřeb, aniž byste se dotkli rozhraní API back-end. Další informace naleznete v [tématu Import a publikování rozhraní API](import-and-publish.md).
* **Produkt APIM** – produkt obsahuje jedno nebo více rozhraní API, stejně jako kvótu využití a podmínky použití. Můžete zahrnout řadu rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Další informace naleznete v [tématu Vytvoření a publikování produktu](api-management-howto-add-products.md).
* **Operace rozhraní API API** – každé rozhraní API představuje sadu operací, které jsou k dispozici vývojářům. Každé rozhraní APIM API obsahuje odkaz na back-end služby, která implementuje rozhraní API a jeho operace mapovat na operace implementované back-end služby. Další informace naleznete v [tématu Mock API responses](mock-api-responses.md).
* **Verze** – někdy chcete publikovat nové nebo jiné funkce rozhraní API pro některé uživatele, zatímco jiní chtějí zůstat s rozhraním API, které v současné době pracuje pro ně. Další informace naleznete v [tématu Publikování více verzí rozhraní API](api-management-get-started-publish-versions.md).
* **Revize** – Když je vaše rozhraní API připraveno k použití a začne být používáno vývojáři, obvykle je třeba dbát na provádění změn v tomto rozhraní API a současně nenarušovat volající vašeho rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. Další informace naleznete v tématu [Použití revizí](api-management-get-started-revise-api.md).
* **Portál pro vývojáře** – Vaši zákazníci (vývojáři) by měli používat portál pro vývojáře pro přístup k vašim rozhraním API. Portál pro vývojáře lze přizpůsobit. Další informace naleznete [v tématu Přizpůsobení portálu pro vývojáře](api-management-customize-styles.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření instance](get-started-create-service-instance.md)

