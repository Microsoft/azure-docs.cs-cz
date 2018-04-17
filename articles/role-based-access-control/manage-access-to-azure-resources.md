---
title: Spravovat přístup k prostředkům Azure s Azure Active Directory
description: Další informace o způsobech, jak spravovat přístup k prostředků Azure pomocí různých funkcí služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: f8f8af1380dc47a4a97845d9d47ac17bd4bba3e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Spravovat přístup k prostředkům Azure s Azure Active Directory

Správu identit a přístupu pro prostředky cloudu je důležité funkce pro každou organizaci, která používá cloudu. Azure Active Directory (Azure AD) je systém identit a přístupu pro Microsoft Azure.  

Před seznamovat se podporu funkce oblastí Azure AD, najdete v následujícím videu: "Zablokujete přístup do cloudu Azure pomocí jednotného přihlašování, řízení přístupu na základě rolí a podmíněného." V něm můžete další informace o:

- Osvědčené postupy pro konfiguraci jednotného přihlašování k portálu Azure s místní služby Active Directory.
- Pomocí Azure RBAC pro řízení podrobných přístupu k prostředkům v rámci předplatných.
- Vynucení pravidel silné ověřování přes Azure AD podmíněného přístupu.
- Koncept spravované služby identitu, kde ke službám Azure můžete automaticky ověřovat prostředky Azure, a vývojáři nemusíte zpracování API klíčů nebo tajných klíčů.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Funkce oblastí
Azure AD poskytuje následující funkce pro správu přístupu k prostředkům Azure:

|||
|---|---|
| [Vztah mezi klienty Azure AD a odběry](rbac-and-directory-admin-roles.md) | Další informace o Azure AD je důvěryhodný zdroj uživatelů a skupin pro předplatné Azure. |
| [Řízení přístupu na základě role (RBAC)](overview.md) | Nabízejí vyladění správy přístupu prostřednictvím role přiřazené uživatele, skupiny nebo objekty služby. |
| [Správa privilegovaných identit s RBAC](pim-azure-resource.md) | Řízení vysoce privilegovaný přístup přiřazením privilegované role v běhu. |
| [Podmíněný přístup pro správu Azure](conditional-access-azure-management.md) | Nastavte zásady podmíněného přístupu povolit nebo blokovat přístup k koncových bodů pro správu Azure. |
| [Identita spravované služby (MSI)](../active-directory/pp/msi-overview.md) | Zadejte prostředky Azure jako virtuální počítače své vlastní identity, takže není nutné uvést přihlašovací údaje do vašeho kódu. |

 