---
title: Osvědčené postupy pro službu Azure RBAC
description: Osvědčené postupy pro použití řízení přístupu na základě role v Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: d58398c42cdc6faed758e5dba3431e0841fc0b03
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555562"
---
# <a name="best-practices-for-azure-rbac"></a>Osvědčené postupy pro službu Azure RBAC

Tento článek popisuje některé osvědčené postupy pro použití řízení přístupu na základě role v Azure (RBAC). Tyto osvědčené postupy se odvozují z našeho prostředí s využitím Azure RBAC a zkušeností zákazníků, jako je sami.

## <a name="only-grant-the-access-users-need"></a>Udělte jenom uživatelům přístup, kteří potřebují.

Pomocí Azure RBAC můžete oddělit povinnosti v rámci týmu a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce. Místo toho, abyste všem uživatelům udělili neomezená oprávnění v předplatném nebo prostředcích Azure, můžete povolit pouze určité akce v určitém oboru.

Osvědčeným postupem při plánování strategie řízení přístupu je udělit uživatelům nejnižší úroveň oprávnění, kterou k práci potřebují. Nepřiřazujte širší role v širších oborech, i když se zpočátku zdá být pohodlnější. Při vytváření vlastních rolí zahrňte jenom oprávnění uživatelé. Omezením rolí a oborů omezíte, jaké prostředky budou ohroženy, pokud dojde k ohrožení zabezpečení objektu zabezpečení.

Následující diagram znázorňuje navrhovaný vzor pro používání Azure RBAC.

![Azure RBAC a nejnižší oprávnění](./media/best-practices/rbac-least-privilege.png)

Informace o tom, jak přiřadit role, najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Omezení počtu vlastníků předplatného

Měli byste mít maximálně 3 vlastníky předplatného, aby bylo možné omezit riziko narušení napadeného vlastníka. Toto doporučení se dá monitorovat v Azure Security Center. Další doporučení pro identitu a přístup v Security Center najdete v tématu [doporučení zabezpečení – referenční příručka](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Použít Azure AD Privileged Identity Management

K ochraně privilegovaných účtů před škodlivými internetovými útoky můžete pomocí Azure Active Directory Privileged Identity Management (PIM) snížit dobu expozice oprávnění a zvýšit viditelnost jejich používání prostřednictvím sestav a výstrah. PIM pomáhá chránit privilegované účty tím, že poskytuje privilegovaný přístup za běhu k prostředkům Azure AD a Azure. Přístup může být časově vázaný po tom, co se oprávnění odvolají automaticky. 

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Přiřazení rolí ke skupinám, ne uživatelům

Aby bylo přiřazení role lépe spravovatelné, nepřiřazujte role přímo uživatelům. Místo toho přiřaďte role ke skupinám. Přiřazování rolí do skupin namísto uživatelů také pomáhá minimalizovat počet přiřazení rolí, což má [omezení 2 000 přiřazení rolí na předplatné](troubleshooting.md#azure-role-assignments-limit). 

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s Azure RBAC](troubleshooting.md)
