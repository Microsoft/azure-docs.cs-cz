---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "68361554"
---
### <a name="identity-tier"></a>Úroveň identity 

Partnerství s Microsoftem Oracle vám umožní nastavit jednotnou identitu napříč Azure, OCI a aplikací Oracle. Pro řešení JD Edwards EnterpriseOne nebo PeopleSoft spouštěných místně se vyžaduje instance serveru Oracle HTTP (OHS) pro nastavení jednotného přihlašování mezi Azure AD a Oracle IDCS.

OHS slouží jako reverzní proxy server aplikační vrstvy, což znamená, že procházejí všechny požadavky na koncové aplikace. Webbrána správce přístupu Oracle je modul plug-in OHS webového serveru, který zachycuje každý požadavek na ukončení aplikace. Pokud je přístup k prostředkům chráněný (vyžaduje ověřenou relaci), zahájí brána OIDC ověřování pomocí cloudové služby identity prostřednictvím prohlížeče uživatele. Další informace o tocích podporovaných webbranou OpenID Connect najdete v [dokumentaci k Oracle Access Manageru](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

S tímto nastavením může uživatel, který je už přihlášený k Azure AD, přejít na aplikaci řešení JD Edwards EnterpriseOne nebo PeopleSoft spouštěných místně, aniž by se musel znovu přihlašovat prostřednictvím cloudové služby Oracle identity. Zákazníci, kteří toto řešení nasadí, získají výhody jednotného přihlašování, včetně jediné sady přihlašovacích údajů, vylepšeného přihlašování, vylepšeného zabezpečení a snížených nákladů na technickou podporu.

Další informace o nastavení jednotného přihlašování pro řešení JD Edwards EnterpriseOne nebo PeopleSoft spouštěných místně se službou Azure AD najdete v přidruženém dokumentu [White paper pro Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).