---
title: Odkazy na stránce nefungují pro aplikaci proxy aplikace.
description: Řešení potíží s nefunkčními odkazy v aplikacích proxy aplikací, které jste integrují s Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 570699fe83197a1b5442909d8b89e285a1dfa73b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275440"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Odkazy na stránce nefungují pro aplikaci proxy aplikace.

Tento článek vám pomůže při odstraňování potíží, proč odkazy v aplikaci Proxy aplikací služby Azure Active Directory nefungují správně.

## <a name="overview"></a>Přehled 
Po publikování aplikace proxy aplikací jsou jediným odkazům, které fungují ve výchozím nastavení v aplikaci, odkazy na cíle obsažené v publikované kořenové adrese URL. Odkazy v aplikacích nefungují, interní adresa URL pro aplikaci pravděpodobně neobsahuje všechna umístění odkazů v rámci aplikace.

**Proč k tomu dochází?** Když kliknete na odkaz v aplikaci, pokusí se proxy aplikace přeložit adresu URL jako interní adresu URL v rámci stejné aplikace nebo jako externě dostupnou adresu URL. Pokud odkaz odkazuje na interní adresu URL, která není v rámci stejné aplikace, nepatří do žádné z těchto kontejnerů a výsledkem není chyba, která nebyla nalezena.

## <a name="ways-you-can-resolve-broken-links"></a>Způsoby, jak můžete vyřešit nefunkční odkazy

Existují tři způsoby, jak tento problém vyřešit. Níže uvedené možnosti jsou uvedeny a zvyšují složitost.

1.  Ujistěte se, že interní adresa URL je kořen, který obsahuje všechny relevantní odkazy pro aplikaci. To umožňuje, aby všechny odkazy byly vyřešeny jako obsah publikovaný v rámci stejné aplikace.

    Pokud změníte interní adresu URL, ale nechcete změnit cílovou stránku pro uživatele, změňte adresu URL domovské stránky na dříve publikovanou interní adresu URL. Můžete to udělat tak, že v části Azure Active Directory&gt; registrace aplikací-&gt; vyberete vlastnosti aplikace-&gt;. Na této kartě Vlastnosti vidíte pole Adresa URL domovské stránky, které můžete upravit jako požadovanou cílovou stránku.

2.  Pokud vaše aplikace používají plně kvalifikované názvy domén (FQDN), publikujte své aplikace pomocí [vlastních domén](application-proxy-configure-custom-domain.md) . Tato funkce umožňuje používat stejnou adresu URL interně i externě.

    Tato možnost zajistí, že jsou odkazy v aplikaci externě přístupné prostřednictvím proxy aplikací, protože odkazy v aplikaci na interní adresy URL jsou také rozpoznány externě. Všechny odkazy stále potřebují patřit do publikované aplikace. Pomocí této možnosti ale odkazy nemusejí patřit do stejné aplikace a můžou patřit do více aplikací.

3.  Pokud žádná z těchto možností není proveditelná, existuje více možností pro povolení překladu vloženého odkazu. Mezi tyto možnosti patří použití rozšíření Intune Managed Browser, moje aplikace nebo použití nastavení překladu odkazů ve vaší aplikaci. Další informace o každé z těchto možností a o tom, jak je povolit, najdete v tématu [přesměrování pevně zakódované odkazů pro aplikace publikované pomocí Azure proxy aplikací služby AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Další kroky
[Práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)

