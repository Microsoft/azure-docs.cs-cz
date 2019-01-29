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
ms.openlocfilehash: ad64fba0288aa5663e008484d6f0d2cbdff0bca6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187279"
---
# <a name="license-requirements-to-use-pim"></a>Licenční požadavky pro použití PIM

Pokud chcete používat Azure Active Directory (Azure AD) Privileged Identity Management (PIM), adresář musí mít platnou licenci. Kromě toho musí přiřadit licence pro správce i uživatele relevantní. Tento článek popisuje licenční požadavky pro použití PIM.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat PIM, adresáři musí mít jeden z následujících placené nebo zkušební licence:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

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

- [Zahájení práce s PIM](pim-getting-started.md)
- [Role, které nelze spravovat v PIM](pim-roles.md)
