---
title: Správa přístupu k prostředkům Azure pomocí Azure AD a PIM
description: Další informace o správě přístupu k prostředkům Azure pomocí Azure Active Directory Privileged Identity Management (PIM) a řízení přístupu na základě rolí (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 53c6375ce04425eae27f4c935e613cc27d058dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138042"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Správa přístupu k prostředkům Azure pomocí správy privilegovaných identit Azure AD

Chcete-li chránit privilegované účty před škodlivými kybernetickými útoky, můžete pomocí správy privilegovaných identit služby Azure Active Directory (PIM) snížit dobu expozice oprávnění a zvýšit viditelnost jejich použití prostřednictvím sestav a výstrah. PIM to tím, že omezuje uživatele pouze převzetí jejich oprávnění "just in time" (JIT), nebo přiřazením oprávnění pro zkrácenou dobu, po které jsou oprávnění automaticky odvolána. 

Teď můžete používat PIM s řízením přístupu na základě rolí Azure (RBAC) ke správě, řízení a monitorování přístupu k prostředkům Azure. PIM může spravovat členství v předdefinovaných a vlastních rolích, které vám pomohou: 

- Povolení přístupu k prostředkům Azure na vyžádání a "právě včas"
- Automaticky vyprší platnost přístupu k prostředkům pro přiřazené uživatele a skupiny
- Přiřazení dočasného přístupu k prostředkům Azure pro rychlé úkoly nebo plány na zavolání
- Získejte upozornění, když je novým uživatelům nebo skupinám přiřazen přístup ke zdrojům a kdy aktivují způsobilá přiřazení

Další informace naleznete v tématu [Co je správa privilegovaných identit Azure AD?](../active-directory/privileged-identity-management/pim-configure.md).