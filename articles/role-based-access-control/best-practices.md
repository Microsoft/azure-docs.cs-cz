---
title: Doporučené postupy pro Azure RBAC
description: Doporučené postupy pro používání řízení přístupu na základě rolí Azure (Azure RBAC).
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726771"
---
# <a name="best-practices-for-azure-rbac"></a>Doporučené postupy pro Azure RBAC

Tento článek popisuje některé osvědčené postupy pro používání řízení přístupu na základě rolí Azure (Azure RBAC). Tyto osvědčené postupy jsou odvozeny z našich zkušeností s Azure RBAC a zkušeností zákazníků, jako jste vy.

## <a name="only-grant-the-access-users-need"></a>Udělte pouze přístup, který uživatelé potřebují

Pomocí Azure RBAC můžete oddělit povinnosti v rámci vašeho týmu a udělit pouze množství přístupu uživatelům, které potřebují k provádění svých úloh. Místo toho, abyste všem uživatelům udělili neomezená oprávnění v předplatném nebo prostředcích Azure, můžete povolit pouze určité akce v určitém oboru.

Osvědčeným postupem při plánování strategie řízení přístupu je udělit uživatelům nejnižší úroveň oprávnění, kterou k práci potřebují. Následující diagram ukazuje navrhovaný způsob používání RBAC.

![RBAC a nejnižší úroveň oprávnění](./media/best-practices/rbac-least-privilege.png)

Informace o přidání přiřazení rolí naleznete v tématu [Přidání nebo odebrání přiřazení rolí](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Omezení počtu vlastníků předplatného

Měli byste mít maximálně 3 vlastníky předplatného, abyste snížili možnost porušení ze strany ohroženého vlastníka. Toto doporučení lze sledovat v Azure Security Center. Další doporučení týkající se identity a přístupu v Centru zabezpečení naleznete v [tématu Doporučení zabezpečení – referenční příručka](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Použití správy privilegovaných identit Azure AD

Chcete-li chránit privilegované účty před škodlivými kybernetickými útoky, můžete pomocí správy privilegovaných identit služby Azure Active Directory (PIM) snížit dobu expozice oprávnění a zvýšit viditelnost jejich použití prostřednictvím sestav a výstrah. PIM pomáhá chránit privilegované účty tím, že poskytuje privilegovaný přístup k prostředkům Azure AD a Azure. Přístup může být časově vázán, po jehož uplynutí jsou oprávnění automaticky odvolána. 

Další informace naleznete v tématu [Co je správa privilegovaných identit Azure AD?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Další kroky

- [Poradce při potížích s Azure RBAC](troubleshooting.md)