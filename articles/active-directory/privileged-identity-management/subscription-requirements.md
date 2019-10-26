---
title: Licenční požadavky pro použití Privileged Identity Management-Azure Active Directory | Microsoft Docs
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895122"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licenční požadavky pro použití Privileged Identity Management

Aby bylo možné používat Azure Active Directory (Azure AD) Privileged Identity Management (PIM), musí mít adresář platnou licenci. Kromě toho musí být licence přiřazeny správcům a relevantním uživatelům. Tento článek popisuje licenční požadavky pro použití Privileged Identity Management.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít Privileged Identity Management, musí mít adresář jednu z následujících placených nebo zkušebních licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Další informace najdete v tématu [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Kteří uživatelé musí mít licence?

Každý správce nebo uživatel, který komunikuje s nebo obdrží výhodu od Privileged Identity Management, musí mít licenci. Patří mezi ně například:

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

Pokud vyprší platnost Azure AD Premium P2, EMS E5 nebo zkušební licence, nebudou ve vašem adresáři k dispozici Privileged Identity Management funkce:

- Trvalá přiřazení rolí k rolím Azure AD nebudou mít vliv na.
- Služba Privileged Identity Management v Azure Portal a také rutiny Graph API a rozhraní PowerShell Privileged Identity Management už nebudou k dispozici pro uživatele, aby mohli aktivovat privilegované role, spravovat privilegovaný přístup nebo provádět kontroly přístupu privilegovaných rolí.
- Budou odebrána oprávněná přiřazení rolí rolí Azure AD, protože uživatelé již nebudou moci aktivovat privilegované role.
- Všechny průběžné kontroly přístupu rolí Azure AD skončí a Privileged Identity Management nastavení konfigurace se odeberou.
- Privileged Identity Management už nebude posílat e-maily při změnách přiřazení role.

## <a name="next-steps"></a>Další kroky

- [Nasazení Privileged Identity Management](pim-deployment-plan.md)
- [Začněte používat Privileged Identity Management](pim-getting-started.md)
- [Role, které nemůžete spravovat v Privileged Identity Management](pim-roles.md)
