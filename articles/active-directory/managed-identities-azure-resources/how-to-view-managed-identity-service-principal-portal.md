---
title: Zobrazení instančního objektu spravované identity na webu Azure Portal – Azure AD
description: Podrobné pokyny pro zobrazení instančního objektu spravované identity na webu Azure Portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298593"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Zobrazení instančního objektu spravované identity na webu Azure Portal

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí portálu Azure.

 > [!NOTE] 
 > Instanční objekty jsou podnikové aplikace. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolit [systém přiřazenou identitu na virtuálním počítači](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) nebo [v aplikaci](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Zobrazit instanční objekt

Tento postup ukazuje, jak zobrazit instanční objekt virtuálního zařízení s povolenou identitou přiřazenou systémem (stejné kroky platí pro aplikaci).

1. Klikněte na **Azure Active Directory** a potom na podnikové **aplikace**.
2. V části **Typ aplikace**zvolte **Všechny aplikace** a klepněte na tlačítko **Použít**.
3. Do pole vyhledávacího filtru zadejte název virtuálního zařízení nebo aplikace, která má povolenou identitu, nebo ji zvolte ze seznamu, který je prezentován.

   ![Zobrazení spravovaného objektu zabezpečení identity na portálu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Další kroky

[Spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview)

