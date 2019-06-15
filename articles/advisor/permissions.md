---
title: Oprávnění ve službě Azure Advisor
description: Advisor oprávnění a jak může zablokovat možnost konfigurace odběrů nebo odložit nebo zavřít doporučení.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467607"
---
# <a name="permissions-in-azure-advisor"></a>Oprávnění ve službě Azure Advisor

Azure Advisor poskytuje doporučení v závislosti na využití a na konfiguraci vašich prostředků Azure a předplatných. Využívá služby Advisor [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) poskytované [řízení přístupu na základě Role](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) pro správu přístup k doporučení a funkce služby Advisor. 

## <a name="roles-and-their-access"></a>Role a jejich přístup

Následující tabulka definuje role a přístupu, které mají v rámci služby Advisor:

| **Role** | **Zobrazit doporučení** | **Upravit pravidla** | **Upravit konfigurace předplatného** | **Upravit konfigurace skupin prostředků**| **Zavřít a odložit doporučení**|
|---|:---:|:---:|:---:|:---:|:---:|
|Vlastník předplatného|**X**|**X**|**X**|**X**|**X**|
|Přispěvatel předplatného|**X**|**X**|**X**|**X**|**X**|
|Čtenář předplatného s oprávněním|**X**|--|--|--|--|
|Skupina prostředků vlastníka|**X**|--|--|**X**|**X**|
|Skupina prostředků, přispěvatele|**X**|--|--|**X**|**X**|
|Skupina prostředků čtečky|**X**|--|--|--|--|
|Vlastník prostředku|**X**|--|--|--|**X**|
|Přispěvatel prostředků|**X**|--|--|--|**X**|
|Čtečka prostředků|**X**|--|--|--|--|

> [!NOTE]
> Přístup k zobrazení doporučení je závislá na váš přístup k ovlivněný prostředek toto doporučení.

## <a name="permissions-and-unavailable-actions"></a>Oprávnění a akcí, není k dispozici

Nedostatečná správná oprávnění, můžete zablokovat možnost provádět akce v Advisoru. Toto jsou některé běžné problémy.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nejde nakonfigurovat předplatná nebo skupiny prostředků

Při pokusu o konfiguraci předplatných nebo skupinách prostředků v Advisoru, může se zobrazit, že je zakázána možnost zahrnout nebo vyloučit. Tento stav indikuje, že nemáte dostatečné oprávnění pro tuto skupinu prostředků nebo předplatného. Chcete-li tento problém vyřešit, přečtěte si postup [udělit přístup uživatelům](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nelze odložit nebo zavřít doporučení

Pokud obdržíte chybu při pokusu o posunout nebo zavřít doporučení, pravděpodobně nemáte dostatečná oprávnění. Ujistěte se, že máte alespoň přístup přispěvatele k ovlivněný prostředek doporučení jsou odložení nebo zavření. Chcete-li tento problém vyřešit, přečtěte si postup [udělit přístup uživatelům](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Další postup

Tento článek poskytla přehled používání RBAC služby Advisor na ovládací prvek uživatelských oprávnění a jak řešit běžné problémy. Další informace o službě Advisor najdete v tématu:

- [Co je Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Začínáme s využitím Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-get-started)
