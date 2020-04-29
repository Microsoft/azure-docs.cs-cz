---
title: Zobrazení instančního objektu spravované identity ve službě Azure Portal – Azure AD
description: Podrobné pokyny pro zobrazení instančního objektu spravované identity v Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79298593"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Zobrazení instančního objektu spravované identity v Azure Portal

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí Azure Portal.

 > [!NOTE] 
 > Instanční objekty jsou podnikové aplikace. 

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolte [identitu přiřazenou systémem na virtuálním počítači nebo v](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [aplikaci](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Zobrazení instančního objektu

Tento postup ukazuje, jak zobrazit instanční objekt virtuálního počítače s povolenou identitou přiřazení systému (stejný postup platí pro aplikaci).

1. Klikněte na **Azure Active Directory** a pak klikněte na **podnikové aplikace**.
2. V části **Typ aplikace**zvolte **všechny aplikace** a pak klikněte na **použít**.
3. Do pole vyhledávací filtr zadejte název virtuálního počítače nebo aplikace, která má povolenou spravovanou identitu, nebo ji vyberte v seznamu zobrazených.

   ![Zobrazit objekt spravované služby identity na portálu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Další kroky

[Spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview)

