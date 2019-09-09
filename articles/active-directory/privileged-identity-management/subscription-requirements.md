---
title: Licenční požadavky na použití PIM-Azure Active Directory | Microsoft Docs
description: Popisuje licenční požadavky pro použití Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83c31c2731a8e872dfd2750fced8b91d283d0892
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804070"
---
# <a name="license-requirements-to-use-pim"></a>Licenční požadavky na používání PIM

Aby bylo možné používat Azure Active Directory (Azure AD) Privileged Identity Management (PIM), musí mít adresář platnou licenci. Kromě toho musí být licence přiřazeny správcům a relevantním uživatelům. Tento článek popisuje licenční požadavky na používání PIM.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné použít PIM, musí mít váš adresář jednu z následujících placených nebo zkušebních licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Další informace najdete v tématu [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Kteří uživatelé musí mít licence?

Každý správce nebo uživatel, který komunikuje s nebo obdrží výhodu od PIM, musí mít licenci. Příklady obsahují:

- Správci s rolemi Azure AD spravovanými pomocí PIM
- Správci s rolemi prostředků Azure spravovanými pomocí PIM
- Správci přiřazení k roli správce privilegovaných rolí
- Uživatelé přiřazení k rolím služby Azure AD, které jsou spravovány pomocí PIM
- Uživatelé mohou schvalovat nebo odmítat požadavky v PIM
- Uživatelé přiřazení k roli prostředku Azure pomocí přiřazení za běhu nebo přímo (podle času)  
- Uživatelé přiřazení k recenzi přístupu
- Uživatelé, kteří provádějí kontroly přístupu

Informace o tom, jak přiřadit licence k vašim účelům, najdete v tématu [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Co se stane, když platnost licence vyprší?

Pokud vyprší platnost Azure AD Premium P2, EMS E5 nebo zkušební licence, funkce PIM už nebudou ve vašem adresáři k dispozici:

- Trvalá přiřazení rolí k rolím Azure AD nebudou mít vliv na.
- Služba PIM v Azure Portal a také rutiny Graph API a rozhraní PowerShell služby PIM již nebudou uživatelům k dispozici, aby mohli aktivovat privilegované role, spravovat privilegovaný přístup nebo provádět kontroly přístupu privilegovaných rolí.
- Budou odebrána oprávněná přiřazení rolí rolí Azure AD, protože uživatelé již nebudou moci aktivovat privilegované role.
- Všechny průběžné kontroly přístupu rolí Azure AD skončí a nastavení konfigurace PIM se odebere.
- PIM už nebude posílat e-maily na změny přiřazení role.

## <a name="next-steps"></a>Další postup

- [Nasazení PIM](pim-deployment-plan.md)
- [Zahájení práce s PIM](pim-getting-started.md)
- [Role, které nemůžete spravovat v PIM](pim-roles.md)
