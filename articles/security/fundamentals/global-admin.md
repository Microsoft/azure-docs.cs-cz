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
ms.openlocfilehash: e30ce71a66dd5cb6c810111d359660d875ae97a8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927908"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Vynutil vícefaktorové ověřování (MFA) pro správce předplatného

Při vytváření správců, včetně účtu globálního správce, je nezbytné použít velmi silné metody ověřování.

Každodenní správu můžete provádět přiřazením konkrétních rolí správce, jako je třeba správce Exchange nebo správce hesel – podle potřeby uživatelských účtů zaměstnanců IT.
Kromě toho může [Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) pro vaše správce přidat druhou úroveň zabezpečení pro přihlášení a transakce uživatelů. Azure MFA také pomáhá snižovat pravděpodobnost, že by napadené přihlašovací údaje měly přístup k datům organizace.

Příklad: Pro uživatele vynutili Azure MFA a nakonfigurujete ji tak, aby jako ověření používala telefonní hovor nebo textovou zprávu. Pokud dojde k ohrožení přihlašovacích údajů uživatele, útočník nebude mít přístup k jakémukoli prostředku, protože nebude mít přístup k telefonu uživatele. Organizacím, které nepřidává další vrstvy Identity Protection, jsou náchylnější k útoku prostřednictvím krádeže přihlašovacích údajů, což může vést k ohrožení bezpečnosti dat.

Jedna alternativa pro organizace, které chtějí zachovat celé řízení ověřování v místním prostředí, je použití [serveru Azure Multi-Factor Authentication Server](../../active-directory/authentication/howto-mfaserver-deploy.md), který se také nazývá "ověřování v místním prostředí". Pomocí této metody budete moci vyhodnotit vícefaktorové ověřování a současně zachovat Server MFA v místním prostředí.

Chcete-li zjistit, kdo ve vaší organizaci má oprávnění správce, můžete ověřit pomocí následujícího příkazu Microsoft Azure AD v2 prostředí PowerShell:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Povolení MFA

Přečtěte si, jak [MFA](../../active-directory/authentication/howto-mfa-mfasettings.md) funguje, než budete pokračovat.

Pokud vaši uživatelé mají licence, které zahrnují službu Azure Multi-Factor Authentication, pro zapnutí Azure MFA není potřeba nic dělat. Můžete začít s vyžadováním dvoustupňového ověřování u jednotlivých uživatelů. Licence, které umožňují Azure MFA, jsou:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Zapnutí dvoustupňového ověřování pro uživatele

Použijte jeden z postupů uvedených v tématu [jak vyžadovat dvoustupňové ověřování](../../active-directory/authentication/howto-mfa-userstates.md) pro uživatele nebo skupinu, aby bylo možné začít používat Azure MFA. Můžete vynutit dvoustupňové ověřování pro všechna přihlášení, nebo můžete vytvořit zásady podmíněného přístupu, které budou vyžadovat dvoustupňové ověřování jenom v případě, že to záleží na vás.

