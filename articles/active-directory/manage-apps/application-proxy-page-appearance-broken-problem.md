---
title: Stránka aplikace se nezobrazuje správně pro aplikaci proxy aplikace | Microsoft Docs
description: Pokyny, pokud se stránka v aplikaci proxy aplikace, kterou jste spojili se službou Azure AD, nezobrazuje správně
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
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
ms.openlocfilehash: 780847494da0ec5cd4eb8ab76f1e46125b8a7f8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764413"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Stránka aplikace se nezobrazuje správně u aplikace proxy aplikace.

Tento článek vám pomůže řešit problémy s Proxy aplikací služby Azure Active Directory aplikacemi při přechodu na stránku, ale něco na stránce nevypadá správně.

## <a name="overview"></a>Přehled
Když publikujete aplikaci proxy aplikace, budou při přístupu k aplikaci přístupné jenom stránky v kořenovém adresáři. Pokud se stránka nezobrazuje správně, může v kořenové interní adrese URL použité pro aplikaci chybět některé prostředky stránky. Chcete-li problém vyřešit, ujistěte se, že jste publikovali *všechny* prostředky pro stránku jako součást aplikace.

Můžete ověřit, jestli se u chybějících prostředků jedná o problém, a to tak, že otevřete sledování sítě (například Fiddler nebo nástroje F12 v aplikaci Internet Explorer/Microsoft Edge), nanačítáte stránku a vyhledáte chyby 404. To indikuje, že stránky se momentálně nenašly a že je potřebujete publikovat.

Jako příklad tohoto případu Předpokládejme, že jste publikovali aplikaci výdajů pomocí interní adresy URL `http://myapps/expenses` , ale aplikace používá šablonu stylů `http://myapps/style.css` . V takovém případě šablona stylů není ve vaší aplikaci publikovaná, takže při pokusu o načtení Style. CSS vyvolají aplikace po načtení chyby 404. V tomto příkladu se problém vyřeší publikováním aplikace s interní adresou URL `http://myapp/` .

## <a name="problems-with-publishing-as-one-application"></a>Problémy s publikováním jako jedna aplikace

Pokud není možné publikovat všechny prostředky ve stejné aplikaci, je nutné publikovat více aplikací a povolit propojení mezi nimi.

K tomu doporučujeme použít řešení [vlastní domény](application-proxy-configure-custom-domain.md) . Toto řešení ale vyžaduje, abyste vlastní certifikát pro vaši doménu a vaše aplikace používaly plně kvalifikované názvy domény (FQDN). Další možnosti najdete v [dokumentaci k řešení potíží s nefunkčními odkazy](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Další kroky
[Publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-add-on-premises-application.md)
