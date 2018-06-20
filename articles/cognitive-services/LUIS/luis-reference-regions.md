---
title: Jazyk oblasti Principy (LEOŠ) | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje seznam oblastí LEOŠ webu LEOŠ, předplatných Azure a oblasti world.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36236941"
---
# <a name="regions-and-keys"></a>Oblasti a klíče

Oblast, ve kterém můžete publikovat aplikaci LEOŠ odpovídá oblast nebo umístění, které zadáte na portálu Azure při vytváření klíčem Azure LEOŠ koncový bod. Pokud jste [publikovat aplikaci](./PublishApp.md), LEOŠ automaticky generuje adresu URL koncového bodu pro oblast přidružené ke klíči. K publikování aplikace LEOŠ na více než jednu oblast, potřebujete aspoň jeden klíč každou oblast. 

## <a name="luis-website"></a>LEOŠ webu
Existují tři LEOŠ webů, na základě oblasti. Musíte vytvořit a publikovat ve stejné oblasti. 

|LUIS|Oblast|
|--|--|
|[www.LUIS.ai][www.luis.ai]|USA<br>není Evropa<br>není Austrálie|
|[AU.LUIS.ai][au.luis.ai]|Austrálie|
|[EU.LUIS.ai][eu.luis.ai]|Evropa|


## <a name="publishing-regions"></a>Publikování oblastí

LEOŠ aplikace vytvořené na https://www.luis.ai mohou být publikovány pro všechny koncové body s výjimkou [Evropské](#publishing-to-europe) a [Austrálie](#publishing-to-australia) oblasti. 

Vytváření oblastí aplikace lze publikovat pouze na odpovídající publikovat oblast. Pokud je vaše aplikace aktuálně do nesprávné oblasti vytváření exportovat aplikaci a importujte ho na správné pro tvorbu oblast pro vaši oblast publikování. 

 Globální oblast | Oblast pro tvorbu | Publikování & dotazování oblast   |   LEOŠ webu | Formát adresy URL koncového bodu   |
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

## <a name="publishing-to-europe"></a>Publikování Evropa

K publikování do Evropského oblastí, vytvoříte LEOŠ aplikace na https://eu.luis.ai pouze. Pokud se pokusíte publikovat kdekoli v oblasti Evropa použití klíče, LEOŠ zobrazí zprávu s upozorněním. Místo toho použijte https://eu.luis.ai. LEOŠ aplikace vytvořená v [ https://eu.luis.ai ] [ eu.luis.ai] není automaticky migrovat do jiných oblastí. Exportovat a importovat aplikaci LEOŠ k jeho migraci.

## <a name="publishing-to-australia"></a>Publikování do Austrálie

K publikování do australské oblastí, vytvoříte LEOŠ aplikace na https://au.luis.ai pouze. Pokud se pokusíte publikovat kdekoli v oblasti australské použití klíče, LEOŠ zobrazí zprávu s upozorněním. Místo toho použijte https://au.luis.ai. LEOŠ aplikace vytvořená v [ https://au.luis.ai ] [ au.luis.ai] není automaticky migrovat do jiných oblastí. Exportovat a importovat aplikaci LEOŠ k jeho migraci.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Odkaz na předem entity](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai