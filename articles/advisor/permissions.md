---
title: Oprávnění v Azure Advisor
description: Oprávnění služby Advisor a jejich způsob, jak můžou zablokovat možnost konfigurovat předplatná nebo odložit nebo zrušit doporučení.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 3c7b42372ae6de5c2f519b69aec8a154a2f35a82
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927381"
---
# <a name="permissions-in-azure-advisor"></a>Oprávnění v Azure Advisor

Azure Advisor poskytuje doporučení v závislosti na využití a konfiguraci vašich předplatných a prostředků Azure. Služba Advisor používá [předdefinované role](../role-based-access-control/built-in-roles.md) poskytované [řízením přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) ke správě přístupu k doporučením a funkcím poradce. 

## <a name="roles-and-their-access"></a>Role a jejich přístup

Následující tabulka definuje role a přístup, které mají v rámci služby Advisor:

| **Role** | **Zobrazení doporučení** | **Upravit pravidla** | **Upravit konfiguraci předplatného** | **Upravit konfiguraci skupiny prostředků**| **Přeskočit a odložit doporučení**|
|---|:---:|:---:|:---:|:---:|:---:|
|Vlastník předplatného|**Znak**|**Znak**|**Znak**|**Znak**|**Znak**|
|Přispěvatel předplatného|**Znak**|**Znak**|**Znak**|**Znak**|**Znak**|
|Čtečka předplatných|**Znak**|--|--|--|--|
|Vlastník skupiny prostředků|**Znak**|--|--|**Znak**|**Znak**|
|Přispěvatel skupiny prostředků|**Znak**|--|--|**Znak**|**Znak**|
|Čtenář skupiny prostředků|**Znak**|--|--|--|--|
|Vlastník prostředku|**Znak**|--|--|--|**Znak**|
|Přispěvatel prostředků|**Znak**|--|--|--|**Znak**|
|Čtečka prostředků|**Znak**|--|--|--|--|

> [!NOTE]
> Přístup k doporučením pro zobrazení je závislý na vašem přístupu k ovlivněnému prostředku doporučení.

## <a name="permissions-and-unavailable-actions"></a>Oprávnění a nedostupné akce

Nedostatek řádných oprávnění může blokovat schopnost provádět akce v Advisoru. Níže jsou uvedeny některé běžné problémy.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nepovedlo se nakonfigurovat předplatné nebo skupiny prostředků.

Při pokusu o konfiguraci předplatných nebo skupin prostředků v Advisoru se může zobrazit, že možnost zahrnout nebo vyloučit je zakázaná. Tento stav označuje, že nemáte dostatečná úroveň oprávnění pro danou skupinu prostředků nebo předplatné. Pokud chcete tento problém vyřešit, přečtěte si, jak [udělit uživateli přístup](../role-based-access-control/quickstart-assign-role-user-portal.md).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nepovedlo se odložit ani zavřít doporučení.

Pokud při pokusu o odložení nebo zrušení doporučení dojde k chybě, možná nemáte dostatečná oprávnění. Ujistěte se, že máte alespoň přístup Přispěvatel k ovlivněnému prostředku doporučení, které odkládáte nebo kdy chybí. Pokud chcete tento problém vyřešit, přečtěte si, jak [udělit uživateli přístup](../role-based-access-control/quickstart-assign-role-user-portal.md).

## <a name="next-steps"></a>Další kroky

Tento článek obsahuje přehled o tom, jak Advisor používá RBAC k řízení uživatelských oprávnění a k řešení běžných problémů. Další informace o službě Advisor najdete v těchto tématech:

- [Co je Azure Advisor?](./advisor-overview.md)
- [Začínáme se službou Azure Advisor](./advisor-get-started.md)
