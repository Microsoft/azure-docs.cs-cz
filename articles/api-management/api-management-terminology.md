---
title: Terminologie služby Azure API Management | Microsoft Docs
description: Tento článek poskytuje definice podmínek, které jsou specifické pro API Management.
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
ms.openlocfilehash: 81cf34cacdfe37e25d6b745304ab0879245fd8da
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934590"
---
# <a name="terminology"></a>Terminologie

Tento článek poskytuje definice podmínek, které jsou specifické pro správu rozhraní API (APIM).

## <a name="term-definitions"></a>Definice podmínek

* **Rozhraní API back-end** -služba HTTP, která implementuje rozhraní API a operace. 
* **Rozhraní API front-endu**/**APIM API** -k rozhraní API APIM není hostitelem rozhraní API, vytvoří průčelí pro vaše rozhraní API, aby bylo možné přizpůsobit průčelí za podle vašich potřeb bez zásahu do zadní ukončení rozhraní API. Další informace najdete v tématu [importu a publikovat rozhraní API](import-and-publish.md).
* **Produkt APIM** -produkt obsahuje jeden nebo více rozhraní API a také kvóty využití a podmínky použití. Může obsahovat několik rozhraní API a poskytněte jim pro vývojáře prostřednictvím portálu pro vývojáře. Další informace najdete v tématu [vytvoření a publikování produktu](api-management-howto-add-products.md).
* **Operace rozhraní API APIM** -každý APIM API představuje sadu operací, které jsou k dispozici pro vývojáře. Každé rozhraní API APIM obsahuje odkaz na službu back-end, který implementuje rozhraní API a jeho operace se mapují na operace implementované back-end službu. Další informace najdete v tématu [model rozhraní API odpovědi](mock-api-responses.md).
* **Verze** – někdy, kterou chcete publikovat nové nebo jiné rozhraní API funkce pro některé uživatele, zatímco ostatní chcete přilepit s rozhraním API, která aktuálně funguje pro ně. Další informace najdete v tématu [publikovat více verzí rozhraní API](api-management-get-started-publish-versions.md).
* **Revize** – Pokud je vaše rozhraní API je připravena k přejděte a začne být používají vývojáři, je obvykle potřeba postará v provádění změn pro toto rozhraní API a současně nechcete přerušit volající rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. Další informace najdete v tématu [použít revize](api-management-get-started-revise-api.md).
* **Portál pro vývojáře** -vašim zákazníkům (vývojáři) má použít portál pro vývojáře pro přístup vašich rozhraní API. Portál pro vývojáře můžete přizpůsobit. Další informace najdete v tématu [přizpůsobení portálu pro vývojáře](api-management-customize-styles.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření instance](get-started-create-service-instance.md)

