---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673760"
---
### <a name="identity-tier"></a>Úroveň identity 

Partnerství Microsoft-Oracle umožňuje nastavit jednotnou identitu napříč Azure, OCI a aplikací Oracle. Pro řešení JD Edwards EnterpriseOne nebo PeopleSoft spouštěných místně se vyžaduje instance serveru Oracle HTTP (OHS) pro nastavení jednotného přihlašování mezi Azure AD a Oracle IDCS.

OHS slouží jako reverzní proxy server aplikační vrstvy, což znamená, že procházejí všechny požadavky na koncové aplikace. Webbrána správce přístupu Oracle je modul plug-in OHS webového serveru, který zachycuje každý požadavek na ukončení aplikace. Pokud je přístup k prostředkům chráněný (vyžaduje ověřenou relaci), zahájí brána OIDC ověřování pomocí cloudové služby identity prostřednictvím prohlížeče uživatele. Další informace o tocích podporovaných webbranou OpenID Connect najdete v [dokumentaci k Oracle Access Manageru](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327).

S tímto nastavením může uživatel, který je už přihlášený k Azure AD, přejít na aplikaci řešení JD Edwards EnterpriseOne nebo PeopleSoft spouštěných místně, aniž by se musel znovu přihlašovat prostřednictvím cloudové služby Oracle identity. Zákazníci, kteří toto řešení nasadí, získají výhody jednotného přihlašování, včetně jediné sady přihlašovacích údajů, vylepšeného přihlašování, vylepšeného zabezpečení a snížených nákladů na technickou podporu.

Další informace o nastavení jednotného přihlašování pro řešení JD Edwards EnterpriseOne nebo PeopleSoft spouštěných místně se službou Azure AD najdete v přidruženém dokumentu [White paper pro Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).