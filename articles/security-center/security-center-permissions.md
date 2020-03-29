---
title: Oprávnění v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak Azure Security Center používá řízení přístupu na základě rolí přiřadit oprávnění uživatelům a identifikuje povolené akce pro každou roli.
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
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921313"
---
# <a name="permissions-in-azure-security-center"></a>Oprávnění ve službě Azure Security Center

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center vyhodnocuje konfiguraci prostředků k identifikaci problémů se zabezpečením a slabými místy. V Centru zabezpečení se informace související s prostředkem zobrazují pouze v případě, že je přiřazena role vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Kromě těchto rolí existují ve službě Security Center dvě specifické role:

* **Čtečka zabezpečení**: Uživatel, který patří do této role, má práva k zobrazení centra zabezpečení. Uživatel může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny.
* **Správce zabezpečení**: Uživatel, který patří do této role, má stejná práva jako čtečka zabezpečení a může také aktualizovat zásady zabezpečení a zrušit výstrahy a doporučení.

> [!NOTE]
> Role zabezpečení, Čtečka zabezpečení a Správce zabezpečení, mají přístup pouze v Centru zabezpečení. Role zabezpečení nemají přístup k jiným oblastem služeb Azure, jako je úložiště, web & Mobile nebo Internet věcí.
>
>

## <a name="roles-and-allowed-actions"></a>Role a povolené akce

V následující tabulce jsou zobrazeny role a povolené akce v Centru zabezpečení.

| Role | Upravit zásady zabezpečení | Použití doporučení zabezpečení pro prostředek</br> (včetně s 'Quick Fix!') | Zrušení upozornění a doporučení | Zobrazení upozornění a doporučení |
|:--- |:---:|:---:|:---:|:---:|
| Vlastník předplatného | ✔ | ✔ | ✔ | ✔ |
| Přispěvatel předplatného | -- | ✔ | ✔ | ✔ |
| Vlastník skupiny prostředků | -- | ✔ | -- | ✔ |
| Přispěvatel skupiny zdrojů | -- | ✔ | -- | ✔ |
| Čtenář | -- | -- | -- | ✔ |
| Správce zabezpečení | ✔ | -- | ✔ | ✔ |
| Čtečka zabezpečení | -- | -- | -- | ✔ |

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Roli čtenáře přiřaďte například uživatelům, kteří potřebují zobrazit pouze informace o stavu zabezpečení prostředku, ale neprovádět akce, například použít doporučení nebo upravit zásady.
>
>

## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak Security Center používá RBAC přiřadit oprávnění uživatelům a identifikovat povolené akce pro každou roli. Teď, když jste obeznámeni s přiřazeními rolí potřebnými ke sledování stavu zabezpečení předplatného, úpravám zásad zabezpečení a použití doporučení, se dozvíte, jak:

- [Nastavení zásad zabezpečení ve službě Security Center](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Centru zabezpečení](security-center-recommendations.md)
- [Sledování stavu zabezpečení prostředků Azure](security-center-monitoring.md)
- [Správa výstrah zabezpečení a reakce na ně v Centru zabezpečení](security-center-managing-and-responding-alerts.md)
- [Monitorování řešení zabezpečení partnerů](security-center-partner-solutions.md)
