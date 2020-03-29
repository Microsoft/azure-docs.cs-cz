---
title: Problém s vytvořením aplikace Proxy aplikace | Dokumenty společnosti Microsoft
description: Řešení problémů s vytvářením aplikací proxy aplikací na portálu Azure AD Admin Portal
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825805"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problém s vytvořením aplikace Proxy aplikace 

Níže jsou uvedeny některé z běžných problémů, kterým lidé čelí při vytváření nové aplikace proxy aplikace.

## <a name="recommended-documents"></a>Doporučené dokumenty 

Další informace o vytváření aplikace proxy aplikace prostřednictvím portálu pro správu najdete v tématu [Publikování aplikací pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md).

Pokud dodržujete kroky v tomto dokumentu a při vytváření aplikace došlo k chybě, přečtěte si podrobnosti o chybě, kde naleznete informace a návrhy, jak aplikaci opravit. Většina chybových zpráv obsahuje navrhovanou opravu. 

## <a name="specific-things-to-check"></a>Konkrétní věci ke kontrole

Chcete-li se vyhnout běžným chybám, ověřte:

-   Jste správce s oprávněním k vytvoření aplikace Proxy aplikace

-   Interní adresa URL je jedinečná.

-   Externí adresa URL je jedinečná

-   Adresy URL začínají protokolem http nebo https a končí "/"

-   Adresa URL by měla být název domény a nikoli IP adresa

Při vytváření aplikace by se měla v pravém horním rohu zobrazit chybová zpráva. Můžete také vybrat ikonu oznámení a zobrazit chybové zprávy.

   ![Výzva k oznámení](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Další kroky
[Povolení proxy aplikace v portálu Azure](application-proxy-add-on-premises-application.md)
