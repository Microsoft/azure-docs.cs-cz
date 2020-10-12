---
title: Odkazy na stránce nefungují pro aplikaci proxy aplikace.
description: Řešení potíží s nefunkčními odkazy v aplikacích proxy aplikací, které jste integrují s Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050874b301628348b513b8b5b0c2b878a1dcecaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84760197"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Odkazy na stránce nefungují pro aplikaci proxy aplikace.

Tento článek vám pomůže při odstraňování potíží, proč odkazy v aplikaci Proxy aplikací služby Azure Active Directory nefungují správně.

## <a name="overview"></a>Přehled 
Po publikování aplikace proxy aplikací jsou jediným odkazům, které fungují ve výchozím nastavení v aplikaci, odkazy na cíle obsažené v publikované kořenové adrese URL. Odkazy v aplikacích nefungují, interní adresa URL pro aplikaci pravděpodobně neobsahuje všechna umístění odkazů v rámci aplikace.

**Proč k tomu dochází?** Když kliknete na odkaz v aplikaci, pokusí se proxy aplikace přeložit adresu URL jako interní adresu URL v rámci stejné aplikace nebo jako externě dostupnou adresu URL. Pokud odkaz odkazuje na interní adresu URL, která není v rámci stejné aplikace, nepatří do žádné z těchto kontejnerů a výsledkem není chyba, která nebyla nalezena.

## <a name="ways-you-can-resolve-broken-links"></a>Způsoby, jak můžete vyřešit nefunkční odkazy

Existují tři způsoby, jak tento problém vyřešit. Níže uvedené možnosti jsou uvedeny a zvyšují složitost.

1.  Ujistěte se, že interní adresa URL je kořen, který obsahuje všechny relevantní odkazy pro aplikaci. To umožňuje, aby všechny odkazy byly vyřešeny jako obsah publikovaný v rámci stejné aplikace.

    Pokud změníte interní adresu URL, ale nechcete změnit cílovou stránku pro uživatele, změňte adresu URL domovské stránky na dříve publikovanou interní adresu URL. To se dá udělat tak, že v části Azure Active Directory – &gt; Registrace aplikací &gt; vyberete aplikaci – &gt; branding. V části branding se zobrazí pole Adresa URL domovské stránky, které můžete upravit jako požadovanou cílovou stránku. Pokud pořád používáte starší verze Registrace aplikací zkušeností, na kartě Vlastnosti se zobrazí podrobnosti adresa URL domovské stránky. 
    
    > [!IMPORTANT]
    > Aby bylo možné provést výše uvedené změny, budete potřebovat práva k úpravám aplikačních objektů ve službě Azure AD. Uživatel musí mít přiřazenou roli [správce aplikace](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) , která uživateli uděluje modificaion práva k aplikacím v Azure AD.
    >

2.  Pokud vaše aplikace používají plně kvalifikované názvy domén (FQDN), publikujte své aplikace pomocí [vlastních domén](application-proxy-configure-custom-domain.md) . Tato funkce umožňuje používat stejnou adresu URL interně i externě.

    Tato možnost zajistí, že jsou odkazy v aplikaci externě přístupné prostřednictvím proxy aplikací, protože odkazy v aplikaci na interní adresy URL jsou také rozpoznány externě. Všechny odkazy stále potřebují patřit do publikované aplikace. Pomocí této možnosti ale odkazy nemusejí patřit do stejné aplikace a můžou patřit do více aplikací.

3.  Pokud žádná z těchto možností není proveditelná, existuje více možností pro povolení překladu vloženého odkazu. Mezi tyto možnosti patří použití rozšíření Intune Managed Browser, moje aplikace nebo použití nastavení překladu odkazů ve vaší aplikaci. Další informace o každé z těchto možností a o tom, jak je povolit, najdete v tématu [přesměrování pevně zakódované odkazů pro aplikace publikované pomocí Azure proxy aplikací služby AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Další kroky
[Práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)

