---
title: Spravovat přístup k prostředkům Azure s Privileged Identity Management (PIM)
description: Další informace o správě přístupu k Azure prostředkům pomocí Privileged Identity Management (PIM) a řízení přístupu na základě role (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 838c889f2dc099b4a4c5d84521871c64eb989163
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293746"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Spravovat přístup k prostředkům Azure s Privileged Identity Management

K ochraně před internetovými útoky privilegované účty, můžete použít Azure Active Directory Privileged Identity Management (PIM) Pokud chcete snížit čas ohrožení oprávnění a zvýšit vaši přehled o jejich používání prostřednictvím sestavy a výstrahy. PIM tomu omezením uživatelům na jejich oprávnění pouze trvá "právě v čase" (JIT), nebo přiřazením oprávnění zkrácení doby trvání, po které jsou oprávnění automaticky odvolat. 

Nyní můžete PIM pomocí řízení přístupu Azure na základě rolí (RBAC) pro správu, řízení a monitorování přístupu k prostředkům Azure. PIM můžete spravovat členství předdefinované a vlastní role můžete: 

- Povolit "právě v čase" přístup k prostředkům Azure na vyžádání
- Platnost přístupu k prostředkům automaticky pro přiřazené uživatele a skupiny
- Přiřadit dočasný přístup k prostředkům Azure pro Rychlé úlohy nebo plány na volání
- Zasílání upozornění, když nové uživatele nebo skupiny jsou přiřazeny přístup k prostředkům a aktivují oprávněné přiřazení

Další informace najdete v tématu [Přehled řízení přístupu na základě role v Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).