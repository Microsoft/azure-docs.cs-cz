---
title: Licenční požadavky pro použití PIM – Azure | Dokumentace Microsoftu
description: Popisuje licenční požadavky na používání Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
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
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f6e2042f94ce653c1d569385deddbade548a58b4
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977823"
---
# <a name="license-requirements-to-use-pim"></a>Licenční požadavky pro použití PIM

Pokud chcete používat Azure Active Directory (Azure AD) Privileged Identity Management (PIM), adresář musí mít platnou licenci. Kromě toho musí přiřadit licence pro správce i uživatele relevantní. Tento článek popisuje licenční požadavky pro použití PIM.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat PIM, adresáři musí mít jeden z následujících placené nebo zkušební licence:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Další informace najdete v tématu [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Kteří uživatelé musí mít licence?

Každý správce nebo uživatel, který komunikuje se službou nebo přijímá na výhodu plynoucí z PIM musí mít licenci. Příklady obsahují:

- Správci s rolí Azure AD spravují pomocí PIM
- Správci se spravují pomocí PIM role prostředků Azure
- Správcům, přiřazena k roli správce privilegovaných rolí
- Uživatelům přiřadit jako oprávněných rolí adresáře se spravují pomocí PIM
- Uživatelé moct schvalovat a odmítat žádosti v PIM
- Uživatelé přiřazení k roli prostředků Azure s just-in-time nebo přímým přístupem (podle času) přiřazení  
- Uživatelé s kontroly přístupu
- Uživatelé, kteří provádění kontroly přístupu

Informace o tom, jak přiřadit licence pro vaše používá, najdete v části [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Co se stane, když vyprší platnost licence?

Pokud Azure AD Premium P2, EMS E5 nebo zkušební licence vyprší, funkce PIM již nebude k dispozici ve vašem adresáři:

- Přiřazení trvalých rolí pro role Azure AD, zůstanou beze změn.
- Služba PIM v na webu Azure portal, jakož i rutiny rozhraní Graph API a prostředí PowerShell rozhraní PIM, nebudou k dispozici pro uživatele k aktivovat privilegované role, Správa privilegovaného přístupu nebo provedení kontroly přístupu privilegovaných rolí.
- Role oprávněné přiřazení role Azure AD se odebere, jak uživatelé už nebudou moci uživatel aktivovat privilegované role.
- Žádné kontroly přístupu probíhající rolí Azure AD se ukončí a odebere se konfigurace nastavení PIM.
- PIM už pošle e-mailů na změny v přiřazení role.

## <a name="next-steps"></a>Další postup

- [Nasazení PIM](pim-deployment-plan.md)
- [Zahájení práce s PIM](pim-getting-started.md)
- [Role, které nelze spravovat v PIM](pim-roles.md)
