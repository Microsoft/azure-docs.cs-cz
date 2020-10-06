---
title: Převod předplatných v rámci plánu Azure na jiného partnera (Preview)
description: Tento článek vám pomůže pochopit, co potřebujete vědět před tím, než převedete vlastnictví fakturace předplatného Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.openlocfilehash: cb9a035217734028df325555cb0954dedd29ac30
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372285"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Převod předplatných v rámci plánu Azure na jiného partnera (Preview)

Tento článek vám pomůže pochopit, co potřebujete vědět před tím, než převedete vlastnictví fakturace předplatného Azure. Pokud chcete zahájit převod předplatného Azure, které je součástí plánu Azure, na jiného partnera Microsoftu, musíte kontaktovat svého partnera. Partner vám pošle pokyny, jak začít. Po dokončení procesu převodu se vlastnictví fakturace předplatného změní.

## <a name="user-access"></a>Přístup uživatelů

Převod nebude mít vliv na přístup ke stávajícím uživatelům, skupinám nebo instančním objektům přiděleným pomocí řízení přístupu na základě role v Azure (Azure RBAC). [Azure RBAC](../../role-based-access-control/overview.md) pomáhá spravovat, kdo má přístup k prostředkům Azure, co může s těmito prostředky dělat a k jakým oblastem má přístup. Převod předplatného nezajistí novému partnerovi přístup Azure RBAC k vašim prostředkům. Předchozí partner si zachová přístup Azure RBAC.

V důsledku toho je důležité odebrat přístup Azure RBAC pro starého partnera a přidat přístup pro nového partnera. Další informace o postupu při přidělování přístupu novému partnerovi najdete v tématu [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md). Další informace o odebrání přístupu Azure RBAC pro předchozího partnera najdete v tématu [Odebrání přiřazení role](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Kromě toho nový partner automaticky nezíská přístup [AOBO (Admin on Behalf Of)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) k vašim předplatným. AOBO je potřeba k tomu, aby partner mohl spravovat předplatná Azure za vás. Další informace o oprávněních Azure najdete v tématu [Získání oprávnění ke správě služby nebo předplatného zákazníka](/partner-center/customers-revoke-admin-privileges).

## <a name="stop-a-transfer"></a>Zastavení převodu

Pokud obdržíte potvrzení, že byla odeslána žádost o převod, a **nechcete, aby tento převod pokračoval**, můžete použít jednu z následujících možností.

- Pokud aktuální partner ještě tuto žádost nepřijal, můžete požádat nového partnera, aby zrušil žádost o převod, kterou inicioval (pokud je ve stavu čekání).
- Požádejte aktuálního partnera, aby po přijetí žádosti o převod neprováděl žádnou akci. Bez souhlasu aktuálního partnera nemůže převod pokračovat. Platnost žádosti vyprší.
- Můžete _odebrat vztah prodejce_ s novým partnerem. Tato akce způsobí, že ztratíte možnost převést předplatné. Žádost se tím zruší.

Pokud hledáte pomoc nebo chcete nahlásit nevhodné chování či podezřelou aktivitu, využijte některou z možností na webu [Microsoft Legal](https://www.microsoft.com/legal/). Možnost nahlásit případný problém najdete v části Compliance & ethics (Dodržování předpisů a etická pravidla).

## <a name="next-steps"></a>Další kroky

- Pokud chcete novému partnerovi udělit přístup Azure RBAC, přečtěte si téma [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- [Získání oprávnění ke správě služby nebo předplatného zákazníka](/partner-center/customers-revoke-admin-privileges).