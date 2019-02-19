---
title: Správa přístupu k prostředkům Azure pomocí Azure AD Privileged Identity Management (PIM)
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
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338196"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Správa přístupu k prostředkům Azure pomocí Azure AD Privileged Identity Management

Privilegované účty chránit před kybernetickými útoky, můžete použít Azure Active Directory Privileged Identity Management (PIM) zkrátit dobu expozice oprávnění a zvýší viditelnost do jejich používání prostřednictvím sestavy a upozornění. PIM toho dosahuje tím, že omezíte uživatele pouze s ohledem na jejich oprávnění "just in time" (JIT), nebo pomocí přiřazení oprávnění pro zkrácený dobu trvání, po jejímž uplynutí se oprávnění automaticky odvolána. 

Nyní můžete PIM pomocí řízení přístupu Azure na základě rolí (RBAC) spravovat, řídit a monitorovat přístup k prostředkům Azure. PIM můžete spravovat členství předdefinované a vlastní role můžete: 

- Povolit "just in time" přístup k prostředkům Azure na vyžádání
- Vypršení platnosti přístupu k prostředkům automaticky pro přiřazení uživatelé a skupiny
- Přiřadit dočasný přístup k prostředkům Azure pro rychlé úkoly nebo plány na volání
- Dostávat upozornění, když nové uživatele nebo skupiny jsou přiřazeny přístup k prostředkům, a při aktivaci oprávněnými přiřazeními

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).