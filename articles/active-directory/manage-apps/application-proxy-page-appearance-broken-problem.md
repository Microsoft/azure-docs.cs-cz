---
title: Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikace | Dokumenty společnosti Microsoft
description: Navádění, když se stránka nezobrazuje správně v aplikaci proxy aplikace, kterou jste integrovali s Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
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
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381425"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikace

Tento článek vám pomůže vyřešit problémy s aplikacemi proxy aplikací služby Azure Active Directory při přechodu na stránku, ale něco na stránce nevypadá správně.

## <a name="overview"></a>Přehled
Když publikujete aplikaci Proxy aplikace, jsou při přístupu k aplikaci přístupné pouze stránky pod kořenem. Pokud se stránka nezobrazuje správně, kořenová interní adresa URL použitá pro aplikaci pravděpodobně postrádá některé prostředky stránky. Chcete-li vyřešit, ujistěte se, že jste publikovali *všechny* prostředky pro stránku jako součást aplikace.

Můžete ověřit, zda chybějící prostředky je problém otevřením nástroje sítě (například Šumař nebo F12 nástroje v aplikaci Internet Explorer/Microsoft Edge), načítání stránky a hledá 404 chyb. To znamená, že stránky, které jsou aktuálně nalezeny, nelze najít a že je nutné je publikovat.

Jako příklad tohoto případu předpokládejme, že jste publikovali aplikaci pro výdaje pomocí interní adresy URL `http://myapps/expenses`, ale aplikace používá šablonu stylů `http://myapps/style.css`. V tomto případě šablona stylů není publikována ve vaší aplikaci, takže načítání aplikace výdaje vyvolat chybu 404 při pokusu o načtení style.css. V tomto příkladu je problém vyřešen publikováním `http://myapp/`aplikace s interní adresou URL .

## <a name="problems-with-publishing-as-one-application"></a>Problémy s publikováním jako jedna aplikace

Pokud není možné publikovat všechny prostředky v rámci stejné aplikace, je třeba publikovat více aplikací a povolit propojení mezi nimi.

Chcete-li tak učinit, doporučujeme použít řešení [vlastní domény.](application-proxy-configure-custom-domain.md) Toto řešení však vyžaduje, abyste certifikát pro vaši doménu vlastnili a aplikace používaly plně kvalifikované názvy domén (FQDN). Další možnosti naleznete v [dokumentaci k odstraňování nefunkčních odkazů](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Další kroky
[Publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-add-on-premises-application.md)
