---
title: "Azure Active Directory Domain Services: Řešení potíží s konfigurací zabezpečení LDAP | Microsoft Docs"
description: "Řešení potíží s zabezpečený LDAP pro Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: 38885caacfb98f76627854a782aa56d8a609aad9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services – konfigurace řešení potíží s zabezpečení LDAP

Tento článek poskytuje řešení pro běžné problémy při [konfigurace zabezpečeného LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) pro Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Konfigurace zabezpečeného skupinu zabezpečení sítě LDAP

**Zpráva s výstrahou:**

*Zabezpečený LDAP přes internet je povoleno pro spravované domény. Přístup k portu 636 však není uzamčené pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně k útoku hrubou silou heslo.*

### <a name="secure-ldap-port"></a>Zabezpečený port LDAP

Pokud je povoleno zabezpečené LDAP, doporučujeme vytvořit další pravidla povolit příchozí LDAPS přístup pouze z určité IP adresy. Tato pravidla chránit před útoky hrubou silou, které může představovat bezpečnostní hrozby vaší domény. Port 636 umožňuje přístup k vaší spravované domény. Chcete-li aktualizovat vaše skupina NSG pro povolení přístupu pro zabezpečené LDAP:

1. Přejděte na [kartě skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) na portálu Azure
2. Zvolte NSG přidružené k vaší doméně z tabulky.
3. Klikněte na **příchozí pravidla zabezpečení**
4. Vytvořit pravidlo portu 636
   1. Klikněte na tlačítko **přidat** v horním navigačním panelu.
   2. Zvolte **IP adresy** zdroje.
   3. Zadejte rozsahy portů zdroj pro toto pravidlo.
   4. Zadejte "636" pro cílové rozsahy portů.
   5. Protokol je **TCP**.
   6. Zadejte pro pravidlo vhodný název, popis a priority. Toto pravidlo s prioritou musí být vyšší než pravidla "Deny všechny" priority, pokud nemáte.
   7. Klikněte na **OK**.
5. Ověřte, zda byl vytvořen pravidla.
6. Zkontrolujte stav vaší domény ve dvou hodin k zajištění, že jste dokončili postup správně.

> [!TIP]
> Port 636 není pouze pravidlo, které jsou potřebné pro Azure AD Domain Services plynulejší. Další informace najdete [sítě pokyny](active-directory-ds-networking.md) nebo [NSG řešení potíží s konfigurací](active-directory-ds-troubleshoot-nsg.md) články.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Zabezpečené vypršení platnosti certifikátu LDAP

**Zpráva s výstrahou:**

*Zabezpečený LDAP certifikát pro spravované doméně vyprší XX.*

**Řešení:**

Vytvořit nový certifikát zabezpečený LDAP podle kroků uvedených v [konfigurace zabezpečeného LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) článku.

## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na produktový tým Azure Active Directory Domain Services na [sdílet zpětnou vazbu nebo pro podporu](active-directory-ds-contact-us.md).
