---
title: Odkazy na stránce nefungují pro aplikaci Proxy aplikace
description: Řešení problémů s nefunkčními odkazy v aplikacích proxy aplikací, které jste integrovali do služby Azure AD
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430244"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Odkazy na stránce nefungují pro aplikaci Proxy aplikace

Tento článek vám pomůže vyřešit, proč odkazy na aplikaci proxy aplikací služby Azure Active Directory nefungují správně.

## <a name="overview"></a>Přehled 
Po publikování aplikace Proxy aplikace jsou jedinými odkazy, které v aplikaci fungují ve výchozím nastavení, odkazy na cíle obsažené v publikované kořenové adrese URL. Odkazy v rámci aplikací nefungují, interní adresa URL aplikace pravděpodobně neobsahuje všechny cíle odkazů v rámci aplikace.

**Proč se to děje?** Při klepnutí na odkaz v aplikaci se proxy aplikace pokusí přeložit adresu URL jako interní adresu URL v rámci stejné aplikace nebo jako externě dostupnou adresu URL. Pokud odkaz odkazuje na interní adresu URL, která není ve stejné aplikaci, nepatří do žádného z těchto bloků a výsledkem je nenalezena chyba.

## <a name="ways-you-can-resolve-broken-links"></a>Způsoby řešení nefunkčních odkazů

Tento problém lze vyřešit třemi způsoby. Níže uvedené možnosti jsou uvedeny v rostoucí složitosti.

1.  Ujistěte se, že interní adresa URL je kořen, který obsahuje všechny příslušné odkazy pro aplikaci. To umožňuje všechny odkazy, které mají být vyřešeny jako obsah publikovaný v rámci stejné aplikace.

    Pokud změníte interní adresu URL, ale nechcete změnit vstupní stránku pro uživatele, změňte adresu URL domovské stránky na dříve publikovanou interní adresu URL. To lze provést tak, že přejdete&gt; na "Azure Active Directory" – Registrace aplikací -&gt; vyberte aplikaci -&gt; Branding. V sekci značky se zobrazí pole "Adresa URL domovské stránky", které můžete upravit jako požadovanou vstupní stránku. Pokud stále používáte starší registrace aplikací, karta vlastností zobrazí podrobnosti adresy URL domovské stránky. 
    
    > [!IMPORTANT]
    > Chcete-li provést výše uvedené změny, budete potřebovat práva k úpravám aplikačních objektů ve službě Azure AD. Uživateli musí být přiřazena role [správce aplikací,](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) která uděluje uživateli práva na úpravu aplikace ve službě Azure AD.
    >

2.  Pokud vaše aplikace používají plně kvalifikované názvy domén (Plně kvalifikované názvy domén), použijte k publikování aplikací [vlastní domény.](application-proxy-configure-custom-domain.md) Tato funkce umožňuje stejnou adresu URL, která má být použita interně i externě.

    Tato možnost zajišťuje, že odkazy ve vaší aplikaci jsou externě přístupné prostřednictvím proxy aplikace, protože odkazy v rámci aplikace na interní adresy URL jsou také rozpoznány externě. Všechny odkazy stále musí patřit do publikované aplikace. S touto volbou však odkazy nemusí patřit do stejné aplikace a mohou patřit do více aplikací.

3.  Pokud ani jedna z těchto možností není proveditelná, existuje několik možností pro povolení překladu vřádkových odkazů. Mezi tyto možnosti patří použití spravovaného prohlížeče Intune, rozšíření Moje aplikace nebo nastavení překladu odkazů v aplikaci. Další informace o jednotlivých těchto možnostech a jejich povolení najdete v tématu [Přesměrování pevně zakódovaných odkazů pro aplikace publikované pomocí proxy aplikace Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Další kroky
[Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)

