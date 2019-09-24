---
title: Oprávnění v Azure Security Center | Microsoft Docs
description: Tento článek vysvětluje, jak Azure Security Center používá řízení přístupu na základě rolí k přiřazování oprávnění uživatelům a identifikaci povolených akcí pro jednotlivé role.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: f52d518d2ed1dfb62eed72cf9c0b839a37b7f856
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201643"
---
# <a name="permissions-in-azure-security-center"></a>Oprávnění v Azure Security Center

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Ve službě Security Center zobrazí jenom informace související s prostředku, když máte přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které prostředek patří.

Kromě těchto rolí existují ve službě Security Center dvě specifické role:

* **Čtecí modul zabezpečení**: Uživatel, který patří do této role, má oprávnění k zobrazení Security Center. Uživatel může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny.
* **Správce zabezpečení**: Uživatel, který patří do této role, má stejná práva jako čtecí modul zabezpečení a může také aktualizovat zásady zabezpečení a zrušit výstrahy a doporučení.

> [!NOTE]
> Role zabezpečení, čtecí modul zabezpečení a správce zabezpečení mají přístup pouze v Security Center. Role zabezpečení nemají přístup k jiným oblastem služeb Azure, jako jsou Storage, web & Mobile nebo Internet věcí.
>
>

## <a name="roles-and-allowed-actions"></a>Role a povolené akce

V následující tabulce jsou uvedeny role a povolené akce v Security Center. X označuje, že akce je pro tuto roli povolena.

| Role | Upravit zásady zabezpečení | Použití doporučení zabezpečení pro prostředek | Zavřít výstrahy a doporučení | Zobrazit výstrahy a doporučení |
|:--- |:---:|:---:|:---:|:---:|
| Vlastník předplatného | X | X | X | X |
| Přispěvatel předplatného | -- | X | X | X |
| Vlastník skupiny prostředků | -- | X | -- | X |
| Přispěvatel skupiny prostředků | -- | X | -- | X |
| Čtenář | -- | -- | -- | X |
| Správce zabezpečení | X | -- | X | X |
| Čtenář zabezpečení | -- | -- | -- | X |

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Přiřaďte například roli Čtenář uživatelům, kteří potřebují jenom zobrazit informace o stavu zabezpečení prostředku, ale neprovádějí akci, jako je třeba použití doporučení nebo úprav zásad.
>
>

## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak Security Center používá RBAC k přiřazení oprávnění uživatelům a identifikaci povolených akcí pro jednotlivé role. Teď, když jste obeznámeni s přiřazením rolí potřebnými k monitorování stavu zabezpečení předplatného, úpravám zásad zabezpečení a použití doporučení, zjistěte, jak:

- [Nastavení zásad zabezpečení v Security Center](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Security Center](security-center-recommendations.md)
- [Monitorování stavu zabezpečení prostředků Azure](security-center-monitoring.md)
- [Správa a reakce na výstrahy zabezpečení ve službě Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorování řešení zabezpečení pro partnery](security-center-partner-solutions.md)
