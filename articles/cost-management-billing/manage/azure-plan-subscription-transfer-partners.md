---
title: Převod předplatných Azure na jiného partnera (Preview)
description: Tento článek vám pomůže pochopit, co potřebujete vědět před tím, než převedete vlastnictví fakturace předplatného Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554194"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Převod předplatných Azure na jiného partnera (Preview)

Tento článek vám pomůže pochopit, co potřebujete vědět před tím, než převedete vlastnictví fakturace předplatného Azure. Pokud chcete zahájit převod předplatného Azure, které je součástí plánu Azure, na jiného partnera Microsoftu, musíte kontaktovat svého partnera. Partner vám pošle pokyny, jak začít. Po dokončení procesu převodu se vlastnictví fakturace předplatného změní.

## <a name="user-access"></a>Přístup uživatelů

Převod nebude mít vliv na přístup ke stávajícím uživatelům, skupinám nebo instančním objektům přiděleným pomocí řízení přístupu na základě role v Azure (RBAC). [Azure RBAC](../../role-based-access-control/overview.md) pomáhá spravovat, kdo má přístup k prostředkům Azure, co může s těmito prostředky dělat a k jakým oblastem má přístup. Převod předplatného nezajistí novému partnerovi přístup RBAC k vašim prostředkům. Předchozí partner si zachová přístup RBAC.

V důsledku toho je důležité odebrat přístup Azure RBAC pro starého partnera a přidat přístup pro nového partnera. Další informace o postupu při přidělování přístupu novému partnerovi najdete v tématu [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md). Další informace o odebrání přístupu RBAC pro předchozího partnera najdete v tématu [Odebrání přiřazení role](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Kromě toho nový partner automaticky nezíská přístup [AOBO (Admin on Behalf Of)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) k vašim předplatným. AOBO je potřeba k tomu, aby partner mohl spravovat předplatná Azure za vás. Další informace o postupu při přidělování přístupu AOBO novému partnerovi najdete v tématu [Převod vlastnictví fakturace předplatného Azure na jiný účet](billing-subscription-transfer.md).

## <a name="stop-a-transfer"></a>Zastavení převodu

Pokud obdržíte potvrzení, že byla odeslána žádost o převod, a **nechcete, aby tento převod pokračoval**, můžete použít jednu z následujících možností.

- Pokud aktuální partner ještě tuto žádost nepřijal, můžete požádat nového partnera, aby zrušil žádost o převod, kterou inicioval (pokud je ve stavu čekání).
- Požádejte aktuálního partnera, aby po přijetí žádosti o převod neprováděl žádnou akci. Bez souhlasu aktuálního partnera nemůže převod pokračovat. Platnost žádosti vyprší.
- Můžete _odebrat vztah prodejce_ s novým partnerem. Tato akce způsobí, že ztratíte možnost převést předplatné. Žádost se tím zruší.

Pokud hledáte pomoc nebo chcete nahlásit nevhodné chování či podezřelou aktivitu, využijte některou z možností na webu [Microsoft Legal](https://www.microsoft.com/legal/). Možnost nahlásit případný problém najdete v části Compliance & ethics (Dodržování předpisů a etická pravidla).

## <a name="next-steps"></a>Další kroky

- Pokud chcete novému partnerovi udělit přístup RBAC, přečtěte si téma [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- Pokud chcete novému partnerovi udělit přístup AOBO, přečtěte si téma [Převod vlastnictví fakturace předplatného Azure na jiný účet](billing-subscription-transfer.md).