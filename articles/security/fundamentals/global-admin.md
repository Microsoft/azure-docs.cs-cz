---
title: Povolit vícefaktorové ověřování pro všechny správce Azure
description: Pokyny pro povolení globálního správce
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: 702feded450aed7368836c7eff799e969b9f396b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727305"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Vynutil vícefaktorové ověřování (MFA) pro správce předplatného

Při vytváření správců, včetně účtu globálního správce, je nezbytné použít velmi silné metody ověřování.

Každodenní správu můžete provádět přiřazením konkrétních rolí správce, jako je třeba správce Exchange nebo správce hesel – podle potřeby uživatelských účtů zaměstnanců IT.
Kromě toho může [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) pro vaše správce přidat druhou úroveň zabezpečení pro přihlášení a transakce uživatelů. Azure MFA také pomáhá snižovat pravděpodobnost, že by napadené přihlašovací údaje měly přístup k datům organizace.

Příklad: Pro uživatele vynutili Azure MFA a nakonfigurujete ji tak, aby jako ověření používala telefonní hovor nebo textovou zprávu. Pokud dojde k ohrožení přihlašovacích údajů uživatele, útočník nebude mít přístup k jakémukoli prostředku, protože nebude mít přístup k telefonu uživatele. Organizacím, které nepřidává další vrstvy Identity Protection, jsou náchylnější k útoku prostřednictvím krádeže přihlašovacích údajů, což může vést k ohrožení bezpečnosti dat.

Jedna alternativa pro organizace, které chtějí zachovat celé řízení ověřování v místním prostředí, je použití [serveru Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), který se také nazývá "ověřování v místním prostředí". Pomocí této metody budete moci vyhodnotit vícefaktorové ověřování a současně zachovat Server MFA v místním prostředí.

Chcete-li zjistit, kdo ve vaší organizaci má oprávnění správce, můžete ověřit pomocí následujícího příkazu Microsoft Azure AD v2 prostředí PowerShell:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Povolení MFA

Přečtěte si, jak [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) funguje, než budete pokračovat.

Pokud vaši uživatelé mají licence, které zahrnují službu Azure Multi-Factor Authentication, pro zapnutí Azure MFA není potřeba nic dělat. Můžete začít s vyžadováním dvoustupňového ověřování u jednotlivých uživatelů. Licence, které umožňují Azure MFA, jsou:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Zapnutí dvoustupňového ověřování pro uživatele

Použijte jeden z postupů uvedených v tématu [jak vyžadovat dvoustupňové ověřování](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) pro uživatele nebo skupinu, aby bylo možné začít používat Azure MFA. Můžete vynutit dvoustupňové ověřování pro všechna přihlášení, nebo můžete vytvořit zásady podmíněného přístupu, které budou vyžadovat dvoustupňové ověřování jenom v případě, že to záleží na vás.

