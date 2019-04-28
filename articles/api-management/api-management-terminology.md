---
title: Terminologie služby Azure API Management | Dokumentace Microsoftu
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074050"
---
# <a name="terminology"></a>Terminologie

Tento článek obsahuje definice podmínek, které jsou specifické pro API Management (APIM).

## <a name="term-definitions"></a>Definice termínu

* **Rozhraní API back-endu** – služba HTTP, která implementuje rozhraní API a jeho operace. 
* **Front-endové rozhraní API**/**API služby APIM** – k rozhraní API služby APIM nehostuje rozhraní API, vytvoří fasády pro vaše rozhraní API dokážeme průčelí podle vašich potřeb bez zásahu back end rozhraní API. Další informace najdete v tématu [Import a publikování rozhraní API](import-and-publish.md).
* **APIM produktu** – obsahuje produkt jedno nebo více rozhraní API a také kvótu využití a podmínky použití. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Další informace najdete v tématu [vytvoření a publikování produktu](api-management-howto-add-products.md).
* **Operace rozhraní API služby APIM** – každé rozhraní API služby APIM představuje sadu operací, které jsou k dispozici pro vývojáře. Každé rozhraní API služby APIM obsahuje odkaz na back-end služba, která implementuje rozhraní API a jeho operace se mapují na operace implementované back-end služby. Další informace najdete v tématu [API napodobení odpovědi](mock-api-responses.md).
* **Verze** – někdy chcete publikovat nové nebo jiné rozhraní API funkce pro některé uživatele, zatímco jiné mají zůstat u rozhraní API, které pro ně v současné době používá. Další informace najdete v tématu [publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md).
* **Revize** – Pokud je vaše rozhraní API, jste připravení přejít a spustí ho využívat vývojáři, je obvykle potřeba starat o provádění změn rozhraní API a zároveň nechcete přerušit volající vašeho rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. Další informace najdete v tématu [použití revizí](api-management-get-started-revise-api.md).
* **Portál pro vývojáře** – vaši zákazníci (vývojáři) by měl používat portál pro vývojáře pro přístup k rozhraní API. Je možné přizpůsobit portál pro vývojáře. Další informace najdete v tématu [přizpůsobení portálu pro vývojáře](api-management-customize-styles.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření instance](get-started-create-service-instance.md)

