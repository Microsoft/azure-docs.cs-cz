---
title: Odkazy na stránce nefungují pro aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Řešení potíží s nefunkční odkazy na aplikace Proxy aplikací, které mají integrované s Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 65e903ee1ee8111d3d3b064d6018f49b2e96af47
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478004"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Odkazy na stránce nefungují pro aplikaci Proxy aplikací

Tento článek pomáhá řešit potíže způsobující odkazy na aplikace Azure Active Directory Application Proxy nebudou správně fungovat.

## <a name="overview"></a>Přehled 
Po publikování aplikace Proxy aplikací, jenom odkazy, které fungují ve výchozím nastavení v aplikaci jsou odkazy na cíle, které jsou obsažené v publikované kořenová adresa URL. Nefungují odkazy v rámci aplikace, interní adresa URL pro aplikaci pravděpodobně nezahrnuje všechny cíle odkazů v rámci aplikace.

**Proč k tomu dochází?** Při klepnutí na odkaz v aplikaci Proxy aplikací se pokusí přeložit adresu URL jako buď interní adresa URL uvnitř stejné aplikace nebo jako adresu URL externě dostupný. Pokud odkaz směřuje na interní adresu URL, která není v rámci stejné aplikace, ji není patří do některé z těchto kontejnerů a způsobit chybu nenalezení.

## <a name="ways-you-can-resolve-broken-links"></a>Způsoby, které lze vyřešit nefunkční odkazy

Existují tři způsoby, jak tento problém vyřešit. Volby níže jsou uvedeny v zvýšit složitost.

1.  Ujistěte se, že interní adresa URL kořenového, která obsahuje všechny odkazy, které jsou relevantní pro aplikaci. To umožňuje všechny odkazy na být vyřešeny automatickým obsah publikovaný v rámci stejné aplikace.

    Pokud změníte interní adresa URL, ale nechcete změnit cílová stránka pro uživatele, změňte adresu URL domovské stránky na dříve publikovaným interní adresa URL. To můžete udělat tak, že přejdete do "Azure Active Directory" -&gt; registrace aplikací –&gt; vyberte aplikaci, kterou -&gt; vlastnosti. Na této kartě Vlastnosti se zobrazí pole "Adresa URL domovské stránky", který můžete upravit na požadovanou cílovou stránkou.

2.  Pokud vaše aplikace použít plně kvalifikované názvy domény (FQDN), použijte [vlastních domén](application-proxy-configure-custom-domain.md) k publikování aplikace. Tato funkce umožňuje stejnou adresu URL, který se má použít jak interně a externě.

    Tato možnost zajišťuje odkazy v aplikaci zvenku přístupný prostřednictvím Proxy aplikací od odkazy v aplikaci na interní adresy URL jsou také rozpoznány externě. Všechny odkazy stále musí patřit do publikované aplikace. Však s touto možností odkazy nemusí patřit do stejné aplikace a můžou patřit do několika aplikací.

3.  Pokud žádná z těchto možností jsou to proveditelné, povolení vložený odkaz překlad více způsoby. Mezi tyto možnosti patří pomocí Intune Managed Browser, Moje aplikace rozšíření, nebo pomocí nastavení překladu odkaz na aplikaci. Další informace o každé z těchto možností a jak je chcete povolit, najdete v článku [přesměrovat odkazy pevně zakódované k aplikacím publikovaným pomocí Proxy aplikací Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Další postup
[Práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)

