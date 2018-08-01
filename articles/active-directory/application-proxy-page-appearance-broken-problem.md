---
title: Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Pokyny, když stránka se nezobrazuje správně v aplikačního proxy serveru mají integrované s Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 118d5780145d0421160c70546f01dc930190185e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365205"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikací

Tento článek pomůže při odstraňování problémů s aplikacemi Azure Active Directory Application Proxy, když přejdete na stránku, ale něco na stránce asi nebude správné.

## <a name="overview"></a>Přehled
Když publikujete aplikaci Proxy aplikací, jsou přístupné pouze stránky v kořenovém adresáři, při přístupu k aplikaci. Pokud na stránce se nezobrazuje správně, kořenové interní adresa URL pro aplikaci můžou chybět některé prostředky stránky. Pokud chcete vyřešit, ujistěte se, že jste publikovali *všechny* prostředky pro stránku v rámci vaší aplikace.

Můžete ověřit, zda chybějících prostředků je problém tak, že otevřete sledování vaší sítě (například Fiddler nebo F12 nástroje v Internet Exploreru/Edge), načítání stránky a hledáte chyby 404. Který určuje současné době nelze najít a je potřeba publikovat je.

Jako příklad tento případ, se předpokládá jste publikovali aplikaci výdaje pomocí interní adresy URL http://myapps/expenses, ale aplikace používá šablony stylů http://myapps/style.css. V takovém případě šablony stylů není publikována ve vaší aplikaci, tak načítání aplikace výdaje vyvolat chybu 404 při pokusu o načtení style.css. V tomto příkladu se problém nevyřeší a publikujte aplikace s interní adresa URL http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problémy s publikování jako jedna aplikace

Pokud není možné publikovat všechny prostředky v rámci stejné aplikaci, musíte publikovat více aplikací a povolit propojení mezi nimi.

Proto doporučujeme použít [vlastních domén](manage-apps/application-proxy-configure-custom-domain.md) řešení. Toto řešení vyžaduje však, že vlastní certifikát pro vaši doménu a vaše aplikace používat plně kvalifikované názvy domény (FQDN). Další možnosti najdete v článku [Poradce při potížích s nefunkční odkazy dokumentaci](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Další postup
[Publikování aplikací pomocí Proxy aplikací Azure AD](manage-apps/application-proxy-publish-azure-portal.md)
