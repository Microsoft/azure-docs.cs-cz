---
title: Problém při vytváření aplikace proxy aplikace | Microsoft Docs
description: Řešení potíží při vytváření aplikací proxy aplikací na portálu pro správu Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0c88b90d0caa2f723921d76b5f9dc036895f14e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258010"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problém při vytváření aplikace proxy aplikací 

Níže jsou uvedeny některé běžné problémy, které uživatelé čelí při vytváření nové aplikace proxy aplikací.

## <a name="recommended-documents"></a>Doporučené dokumenty 

Další informace o vytváření aplikací proxy aplikací prostřednictvím portálu pro správu najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md).

Pokud používáte postup v tomto dokumentu a při vytváření aplikace dojde k chybě, Projděte si podrobnosti o chybě, kde najdete informace a návrhy, jak aplikaci opravit. Většina chybových zpráv obsahuje navrhovanou opravu. 

## <a name="specific-things-to-check"></a>Konkrétní věci ke kontrole

Chcete-li se vyhnout běžným chybám, ověřte:

-   Jste správce s oprávněním k vytvoření aplikace proxy aplikace.

-   Interní adresa URL je jedinečná.

-   Externí adresa URL je jedinečná.

-   Adresy URL začínají na http nebo https a končí znakem "/".

-   Adresa URL by měla být název domény, a ne IP adresa.

Chybová zpráva by se měla zobrazit v pravém horním rohu při vytváření aplikace. Můžete také vybrat ikonu oznámení a zobrazit chybové zprávy.

   ![Výzva k oznámení](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Další kroky
[Povolení proxy aplikace v portálu Azure](application-proxy-add-on-premises-application.md)
