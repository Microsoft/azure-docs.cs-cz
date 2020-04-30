---
title: Osvědčené postupy pro službu Azure RBAC
description: Osvědčené postupy pro použití řízení přístupu na základě role v Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726771"
---
# <a name="best-practices-for-azure-rbac"></a>Osvědčené postupy pro službu Azure RBAC

Tento článek popisuje některé osvědčené postupy pro použití řízení přístupu na základě role v Azure (RBAC). Tyto osvědčené postupy se odvozují z našeho prostředí s využitím Azure RBAC a zkušeností zákazníků, jako je sami.

## <a name="only-grant-the-access-users-need"></a>Udělte jenom uživatelům přístup, kteří potřebují.

Pomocí Azure RBAC můžete oddělit povinnosti v rámci svého týmu a udělit jenom přístup uživatelům, kteří potřebují k provádění svých úloh. Místo toho, abyste všem uživatelům udělili neomezená oprávnění v předplatném nebo prostředcích Azure, můžete povolit pouze určité akce v určitém oboru.

Osvědčeným postupem při plánování strategie řízení přístupu je udělit uživatelům nejnižší úroveň oprávnění, kterou k práci potřebují. Následující diagram ukazuje navrhovaný způsob používání RBAC.

![RBAC a nejnižší úroveň oprávnění](./media/best-practices/rbac-least-privilege.png)

Informace o tom, jak přidat přiřazení rolí, najdete v tématu [Přidání nebo odebrání přiřazení rolí](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Omezení počtu vlastníků předplatného

Měli byste mít maximálně 3 vlastníky předplatného, aby bylo možné omezit riziko narušení napadeného vlastníka. Toto doporučení se dá monitorovat v Azure Security Center. Další doporučení pro identitu a přístup v Security Center najdete v tématu [doporučení zabezpečení – referenční příručka](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Použít Azure AD Privileged Identity Management

K ochraně privilegovaných účtů před škodlivými internetovými útoky můžete pomocí Azure Active Directory Privileged Identity Management (PIM) snížit dobu expozice oprávnění a zvýšit viditelnost jejich používání prostřednictvím sestav a výstrah. PIM pomáhá chránit privilegované účty tím, že poskytuje privilegovaný přístup za běhu k prostředkům Azure AD a Azure. Přístup může být časově vázaný po tom, co se oprávnění odvolají automaticky. 

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s Azure RBAC](troubleshooting.md)