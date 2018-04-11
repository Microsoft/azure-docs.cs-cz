---
title: "Spravovat přístup k prostředkům Azure s Azure Active Directory"
description: "Další informace o způsobech, jak spravovat přístup k prostředků Azure pomocí různých funkcí služby Azure Active Directory."
services: active-directory
documentationcenter: 
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
ms.openlocfilehash: eee4353c183aeec19f72f8e1dec6c20b6c5bb226
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
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
| [Vztah mezi klienty Azure AD a odběry](active-directory-understanding-resource-access.md) | Další informace o Azure AD je důvěryhodný zdroj uživatelů a skupin pro předplatné Azure. |
| [Řízení přístupu na základě role (RBAC)](role-based-access-control-what-is.md) | Nabízejí vyladění správy přístupu prostřednictvím role přiřazené uživatele, skupiny nebo objekty služby. |
| [Správa privilegovaných identit s RBAC](pim-azure-resource.md) | Řízení vysoce privilegovaný přístup přiřazením privilegované role v běhu. |
| [Podmíněný přístup pro správu Azure](conditional-access-azure-management.md) | Nastavte zásady podmíněného přístupu povolit nebo blokovat přístup k koncových bodů pro správu Azure. |
| [Identita spravované služby (MSI)](msi-overview.md) | Zadejte prostředky Azure jako virtuální počítače své vlastní identity, takže není nutné uvést přihlašovací údaje do vašeho kódu. |

 