---
title: Oprávnění v Azure Advisoru
description: Oprávnění poradce a jak mohou blokovat vaši možnost konfigurovat odběry nebo odložit nebo zrušit doporučení.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422321"
---
# <a name="permissions-in-azure-advisor"></a>Oprávnění v Azure Advisoru

Azure Advisor poskytuje doporučení na základě využití a konfigurace prostředků Azure a předplatných. Poradce používá [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) poskytované [řízení mandatorního přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) ke správě vašeho přístupu k doporučením a funkcím poradce. 

## <a name="roles-and-their-access"></a>Role a jejich přístup

V následující tabulce jsou definovány role a přístup, který mají v rámci programu Advisor:

| **Role** | **Zobrazení doporučení** | **Upravit pravidla** | **Upravit konfiguraci předplatného** | **Upravit konfiguraci skupiny prostředků**| **Zamítnutí a odložení doporučení**|
|---|:---:|:---:|:---:|:---:|:---:|
|Vlastník předplatného|**×**|**×**|**×**|**×**|**×**|
|Přispěvatel předplatného|**×**|**×**|**×**|**×**|**×**|
|Čtečka předplatného|**×**|--|--|--|--|
|Vlastník skupiny prostředků|**×**|--|--|**×**|**×**|
|Přispěvatel skupiny prostředků|**×**|--|--|**×**|**×**|
|Čtenář skupiny prostředků|**×**|--|--|--|--|
|Vlastník prostředku|**×**|--|--|--|**×**|
|Přispěvatel zdroje|**×**|--|--|--|**×**|
|Čtečka prostředků|**×**|--|--|--|--|

> [!NOTE]
> Přístup k zobrazení doporučení závisí na přístupu k prostředku ovlivněném doporučením.

## <a name="permissions-and-unavailable-actions"></a>Oprávnění a nedostupné akce

Nedostatek správných oprávnění může blokovat vaši schopnost provádět akce v programu Advisor. Níže jsou uvedeny některé běžné problémy.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nelze konfigurovat předplatná nebo skupiny prostředků.

Při pokusu o konfiguraci odběry nebo skupiny prostředků v poradce, může vidět, že možnost zahrnout nebo vyloučit je zakázána. Tento stav označuje, že nemáte dostatečnou úroveň oprávnění pro tuto skupinu prostředků nebo odběr. Chcete-li tento problém vyřešit, přečtěte si, jak [udělit přístup uživatele](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nelze odložit nebo zamítnout doporučení

Pokud se při pokusu o odložení nebo zrušení doporučení zobrazí chyba, pravděpodobně nemáte dostatečná oprávnění. Ujistěte se, že máte alespoň přispěvatel přístup k ovlivněné zdroje doporučení, které odkládáte nebo zamítáte. Chcete-li tento problém vyřešit, přečtěte si, jak [udělit přístup uživatele](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje přehled o tom, jak Poradce používá RBAC k řízení uživatelských oprávnění a jak vyřešit běžné problémy. Další informace o poradci najdete v tématu:

- [Co je Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Začínáme se službou Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
