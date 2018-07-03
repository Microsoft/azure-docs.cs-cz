---
title: Language Understanding (LUIS) oblasti | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento článek obsahuje seznam oblastí LUIS pro web služby LUIS, předplatná Azure a oblastí světa.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: d81fbc03689788066fb9275523a5e96647117c58
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346440"
---
# <a name="regions-and-keys"></a>Oblasti a klíčů

Oblast, ve kterém můžete publikovat aplikaci LUIS odpovídá oblast nebo umístění, ve kterém zadáte na webu Azure Portal, při vytváření klíče koncového bodu Azure LUIS. Pokud jste [publikování aplikace](./luis-how-to-publish-app.md), LUIS automaticky generuje adresu URL koncového bodu pro oblast spojené s klíči. K publikování aplikace LUIS do více než jedné oblasti, budete potřebovat alespoň jeden klíč v jedné oblasti. 

## <a name="luis-website"></a>Služba LUIS webu
Existují tři websites LUIS, podle oblasti. Musí se vytvářet a publikovat ve stejné oblasti. 

|LUIS|Oblast|
|--|--|
|[www.LUIS.ai][www.luis.ai]|USA<br>není Evropa<br>není Austrálie|
|[AU.LUIS.ai][au.luis.ai]|Austrálie|
|[EU.LUIS.ai][eu.luis.ai]|Evropa|


## <a name="publishing-regions"></a>Publikování oblastí

LUIS aplikací vytvořených v https://www.luis.ai lze publikovat na všechny koncové body s výjimkou [Evropské](#publishing-to-europe) a [australské](#publishing-to-australia) oblastech. 

Pro vytváření oblasti aplikace lze publikovat pouze na odpovídající publikovat oblasti. Pokud vaše aplikace je aktuálně v nesprávné oblasti pro vytváření, export aplikace a importujte ho do správné zdrojové oblasti pro publikování oblast. 

 Globální oblast | Oblast pro tvorbu | Publikování a dotazování na oblast   |   Služba LUIS webu | Formát adresy URL koncového bodu   |
|-----|------|------|------|------|
| Asie | Západní USA| Východní Asie     | [www.LUIS.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | Západní USA| Jihovýchodní Asie     | [www.LUIS.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Austrálie](#publishing-to-australia) | Austrálie – východ| Austrálie – východ     |   [AU.LUIS.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Evropa](#publishing-to-europe)| Západní Evropa| Severní Evropa     | [EU.LUIS.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Evropa](#publishing-to-europe) | Západní Evropa| Západní Evropa     | [EU.LUIS.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika | Západní USA | Východ USA      |[www.LUIS.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | Západní USA | Východní USA 2     | [www.LUIS.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | Západní USA | Střed USA – jih     | [www.LUIS.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Severní Amerika | Západní USA | Západní střed USA     |[www.LUIS.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Severní Amerika | Západní USA | Západní USA |  [www.LUIS.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Severní Amerika | Západní USA | Západní USA 2    | [www.LUIS.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Jižní Amerika | Západní USA | Brazílie – jih     | [www.LUIS.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publikování v Evropě

Chcete-li publikovat do všech evropských oblastech, vytvoříte LUIS aplikace na https://eu.luis.ai pouze. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Evropa, LUIS se zobrazí zpráva s upozorněním. Místo toho použijte https://eu.luis.ai. Služba LUIS aplikací vytvořených v [ https://eu.luis.ai ] [ eu.luis.ai] neproběhne Automatická migrace do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="publishing-to-australia"></a>Publikování do Austrálie

Chcete-li publikovat do různých oblastí, vytvoříte LUIS aplikace na https://au.luis.ai pouze. Při pokusu publikovat kdekoli jinde pomocí klíče v oblasti Austrálie, LUIS se zobrazí zpráva s upozorněním. Místo toho použijte https://au.luis.ai. Služba LUIS aplikací vytvořených v [ https://au.luis.ai ] [ au.luis.ai] neproběhne Automatická migrace do jiných oblastí. Exportovat a importovat aplikaci LUIS k její migraci.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Odkaz na předem připravených entit](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai