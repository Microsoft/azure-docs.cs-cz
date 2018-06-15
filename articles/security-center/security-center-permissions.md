---
title: Oprávnění v Azure Security Center | Microsoft Docs
description: Tento článek vysvětluje, jak Azure Security Center používá řízení přístupu na základě rolí k přiřazení oprávnění pro uživatele a identifikuje povolené akce pro každou roli.
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: f85f49bd54eacbca67143b35eaf555cfb744a41d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31410518"
---
# <a name="permissions-in-azure-security-center"></a>Oprávnění v Azure Security Center

Azure Security Center používá [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md), který poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , lze přiřadit uživatelům, skupinám a službám v Azure.

Security Center vyhodnocuje konfigurace vaše prostředky a identifikují problémy se zabezpečením a ohrožení zabezpečení. V Centru zabezpečení zobrazí jenom informace týkající se prostředek v případě jsou přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které daný prostředek patří.

Kromě těchto rolí existují ve službě Security Center dvě specifické role:

* **Zabezpečení čtečky**: uživatel, který patří do této role má zobrazení práva k Security Center. Uživatel může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nelze provádět změny.
* **Správce zabezpečení**: uživatel, který patří do této role má stejné oprávnění jako čtečky zabezpečení a zda můžete taky aktualizovat zásady zabezpečení a zavření výstrah a doporučení.

> [!NOTE]
> Role zabezpečení, čtečky zabezpečení a správce zabezpečení, mají přístup pouze ve službě Security Center. Role zabezpečení nemají přístup do jiných oblastí služby Azure, jako je například úložiště, webové a mobilní telefon nebo Internet věcí.
>
>

## <a name="roles-and-allowed-actions"></a>Role a povolených akcí

Následující tabulka zobrazuje role a povolené akce v Centru zabezpečení. X označuje, že je povolené akce pro tuto roli.

| Role | Upravit zásady zabezpečení | Použít doporučení zabezpečení pro prostředek | Zavření výstrahy a doporučení | Zobrazit výstrahy a doporučení |
|:--- |:---:|:---:|:---:|:---:|
| Vlastník předplatného | X | X | X | X |
| Předplatné přispěvatele | X | X | X | X |
| Vlastník skupiny prostředků | -- | X | -- | X |
| Skupina prostředků přispěvatele | -- | X | -- | X |
| Čtenář | -- | -- | -- | X |
| Správce zabezpečení | X | -- | X | X |
| Čtecí zařízení pro zabezpečení | -- | -- | -- | X |

> [!NOTE]
> Doporučujeme přiřadit uživatelům tu nejvíc omezenou roli, kterou ke své práci potřebují. Například přiřadíte role Čtenář uživatelům, kteří potřebují jenom zobrazit informace o stavu zabezpečení prostředku, ale nemusí provádět žádné akce, třeba uplatňovat doporučení nebo upravovat zásady.
>
>

## <a name="next-steps"></a>Další postup
Tento článek vysvětlení, jak Security Center používá RBAC přiřadit oprávnění pro uživatele a identifikovat povolené akce pro každou roli. Teď, když jste obeznámeni s přiřazení rolí, které jsou potřeba k monitorování stavu zabezpečení vašeho předplatného, upravit zásady zabezpečení a použijete doporučení, zjistěte, jak:

- [Nastavení zásad zabezpečení v Centru zabezpečení](security-center-policies.md)
- [Správa doporučení zabezpečení v Centru zabezpečení](security-center-recommendations.md)
- [Sledování stavu zabezpečení prostředků Azure](security-center-monitoring.md)
- [Správa a reakce na výstrahy zabezpečení ve službě Security Center](security-center-managing-and-responding-alerts.md)
- [Sledování partnerských řešení zabezpečení](security-center-partner-solutions.md)
