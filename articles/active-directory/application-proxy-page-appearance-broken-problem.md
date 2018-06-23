---
title: Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikace | Microsoft Docs
description: Pokyny při stránky není správně zobrazení v aplikaci Proxy aplikací mít integrované s Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ee06018cd500937c69824f796c137e3972c55f6c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334696"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikace

Tento článek vám pomůže vyřešit problémy s aplikacemi Azure Proxy aplikace služby Active Directory když přejdete na stránku, ale něco na stránce nevypadá správné.

## <a name="overview"></a>Přehled
Když publikujete aplikaci Proxy aplikace, jsou přístupné pouze stránky v rámci kořenového adresáře, při přístupu k aplikaci. Pokud stránky nezobrazuje správně, interní adresy URL kořenového adresáře používá pro aplikace mohou chybět některé prostředky stránky. Chcete-li vyřešit, ujistěte se, jste publikovali *všechny* prostředky pro stránku v rámci vaší aplikace.

Můžete ověřit, pokud chybí prostředky je problém otevřením sledovací modul vaší sítě (například aplikaci Fiddler nebo F12 nástroje v Internet Explorer nebo Edge), načítání stránky a hledá chyb 404. Který označuje stránky aktuálně nelze nalézt a že je potřeba publikovat je.

Jako příklad tento případ, předpokládá jste publikovali aplikaci výdaje pomocí interní adresa URL http://myapps/expenses, ale aplikace používá šablony stylů http://myapps/style.css. V takovém případě šablony stylů není publikována ve vaší aplikaci, takže chybu 404 načítání aplikace výdaje výjimku při pokusu o načtení style.css. V tomto příkladu je problém vyřešen, tím, že publikujete aplikaci s interní adresa URL http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problémy s publikování jako jednu aplikaci

Pokud není možné publikovat všechny prostředky v rámci stejné aplikaci, musíte k publikování několika aplikací a povolit propojení mezi nimi.

Chcete-li tak učinit, doporučujeme používat [vlastní domény](manage-apps/application-proxy-configure-custom-domain.md) řešení. Toto řešení je však třeba vlastní certifikát pro vaši doménu a aplikace pomocí plně kvalifikované názvy domény (FQDN). Další možnosti najdete v tématu [řešení potíží s poškozených odkazů dokumentaci](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Další postup
[Publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md)
