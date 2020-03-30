---
title: Řešení zabezpečených výstrah LDAP ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit a řešit běžné výstrahy pomocí zabezpečeného protokolu LDAP pro službu Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258052"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Známé problémy: Zabezpečení výstrah LDAP ve službě Azure Active Directory Domain Services

Aplikace a služby, které ke komunikaci se službou LdAP (LdAP) používají protokol LDAP (LIGHTWEIGHT Directory Access Services), lze [nakonfigurovat tak, aby používaly zabezpečené protokol LDAP](tutorial-configure-ldaps.md). Aby zabezpečené protokol LDAP fungovaly správně, musí být otevřen příslušný certifikát a požadované síťové porty.

Tento článek vám pomůže pochopit a vyřešit běžné výstrahy se zabezpečeným přístupem LDAP ve službě Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Bezpečná konfigurace sítě LDAP

### <a name="alert-message"></a>Výstražná zpráva

*Pro spravovanou doménu je povoleno zabezpečené protokol LDAP přes internet. Přístup k portu 636 však není uzamčen pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně k útokům hrubou silou hesla.*

### <a name="resolution"></a>Řešení

Pokud povolíte zabezpečené protokol LDAP, doporučujeme vytvořit další pravidla, která omezují příchozí přístup ldaps na konkrétní adresy IP. Tato pravidla chrání spravovanou doménu Azure AD DS před útoky hrubou silou. Chcete-li aktualizovat skupinu zabezpečení sítě tak, aby omezila přístup k portu TCP 636 pro zabezpečený protokol LDAP, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **skupiny zabezpečení sítě**.
1. Zvolte skupinu zabezpečení sítě přidruženou ke spravované doméně, *například AADDS-contoso.com-NSG*, a pak vyberte **Příchozí pravidla zabezpečení.**
1. **+ Přidejte** pravidlo pro port TCP 636. V případě potřeby **vyberte** Upřesnit v okně a vytvořte pravidlo.
1. V **rozevírací**nabídce Source zvolte *IP adresy.* Zadejte zdrojové adresy IP, které chcete udělit přístup pro zabezpečený provoz LDAP.
1. Jako **cíl**zvolte *Libovolné* a pak zadejte **rozsahy cílových portů** *636* .
1. Nastavte **protokol** jako *protokol TCP* a **akci,** která má *být povolena*.
1. Zadejte prioritu pravidla a zadejte název, například *RestrictLDAPS*.
1. Až budete připraveni, vyberte **Přidat** a vytvořte pravidlo.

Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

> [!TIP]
> Tcp port 636 není jediné pravidlo potřebné pro Azure AD DS běžet hladce. Další informace najdete ve [skupinách zabezpečení sítě Azure AD DS a požadovaných portech](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Platnost zabezpečeného certifikátu LDAP vypršela

### <a name="alert-message"></a>Výstražná zpráva

*Platnost zabezpečeného certifikátu LDAP pro spravovanou doménu vyprší [date]].*

### <a name="resolution"></a>Řešení

Vytvořte náhradní zabezpečený certifikát LDAP podle pokynů k [vytvoření certifikátu pro zabezpečený protokol LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Použijte náhradní certifikát pro Azure AD DS a distribuovat certifikát všem klientům, kteří se připojují pomocí zabezpečeného LDAP.

## <a name="next-steps"></a>Další kroky

Pokud máte stále problémy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
