---
title: Problém s vytvořením aplikace Proxy aplikace | Microsoft Docs
description: Řešení potíží při vytváření aplikací pro Proxy aplikace v portálu pro správu Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 97795593ecc83a0b15f018861f5cd9139d26443b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335104"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problém s vytvořením aplikace Proxy aplikace 

V následující tabulce jsou některé běžné problémy, uživatelé setkávají při vytváření nové aplikace proxy aplikace.

## <a name="recommended-documents"></a>Doporučené dokumenty 

Další informace o vytváření aplikací Proxy aplikace prostřednictvím portálu pro správu, najdete v části [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md).

Pokud postupujete podle kroků v tomto dokumentu a dochází k výskytu k chybě při vytváření aplikace, najdete v části Podrobnosti o chybě informace a návrhy, jak opravit aplikaci. Většina chybové zprávy, obsahuje navrhované opravu. 

## <a name="specific-things-to-check"></a>Konkrétní co je potřeba zkontrolovat

Aby se zabránilo běžné chyby, ověřte:

-   Správci s oprávněním k vytvoření aplikace Proxy aplikace

-   Interní adresa URL je jedinečný

-   Externí adresa URL je jedinečný

-   Adresy URL začínat protokolu http nebo https a končit "/"

-   Adresa URL by měla být název domény a ne IP adresy

Chybová zpráva by měla zobrazit v pravém horním rohu při vytváření aplikace. Můžete také vybrat na ikonu oznámení naleznete v chybových zprávách.

   ![Oznámení řádku](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Další postup
[Povolení Proxy aplikace na portálu Azure](manage-apps/application-proxy-enable.md)
