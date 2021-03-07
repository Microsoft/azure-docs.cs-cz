---
title: Oprávnění v Azure Security Center | Microsoft Docs
description: Tento článek vysvětluje, jak Azure Security Center používá řízení přístupu na základě rolí k přiřazování oprávnění uživatelům a identifikaci povolených akcí pro jednotlivé role.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 01/03/2021
ms.author: memildin
ms.openlocfilehash: dcbb4977e1bfd17f0cbed61abf9ba335615b7799
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443756"
---
# <a name="permissions-in-azure-security-center"></a>Oprávnění ve službě Azure Security Center

Azure Security Center používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), které poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , které je možné přiřadit uživatelům, skupinám a službám v Azure.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Kromě těchto rolí existují ve službě Security Center dvě specifické role:

* **Čtecí modul zabezpečení**: uživatel patřící do této role má práva k zobrazení Security Center. Uživatel může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny.
* **Správce zabezpečení**: uživatel patřící do této role má stejná práva jako čtecí modul zabezpečení a může také aktualizovat zásady zabezpečení a zrušit výstrahy a doporučení.

> [!NOTE]
> Role zabezpečení, čtecí modul zabezpečení a správce zabezpečení mají přístup pouze v Security Center. Role zabezpečení nemají přístup k jiným oblastem služeb Azure, jako jsou Storage, web & Mobile nebo Internet věcí.
>

## <a name="roles-and-allowed-actions"></a>Role a povolené akce

V následující tabulce jsou uvedeny role a povolené akce v Security Center.

| Akce                                                                                                                                        | Čtecí modul zabezpečení/ <br> Čtenář | Správce zabezpečení | Přispěvatel skupiny prostředků/ <br> Vlastník skupiny prostředků | Přispěvatel předplatného | Vlastník předplatného |
|:----------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
| Upravit zásady zabezpečení                                                                                                                          | -                             | ✔             | -                                                      | -                        | ✔                 |
| Přidat nebo přiřadit iniciativy (včetně) standardů dodržování předpisů v legislativě                                                                           | -                             | -              | -                                                      | -                        | ✔                 |
| Povolit nebo zakázat Azure Defender                                                                                                               | -                             | ✔             | -                                                      | -                        | ✔                 |
| Povolit nebo zakázat Automatické zřizování                                                                                                            | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Použití doporučení zabezpečení pro prostředek</br> (a použijte [rychlou opravu!](security-center-remediate-recommendations.md#quick-fix-remediation)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Zavřít výstrahy                                                                                                                                | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Zobrazit výstrahy a doporučení                                                                                                               | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Přiřaďte například roli Čtenář uživatelům, kteří potřebují jenom zobrazit informace o stavu zabezpečení prostředku, ale neprovádějí akci, jako je třeba použití doporučení nebo úprav zásad.
>
>

## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak Security Center používá službu Azure RBAC k přiřazení oprávnění uživatelům a identifikaci povolených akcí pro jednotlivé role. Teď, když jste obeznámeni s přiřazením rolí potřebnými k monitorování stavu zabezpečení předplatného, úpravám zásad zabezpečení a použití doporučení, zjistěte, jak:

- [Nastavení zásad zabezpečení ve službě Security Center](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Security Center](security-center-recommendations.md)
- [Správa a reakce na výstrahy zabezpečení v Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorování řešení zabezpečení pro partnery](./security-center-partner-integration.md)