---
title: Konfigurace DNS pro přístup k spravované doméně pomocí protokolu LDAPS přes internet | Dokumentace Microsoftu
description: Konfigurace DNS pro přístup k spravované doméně služby Azure AD Domain Services pomocí protokolu LDAPS přes internet
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 122282d168246e34aaa4a6369f7433b167355887
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152990"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Konfigurace DNS pro přístup k spravované doméně služby Azure AD Domain Services pomocí zabezpečeného protokolu LDAP (LDAPS)

## <a name="before-you-begin"></a>Před zahájením
Kompletní [úloha 3: povolení protokolu LDAPS pro spravované domény pomocí webu Azure portal](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Úloha 4: Konfigurace DNS pro přístup ke spravované doméně z Internetu
> [!TIP]
> **Nepovinná úloha** – Pokud nemáte v plánu přístup ke spravované doméně pomocí protokolu LDAPS přes internet, přeskočte tuto úlohu konfigurace.
>
>

Před zahájením této úlohy, dokončení podle kroků popsaných v [úloha 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Když povolíte přístup protokolu secure LDAP přes internet, budete muset aktualizovat DNS tak, aby klientské počítače můžete najít této spravované doméně. Uvidíte na externí IP adresu **vlastnosti** kartu **externí IP adresu pro LDAPS přístup**.

Externí poskytovatele DNS nakonfigurujte tak, aby název DNS spravovanou doménu (například "ldaps.contoso100.com') odkazuje na tuto externí IP adresu. Například vytvořte následující položku DNS:

    ldaps.contoso100.com  -> 52.165.38.113

A to je vše! Nyní jste připraveni připojit se k spravované doméně pomocí protokolu secure LDAP přes internet.

> [!WARNING]
> Mějte na paměti, že klientské počítače musí důvěřovat vystavitele certifikátu protokolu LDAPS mohli úspěšně připojit ke spravované doméně pomocí protokolu LDAPS. Pokud používáte veřejně důvěryhodné certifikační autority, není nutné nic dělat, protože klientské počítače důvěřují tyto vystavitelů certifikátů. Pokud používáte certifikát podepsaný svým držitelem, nainstalujte veřejnou část certifikátu podepsaného svým držitelem do úložiště důvěryhodných certifikátů na klientském počítači.
>
>

## <a name="next-step"></a>Další krok
[Krok 5: Vytvoření vazby ke spravované doméně a uzamknutí přístupu přes Secure LDAP](active-directory-ds-ldaps-bind-lockdown.md)
