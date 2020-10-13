---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649110"
---
Spravovaná identita z Azure Active Directory (Azure AD) umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným službou Azure AD, jako je například Azure Key Vault. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani otočení jakýchkoli tajných klíčů. Další informace o spravovaných identitách v Azure AD najdete v tématu [spravované identity pro prostředky Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).

Aplikaci lze udělit dva typy identit:

- **Identita přiřazená systémem** je svázána s vaší aplikací a je odstraněna, pokud je vaše aplikace odstraněna. Aplikace může mít jenom jednu identitu přiřazenou systémem.
- **Uživatelsky přiřazená identita** je samostatný prostředek Azure, který je možné přiřadit k vaší aplikaci. Aplikace může mít více uživatelsky přiřazených identit.