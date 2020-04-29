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
ms.openlocfilehash: 6e61571400930d4a781d6d67647bd662a7f2d350
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106215"
---
# <a name="permissions-in-azure-security-center"></a>Oprávnění ve službě Azure Security Center

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Kromě těchto rolí existují ve službě Security Center dvě specifické role:

* **Čtecí modul zabezpečení**: uživatel patřící do této role má práva k zobrazení Security Center. Uživatel může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny.
* **Správce zabezpečení**: uživatel patřící do této role má stejná práva jako čtecí modul zabezpečení a může také aktualizovat zásady zabezpečení a zrušit výstrahy a doporučení.

> [!NOTE]
> Role zabezpečení, čtecí modul zabezpečení a správce zabezpečení mají přístup pouze v Security Center. Role zabezpečení nemají přístup k jiným oblastem služeb Azure, jako jsou Storage, web & Mobile nebo Internet věcí.
>
>

## <a name="roles-and-allowed-actions"></a>Role a povolené akce

V následující tabulce jsou uvedeny role a povolené akce v Security Center.

| Role | Upravit zásady zabezpečení | Použití doporučení zabezpečení pro prostředek</br> (včetně "rychlé opravy!") | Zavřít výstrahy a doporučení | Zobrazit výstrahy a doporučení |
|:--- |:---:|:---:|:---:|:---:|
| Vlastník předplatného | ✔ | ✔ | ✔ | ✔ |
| Přispěvatel předplatného | -- | ✔ | ✔ | ✔ |
| Vlastník skupiny prostředků | -- | ✔ | -- | ✔ |
| Přispěvatel skupiny prostředků | -- | ✔ | -- | ✔ |
| Čtenář | -- | -- | -- | ✔ |
| Správce zabezpečení | ✔ | -- | ✔ | ✔ |
| Čtecí modul zabezpečení | -- | -- | -- | ✔ |

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Přiřaďte například roli Čtenář uživatelům, kteří potřebují jenom zobrazit informace o stavu zabezpečení prostředku, ale neprovádějí akci, jako je třeba použití doporučení nebo úprav zásad.
>
>

## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak Security Center používá RBAC k přiřazení oprávnění uživatelům a identifikaci povolených akcí pro jednotlivé role. Teď, když jste obeznámeni s přiřazením rolí potřebnými k monitorování stavu zabezpečení předplatného, úpravám zásad zabezpečení a použití doporučení, zjistěte, jak:

- [Nastavení zásad zabezpečení ve službě Security Center](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Security Center](security-center-recommendations.md)
- [Monitorování stavu zabezpečení prostředků Azure](security-center-monitoring.md)
- [Správa a reakce na výstrahy zabezpečení v Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorování řešení zabezpečení pro partnery](security-center-partner-solutions.md)
