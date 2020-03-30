---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361554"
---
### <a name="identity-tier"></a>Úroveň identity 

Partnerství Microsoft-Oracle umožňuje nastavit jednotnou identitu napříč Azure, OCI a vaší aplikací Oracle. Pro jd Edwards EnterpriseOne nebo sadu aplikací PeopleSoft je potřeba instance serveru ORACLE HTTP Server (OHS) k nastavení jednotného přihlášení mezi Azure AD a Oracle IDCS.

OHS funguje jako reverzní proxy pro aplikační vrstvu, což znamená, že všechny požadavky na koncové aplikace projít. Oracle Access Manager WebGate je plugin pro webový server OHS, který zachycuje každý požadavek na koncovou aplikaci. Pokud je prostředek, ke který se přistupuje, chráněn (vyžaduje ověřenou relaci), webgate iniciuje tok ověřování OIDC pomocí služby Identity Cloud Service prostřednictvím prohlížeče uživatele. Další informace o tocích podporovaných aplikací OpenID Connect WebGate naleznete v [dokumentaci k aplikaci Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Díky tomuto nastavení může uživatel, který je již přihlášen ke službě Azure AD, přejít na aplikaci JD Edwards EnterpriseOne nebo PeopleSoft, aniž by se znovu přihlásil prostřednictvím služby Oracle Identity Cloud Service. Zákazníci, kteří nasazují toto řešení, získají výhody jednotného přihlašování, včetně jediné sady přihlašovacích údajů, vylepšeného možnosti přihlašování, vylepšeného zabezpečení a snížených nákladů na technickou podporu.

Další informace o nastavení jednotného přihlašování pro JD Edwards EnterpriseOne nebo PeopleSoft ve službě Azure AD najdete v související [matné knize Oracle Whitepaper](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).