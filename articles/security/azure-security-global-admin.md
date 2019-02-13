---
title: Povolte MFA pro všechny správce Azure
description: Pokyny pro povolení globální správce
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: 2dcd7f42d86000dd3b642c10f6d3db0b0d0fcb03
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116333"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Vynutit ověřování Multi-Factor Authentication (MFA) pro správce předplatného

Při vytváření vašeho správce, včetně vašeho účtu globálního správce, je nezbytné, že používáte metody velmi silné ověřování.

Běžné úkony správy můžete provést pomocí přiřazení rolí správce konkrétní – třeba hesla správce nebo správce systému Exchange – pro uživatelské účty všech zaměstnanců IT podle potřeby.
Kromě toho umožňuje [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) správce přidá druhou vrstvu zabezpečení uživatelská přihlášení a transakce. Azure MFA pomáhá také IT snížit pravděpodobnost, že ohrožení zabezpečení přihlašovacích údajů budou mít přístup k datům organizace.

Příklad: Vynutit Azure MFA pro uživatele a nakonfigurujte ho na použití jako ověřovací prostřednictvím telefonního hovoru nebo textové zprávy. Pokud dojde k ohrožení přihlašovacích údajů uživatele, útočník nebudou mít přístup k jakémukoli prostředku, protože nebude mít přístup k telefonu uživatele. Organizace, které nepřidáte další vrstvy ochrany identit budou náchylnější k útoku krádeží přihlašovacích údajů, což může vést k ohrožení dat.

Pro organizace, které chcete zachovat ověřování celý ovládací prvek místní jeden alternativou je použití [Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), také nazývané "MFA v místním". Pomocí této metody bude stále budete moci vynutit ověřování Multi-Factor Authentication, a zajistit přitom ochranu MFA serveru v místním prostředí.

Chcete-li zkontrolovat, kdo ve vaší organizaci oprávnění správce, které můžete ověřit pomocí Microsoft Azure AD V2 Powershellu následující příkaz:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Povolení MFA

Kontrola jak [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) funguje předtím, než budete pokračovat.

Pokud vaši uživatelé mají licence, které zahrnují službu Azure Multi-Factor Authentication, pro zapnutí Azure MFA není potřeba nic dělat. Můžete začít s vyžadováním dvoustupňového ověřování u jednotlivých uživatelů. Licence, které umožňují Azure MFA, jsou:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Zapnutí dvoustupňového ověřování pro uživatele

Použijte jeden z postupů uvedených v [vyžadování dvoustupňového ověřování](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) pro uživatele nebo skupinu, pokud chcete začít používat Azure MFA. Můžete se rozhodnout vynucovat dvoustupňové ověřování pro všechna přihlášení nebo můžete vytvořit zásady podmíněného přístupu a vyžadovat dvoustupňové ověřování, jenom když potřebujete.

