---
title: Exportovat rozhraní API z Azure API Management do Power Platform | Microsoft Docs
description: Naučte se exportovat rozhraní API z API Management do Power Platform.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146650"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Export rozhraní API z Azure API Management do Power Platform 

Vývojáři občanů, kteří používají [energetickou platformu](https://powerplatform.microsoft.com) Microsoftu, často potřebují přístup k obchodním funkcím vyvinutým profesionálními vývojáři a nasazenými v Azure. [Azure API Management](https://aka.ms/apimrocks) umožňuje profesionálním vývojářům publikovat svou back-end službu jako rozhraní API a snadno exportovat tato rozhraní API do Power Platform (Power Apps a Power Automate) jako vlastní konektory pro spotřebu od vývojářů občanů. 

Tento článek vás provede kroky pro export rozhraní API z API Management na platformu napájení. 

## <a name="prerequisites"></a>Předpoklady

+ Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
+ Ujistěte se, že ve vaší instanci API Management existuje rozhraní API, které byste chtěli exportovat do Power Platform
+ Ujistěte se, že máte Power apps nebo Power automatizuje [prostředí](/powerapps/powerapps-overview#power-apps-for-admins) . 

## <a name="export-an-api"></a>Exportovat rozhraní API

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .
2. Klikněte na tři tečky vedle rozhraní API, které chcete exportovat. 
3. Vyberte **Exportovat** .
4. Vyberte **Power Apps a Power Automatizujte** .
5. Vyberte prostředí, do kterého se má rozhraní API exportovat. 
6. Zadejte zobrazovaný název, který se použije jako název vlastního konektoru.  
7. Volitelné – Pokud je rozhraní API chráněné serverem OAuth 2,0, budete taky muset zadat další podrobnosti `Client ID` , včetně, `Client secret` ,, `Authorization URL` `Token URL` a `Refresh URL` .  
8. Vyberte **Exportovat** . 

Až se export dokončí, přejděte do Power aplikace nebo na Power Automate prostředí. Rozhraní API se zobrazí jako vlastní konektor.

## <a name="next-steps"></a>Další kroky

* [Další informace o Power platformu](https://powerplatform.microsoft.com/)
* [Seznamte se s běžnými úlohami v API Management pomocí těchto kurzů.](./import-and-publish.md)
