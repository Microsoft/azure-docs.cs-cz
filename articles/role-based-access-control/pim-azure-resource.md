---
title: Správa přístupu k prostředkům Azure pomocí Azure AD a PIM
description: Přečtěte si o správě přístupu k prostředkům Azure pomocí Azure Active Directory Privileged Identity Management (PIM) a řízení přístupu na základě role (RBAC).
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
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138042"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Správa přístupu k prostředkům Azure pomocí Azure AD Privileged Identity Management

K ochraně privilegovaných účtů před škodlivými internetovými útoky můžete pomocí Azure Active Directory Privileged Identity Management (PIM) snížit dobu expozice oprávnění a zvýšit viditelnost jejich používání prostřednictvím sestav a výstrah. PIM to dělá tak, že uživatelům omezí jenom přístup k jejich oprávněním (JIT) nebo přiřazením oprávnění pro zkrácenou dobu trvání, po které se oprávnění automaticky odvolají. 

Ke správě, řízení a monitorování přístupu k prostředkům Azure teď můžete použít PIM s řízením přístupu na základě role (RBAC) Azure. PIM může spravovat členství předdefinovaných a vlastních rolí, které vám pomůžou: 

- Povolení přístupu k prostředkům Azure na vyžádání a přístup k prostředkům za běhu
- Automaticky vyprší přístup k prostředkům pro přiřazené uživatele a skupiny.
- Přiřazení dočasného přístupu k prostředkům Azure pro rychlé úlohy nebo plány volání
- Získat výstrahy, když přiřadíte přístup k prostředkům novým uživatelům nebo skupinám a když aktivujete oprávněná přiřazení

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).