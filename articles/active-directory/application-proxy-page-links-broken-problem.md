---
title: Odkazy na stránce nefungují pro aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Řešení potíží s nefunkční odkazy na aplikace Proxy aplikací, které mají integrované s Azure AD
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
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 09fa527f8a1e03d568dec67369651bbcee2a26f2
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365817"
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

2.  Pokud vaše aplikace použít plně kvalifikované názvy domény (FQDN), použijte [vlastních domén](manage-apps/application-proxy-configure-custom-domain.md) k publikování aplikace. Tato funkce umožňuje stejnou adresu URL, který se má použít jak interně a externě.

    Tato možnost zajišťuje odkazy v aplikaci zvenku přístupný prostřednictvím Proxy aplikací od odkazy v aplikaci na interní adresy URL jsou také rozpoznány externě. Všechny odkazy stále musí patřit do publikované aplikace. Však s touto možností odkazy nemusí patřit do stejné aplikace a můžou patřit do několika aplikací.

3.  Pokud žádná z těchto možností lze provést, je ve verzi preview novou funkci, která provádí překlad/přepisování adres URL. Pomocí této funkce interní adresy URL nebo odkazy, které existují v textu HTML vašich aplikací jsou přeložit, nebo "mapovat", na publikované externí Proxy adresy URL aplikací. Tento překlad funguje pouze na odkazy ve formátu HTML nebo šablon stylů CSS a nepomůže, pokud odkaz na vaši je generován prostřednictvím JS. 

V důsledku toho důrazně doporučujeme používat [vlastních domén](manage-apps/application-proxy-configure-custom-domain.md) řešení, pokud je to možné. Pokud chcete získat verzi preview, e-mailu <aadapfeedback@microsoft.com> s applicationId(s).

## <a name="next-steps"></a>Další postup
[Práce s existující místní proxy servery](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

