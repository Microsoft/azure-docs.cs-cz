---
title: Nasazení služby Azure API Management do několika oblastmi Azure | Microsoft Docs
description: Zjistěte, jak nasadit instanci služby Azure API Management na několika oblastmi Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ff0101bde54f99f99461d0f042af520b1642d0df
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Postup nasazení instanci služby Azure API Management na několika oblastmi Azure
API Management podporuje nasazení s více oblast, což umožňuje rozhraní API vydavatelů distribuci jedné služby pro správu rozhraní API přes libovolný počet požadovaných oblastech Azure. To pomáhá zkrátit žádosti o latence, jak jej distribuovat geograficky spotřebitelé rozhraní API a také zvyšuje dostupnost služby, pokud jedna oblast přejde do režimu offline. 

Když služby API Management je původně vytvořen, obsahuje pouze jeden [jednotky] [ unit] a se nachází v jedné oblasti Azure, který je určený jako primární oblasti. Prostřednictvím portálu Azure lze snadno přidat další oblasti. Nasazení serveru služby Brána API Management na každou oblast a volání provoz, budou směrovány na nejbližší bránu. Pokud oblast přejde do režimu offline, je automaticky znovu směrovanou k bráně další nejbližší provoz. 

> [!IMPORTANT]
> Nasazení s více oblasti je dostupná v jenom **[Premium] [ Premium]** vrstvy.
> 
> 

## <a name="add-region"> </a>Nasazení do nové oblasti instanci služby API Management
> [!NOTE]
> Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance API Management][Create an API Management service instance].
> 
> 

Na portálu Azure přejděte do **škálování a ceny** stránky pro instanci služby API Management. 

![Karta škálování][api-management-scale-service]

Chcete-li nasadit do nové oblasti, klikněte na **+ přidat oblast** na panelu nástrojů.

![Přidat oblast][api-management-add-region]

V rozevíracím seznamu vyberte umístění a nastavit počet jednotek pro pomocí posuvníku.

![Zadejte jednotky][api-management-select-location-units]

Klikněte na tlačítko **přidat** umístit výběr v tabulce umístění. 

Tento postup opakujte, dokud máte nakonfigurované všechny umístění a klikněte na tlačítko **Uložit** na panelu nástrojů k zahájení procesu nasazení.

## <a name="remove-region"> </a>Odstranění instance služby API Management z umístění

Na portálu Azure přejděte do **škálování a ceny** stránky pro instanci služby API Management. 

![Karta škálování][api-management-scale-service]

Pro umístění, kterou chcete odebrat otevřete kontextu nabídku pomocí **...**  tlačítko na pravém konci v tabulce. Vyberte **odstranit** možnost.

Potvrzení odstranění a klikněte na tlačítko **Uložit** aby se změny projevily.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

