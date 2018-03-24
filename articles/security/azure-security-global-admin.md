---
title: Povolit pro všechny správce Azure
description: Pokyny k povolení globální správce.
ms.service: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: a247f5afbca491dc9c31c74453860961188411c9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Vynutit ověřování Multi-Factor authentication (MFA) pro správci předplatného

Když vytvoříte vašimi správci, včetně svůj účet globálního správce je nezbytné, že používáte metody velmi silné ověřování.

Můžete provádět každodenní správu podle přiřazení rolí správce konkrétní – například heslo správce nebo správce serveru Exchange – na uživatelské účty zaměstnanců IT podle potřeby.
Kromě toho povolení [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) pro správce přidá druhou vrstvu zabezpečení uživatelská přihlášení a transakce. Azure MFA také pomáhá IT sníží pravděpodobnost, že ohrožené pověření bude mít přístup k dat organizace.

Příklad: můžete vynutit vícefaktorové ověřování Azure pro vaše uživatele a bude sloužit jako ověření telefonního hovoru nebo textové zprávy. Pokud dojde k ohrožení přihlašovacích údajů uživatele, útočník nebudou mít přístup k jakémukoli prostředku, vzhledem k tomu, že nebudete mít přístup k telefonu uživatele. Organizace, které Nepřidávejte další vrstvy ochrany identit budou náchylnější pro útoku krádeží přihlašovacích údajů, což může vést k ohrožení zabezpečení dat.

Jeden alternativou pro organizace, které chcete zachovat celý proces ověřování řízení místní je použít [Azure Multi-Factor Authentication Server](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), označované taky jako "MFA místní". Pomocí této metody přesto bude možné vynutit ověřování Multi-Factor authentication, a zajistit přitom ochranu MFA server místní.

Chcete-li zkontrolovat, kdo ve vaší organizaci oprávnění správce, které si můžete ověřit pomocí následujícího příkazu Powershellu Microsoft Azure AD V2:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Povolení vícefaktorového ověřování

Zkontrolujte jak [MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-whats-next) funguje, než budete pokračovat.

Pokud vaši uživatelé mají licence, které zahrnují službu Azure Multi-Factor Authentication, pro zapnutí Azure MFA není potřeba nic dělat. Můžete začít s vyžadováním dvoustupňového ověřování u jednotlivých uživatelů. Licence, které umožňují Azure MFA, jsou:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Zapnutí dvoustupňového ověřování pro uživatele

Použijte jeden z postupů uvedených v [jak vyžadovat dvoustupňové ověření](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) pro uživatele nebo skupinu, pokud chcete začít používat Azure MFA. Můžete se rozhodnout vynucovat dvoustupňové ověřování pro všechna přihlášení nebo můžete vytvořit zásady podmíněného přístupu a vyžadovat dvoustupňové ověřování, jenom když potřebujete.

