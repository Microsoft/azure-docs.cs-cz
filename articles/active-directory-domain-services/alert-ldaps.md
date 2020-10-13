---
title: Vyřešit výstrahy zabezpečeného protokolu LDAP v Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné výstrahy pomocí protokolu Secure LDAP pro Azure Active Directory Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: b9bdcc7e338c0dffc97bd05a6ae9b64947542b2e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962798"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Známé problémy: výstrahy protokol Secure LDAP v Azure Active Directory Domain Services

Aplikace a služby, které používají protokol LDAP (Lightweight Directory Access Protocol) ke komunikaci s Azure Active Directory Domain Services (Azure služba AD DS), je možné [nakonfigurovat tak, aby používaly zabezpečený protokol LDAP](tutorial-configure-ldaps.md). Aby zabezpečený protokol LDAP správně fungoval, musí být otevřený vhodný certifikát a požadované síťové porty.

Tento článek vám pomůže pochopit a vyřešit běžné výstrahy pomocí zabezpečeného přístupu LDAP v Azure služba AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: konfigurace sítě protokol Secure LDAP

### <a name="alert-message"></a>Zpráva výstrahy

*Pro spravovanou doménu je povolená protokol Secure LDAP přes Internet. Přístup k portu 636 ale není uzamčený pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně pro zneužití hesla hrubou silou.*

### <a name="resolution"></a>Řešení

Pokud povolíte zabezpečený protokol LDAP, doporučujeme vytvořit další pravidla, která omezují přístup příchozích LDAP na konkrétní IP adresy. Tato pravidla chrání spravovanou doménu před útoky hrubou silou. Pokud chcete aktualizovat skupinu zabezpečení sítě tak, aby se omezil přístup k portu TCP 636 pro zabezpečený protokol LDAP, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **skupiny zabezpečení sítě**.
1. Zvolte skupinu zabezpečení sítě přidruženou k vaší spravované doméně, například *AADDS-contoso.com-NSG*, a pak vyberte **příchozí pravidla zabezpečení** .
1. Vyberte **+ Přidat** a vytvořte pravidlo pro port TCP 636. V případě potřeby vyberte v okně možnost **Upřesnit** a vytvořte pravidlo.
1. V části **zdroj**vyberte *IP adresy* z rozevírací nabídky. Zadejte zdrojové IP adresy, u kterých chcete udělit přístup pro zabezpečený přenos LDAP.
1. Vyberte *jako* **cíl**a pak zadejte *636* pro **rozsahy cílových portů**.
1. Nastavte **protokol** *TCP* a **akci** , která má být *povolena*.
1. Zadejte prioritu pravidla a potom zadejte název, například *RestrictLDAPS*.
1. Až budete připraveni, vyberte **Přidat** a vytvořte pravidlo.

Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

> [!TIP]
> Nejedná se o jediné pravidlo, které vyžaduje, aby Azure služba AD DS běžely hladce. 636 Další informace najdete v tématu [skupiny zabezpečení sítě Azure služba AD DS a požadované porty](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: vyprší platnost certifikátu protokol Secure LDAP

### <a name="alert-message"></a>Zpráva výstrahy

*Platnost certifikátu zabezpečeného protokolu LDAP pro spravovanou doménu vyprší dne [datum]].*

### <a name="resolution"></a>Řešení

Pomocí postupu pro [Vytvoření certifikátu protokolu Secure LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)vytvořte náhradní certifikát zabezpečeného protokolu LDAP. Použijte náhradní certifikát pro Azure služba AD DS a distribuujte certifikát pro všechny klienty, kteří se připojují pomocí protokolu Secure LDAP.

## <a name="next-steps"></a>Další kroky

Pokud stále máte problémy, [otevřete žádost o podporu Azure][azure-support] , kde najdete další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
