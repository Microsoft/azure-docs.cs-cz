---
title: 'Azure Active Directory Domain Services: Řešení problémů s konfigurací protokolu Secure LDAP | Dokumentace Microsoftu'
description: Řešení potíží s protokol Secure LDAP pro službu Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: be07579d8273ab19101aa6c475ae5d23375517ca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177181"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - konfigurace řešení potíží s Secure LDAP

Tento článek poskytuje řešení pro běžné potíže při [konfiguruje protokol secure LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) pro službu Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Konfigurace skupiny zabezpečení sítě Secure LDAP

**Zpráva s výstrahou:**

*Pro spravovanou doménu je povolený protokol Secure LDAP přes internet. Přístup k portu 636 ale není uzamčen pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně vůči útokům hrubou silou heslo.*

### <a name="secure-ldap-port"></a>Zabezpečený port protokolu LDAP

Když je povolený protokol secure LDAP, doporučujeme vytvořit další pravidla, která povolí příchozí LDAPS přístup pouze z určité IP adresy. Tato pravidla chránit před útoky hrubou silou, které by mohly představovat ohrožení zabezpečení domény. Port 636 umožňuje přístup ke spravované doméně. Tady je postup, chcete-li aktualizovat vaše skupiny zabezpečení sítě umožňující přístup pro protokol Secure LDAP:

1. Přejděte [skupiny zabezpečení sítě kartu](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) na webu Azure Portal
2. Vyberte skupiny zabezpečení sítě přidružené k vaší doméně z tabulky.
3. Klikněte na **příchozí pravidla zabezpečení**
4. Vytvořit pravidlo portu 636
   1. Klikněte na tlačítko **přidat** na horním navigačním panelu.
   2. Zvolte **IP adresy** zdroje.
   3. Zadejte rozsahy zdrojových portů pro toto pravidlo.
   4. Vstup "636" pro rozsahy cílových portů.
   5. Protokol je **TCP**.
   6. Pravidlo poskytněte vhodný název, popis a priority. Priorita tohoto pravidla by měla být vyšší než priorita tohoto pravidla "Odepřít všechny", pokud nemáte.
   7. Klikněte na **OK**.
5. Ověřte, zda byl vytvořen pravidla.
6. Kontrola stavu domény do dvou hodin k zajištění, že jste dokončili postup správně.

> [!TIP]
> Port 636 není jediné pravidlo, které jsou potřebné pro Azure AD Domain Services běží plynule. Další informace najdete [pokyny pro sítě](active-directory-ds-networking.md) nebo [NSG řešení potíží s konfigurací](active-directory-ds-troubleshoot-nsg.md) články.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Zabezpečení vypršení platnosti certifikátu protokolu LDAP

**Zpráva s výstrahou:**

*Certifikát protokolu secure LDAP pro spravovanou doménu vyprší [date]].*

**Řešení:**

Vytvořit nový certifikát protokolu secure LDAP podle kroků uvedených v [nakonfigurovat protokol secure LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) článku.

## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
