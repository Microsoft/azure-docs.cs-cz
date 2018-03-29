---
title: Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikace | Microsoft Docs
description: Pokyny při stránky není správně zobrazení v aplikaci Proxy aplikací mít integrované s Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: asteen
ms.openlocfilehash: d187b545a486be28fc80e6baf8e58079ff94ec5e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Stránka aplikace se nezobrazuje správně pro aplikaci Proxy aplikace

Tento článek pomoci při řešení problémů s Proxy aplikace služby Active Directory Azure aplikace, když přejdete na stránku, ale něco na stránce nevypadá správné.

## <a name="overview"></a>Přehled
Když publikujete aplikaci Proxy aplikace, jsou přístupné pouze stránky v rámci kořenového adresáře, při přístupu k aplikaci. Pokud stránky nezobrazuje správně, interní adresy URL kořenového adresáře používá pro aplikace mohou chybět některé prostředky stránky. Chcete-li vyřešit, ujistěte se, jste publikovali *všechny* prostředky pro stránku v rámci vaší aplikace.

Toto je problém otevřením sledovací modul vaší sítě můžete ověřit (například aplikaci Fiddler nebo F12 nástroje v Internet Explorer nebo Edge), načítání stránky a hledá chyb 404. Který označuje stránek, které v současné době nelze nalézt a může stále je třeba publikovat.

Jako příklad tento případ, předpokládá jste publikovali aplikaci výdaje pomocí interní URL <http://myapps/expenses>, ale aplikace používá šablony stylů <http://myapps/style.css>. V takovém případě šablony stylů není publikována ve vaší aplikaci, takže chybu 404 načítání aplikace výdaje výjimku při pokusu o načtení style.css. V tomto příkladu by problém vyřešit tím, že publikujete aplikaci s interní URL <http://myapp/> místo.

## <a name="problems-with-publishing-as-one-application"></a>Problémy s publikování jako jednu aplikaci

Pokud není možné publikovat všechny prostředky v rámci stejné aplikaci, musíte k publikování několika aplikací a povolit propojení mezi nimi.

Chcete-li tak učinit, doporučujeme používat [vlastní domény](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) řešení. Toto řešení je však třeba vlastní certifikát pro vaši doménu a aplikace pomocí plně kvalifikované názvy domény (FQDN). Další možnosti najdete v tématu [řešení potíží s poškozených odkazů dokumentaci](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Další postup
[Publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-publish-azure-portal.md)
