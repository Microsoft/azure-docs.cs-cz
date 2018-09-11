---
title: Problém s vytvořením aplikace Proxy aplikací | Dokumentace Microsoftu
description: Jak řešit problémy s vytvářením aplikace Proxy aplikací v portálu pro správu Azure AD
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
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 2344d35827cf541f0230f74917be3ae0ea39e074
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356364"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problém s vytvořením aplikace Proxy aplikací 

Níže jsou uvedeny některé běžné problémy, rozpoznávání tváře lidí při vytváření nové aplikace aplikačního proxy serveru.

## <a name="recommended-documents"></a>Doporučené dokumenty 

Další informace o vytváření aplikace Proxy aplikací prostřednictvím portálu pro správu najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-publish-azure-portal.md).

Pokud jsou následující kroky v tomto dokumentu a se zobrazuje chyba vytváření aplikace, podívejte se na informace v podrobnostech o chybě a návrhy k vyřešení aplikace. Většina chybové zprávy zahrnují navrhované opravy. 

## <a name="specific-things-to-check"></a>Konkrétní co je potřeba zkontrolovat

Aby se zabránilo běžné chyby, zkontrolujte:

-   Jste správce s oprávněním k vytvoření aplikace Proxy aplikací

-   Interní adresa URL je jedinečný

-   Externí adresa URL je jedinečný

-   Adresy URL začínat http nebo https a končí "/"

-   Adresa URL by měl být název domény a ne IP adresy

Když vytvoříte aplikaci, by měl v pravém horním rohu zobrazí chybová zpráva. Můžete také vybrat ikonu oznámení naleznete v chybových zprávách.

   ![Oznámení řádku](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Další postup
[Povolení Proxy aplikace na webu Azure Portal](application-proxy-enable.md)
