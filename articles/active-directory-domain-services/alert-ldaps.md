---
title: 'Azure Active Directory Domain Services: Řešení potíží se zabezpečeným protokolem LDAP | Microsoft Docs'
description: Řešení potíží s protokol Secure LDAP pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8f9f4a8b52548dad011f5e825fa42c50da970ea7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613152"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services – řešení potíží s konfigurací protokol Secure LDAP

Tento článek popisuje řešení běžných problémů při [konfiguraci protokolu Secure LDAP](tutorial-configure-ldaps.md) pro Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: protokol Secure LDAP konfigurace skupiny zabezpečení sítě

**Zpráva upozornění:**

*Pro spravovanou doménu je povolená protokol Secure LDAP přes Internet. Přístup k portu 636 ale není uzamčený pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně pro zneužití hesla hrubou silou.*

### <a name="secure-ldap-port"></a>Port protokol Secure LDAP

Pokud je povolený zabezpečený protokol LDAP, doporučujeme vytvořit další pravidla, která povolí přístup příchozích LDAPs jenom z určitých IP adres. Tato pravidla chrání vaši doménu před útoky hrubou silou, které by mohly představovat bezpečnostní riziko. Port 636 umožňuje přístup ke spravované doméně. Tady je postup aktualizace NSG a povolení přístupu pro protokol Secure LDAP:

1. V Azure Portal přejděte na [kartu skupiny zabezpečení sítě](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) .
2. Z tabulky vyberte NSG přidružené k vaší doméně.
3. Klikněte na **příchozí pravidla zabezpečení** .
4. Vytvoření pravidla portu 636
   1. V horním navigačním panelu klikněte na tlačítko **Přidat** .
   2. Vyberte **IP adresy** pro zdroj.
   3. Zadejte rozsahy zdrojových portů pro toto pravidlo.
   4. Vstup "636" pro rozsahy cílových portů.
   5. Protokol je **TCP**.
   6. Zadejte příslušné pravidlo odpovídající název, popis a prioritu. Priorita pravidla by měla být vyšší než priorita pravidla "Odepřít vše", pokud ji máte.
   7. Klikněte na **OK**.
5. Ověřte, že se vaše pravidlo vytvořilo.
6. Zkontrolujte stav vaší domény dvě hodiny, abyste měli jistotu, že jste tyto kroky dokončili správně.

> [!TIP]
> Port 636 není jediné pravidlo, které vyžaduje, aby Azure AD Domain Services plynule běžel. Další informace najdete v pokynech k [síti](network-considerations.md) nebo v článku věnovaném [odstraňování potíží s konfiguracemi NSG](alert-nsg.md) .
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: protokol Secure LDAP vypršení platnosti certifikátu

**Zpráva upozornění:**

*Platnost certifikátu zabezpečeného protokolu LDAP pro spravovanou doménu vyprší dne [datum]].*

**Řešení:**

Pomocí postupu popsaného v článku [Konfigurace zabezpečeného protokolu LDAP](tutorial-configure-ldaps.md) vytvořte nový certifikát zabezpečeného protokolu LDAP.

## <a name="contact-us"></a>Kontaktujte nás
Kontaktujte tým Azure Active Directory Domain Services produktů, abyste mohli [sdílet zpětnou vazbu nebo podporu](contact-us.md).
