---
title: Načíst aktuální seznam Verizon POP pro Azure CDN | Microsoft Docs
description: Zjistěte, jak načíst aktuální seznam Verizon POP pomocí rozhraní REST API.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: f796d18a03e14fdf652af72366762e1365523f09
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754498"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Načíst aktuální seznam Verizon POP pro Azure CDN

Načíst sadu IP adres pro společnosti Verizon bodů přítomnosti (POP) serverů, můžete použít rozhraní REST API. Tyto servery POP provádět požadavky na počátku servery, které jsou spojeny s koncovými body Azure Content Delivery Network (CDN) pro profil Verizon (**Azure CDN Standard od společnosti Verizon** nebo **Azure CDN Premium od společnosti Verizon**). Všimněte si, že tato sada IP adres se liší od IP adres, který klient se zobrazí při zasílání požadavků do bodů POP. 

Syntaxe operace REST API pro načítání seznamu bodů POP, najdete v části [uzly okraj - seznamu](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Typický případ použití

Z bezpečnostních důvodů můžete pomocí tohoto seznamu IP vynutit, aby pouze z platné POP Verizon jsou vytvářeny požadavky na původním serveru. Například pokud někdo zjistit název hostitele nebo IP adresu pro koncový bod CDN zdrojový server, se jeden Ověřte požadavky přímo na původním serveru, proto obcházení změny velikosti a funkcích zabezpečení poskytované Azure CDN. Nastavením IP adresy ve vráceném seznamu jako jediný povolené IP adresy na původním serveru, je možné zabránit tento scénář. Zajistit, že máte nejnovější seznam POP, jejich načtení alespoň jednou denně. 

## <a name="next-steps"></a>Další postup

Informace o rozhraní REST API najdete v tématu [REST API služby Azure CDN](https://docs.microsoft.com/en-us/rest/api/cdn/).