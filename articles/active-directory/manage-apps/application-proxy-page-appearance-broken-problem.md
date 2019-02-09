---
title: Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Pokyny, když stránka se nezobrazuje správně v aplikačního proxy serveru mají integrované s Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: d579ca6e839d4633e64a9c4775d502cb221c3fd9
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964113"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikací

Tento článek pomůže při odstraňování problémů s aplikacemi Azure Active Directory Application Proxy, když přejdete na stránku, ale něco na stránce asi nebude správné.

## <a name="overview"></a>Přehled
Když publikujete aplikaci Proxy aplikací, jsou přístupné pouze stránky v kořenovém adresáři, při přístupu k aplikaci. Pokud na stránce se nezobrazuje správně, kořenové interní adresa URL pro aplikaci můžou chybět některé prostředky stránky. Pokud chcete vyřešit, ujistěte se, že jste publikovali *všechny* prostředky pro stránku v rámci vaší aplikace.

Můžete ověřit, zda chybějících prostředků je problém tak, že otevřete sledování vaší sítě (například Fiddler nebo F12 nástroje v Internet Explorer nebo Microsoft Edge), načítání stránky a hledáte chyby 404. Který určuje současné době nelze najít a je potřeba publikovat je.

Jako příklad tento případ, se předpokládá jste publikovali aplikaci výdaje pomocí interní adresy URL http://myapps/expenses, ale aplikace používá šablony stylů http://myapps/style.css. V takovém případě šablony stylů není publikována ve vaší aplikaci, tak načítání aplikace výdaje vyvolat chybu 404 při pokusu o načtení style.css. V tomto příkladu se problém nevyřeší a publikujte aplikace s interní adresa URL http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problémy s publikování jako jedna aplikace

Pokud není možné publikovat všechny prostředky v rámci stejné aplikaci, musíte publikovat více aplikací a povolit propojení mezi nimi.

Proto doporučujeme použít [vlastních domén](application-proxy-configure-custom-domain.md) řešení. Toto řešení vyžaduje však, že vlastní certifikát pro vaši doménu a vaše aplikace používat plně kvalifikované názvy domény (FQDN). Další možnosti najdete v článku [Poradce při potížích s nefunkční odkazy dokumentaci](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Další postup
[Publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md)
