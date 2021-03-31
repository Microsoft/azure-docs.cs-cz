---
title: Zobrazení instančního objektu spravované identity ve službě Azure Portal – Azure AD
description: Podrobné pokyny pro zobrazení instančního objektu spravované identity v Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003731"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Zobrazení instančního objektu spravované identity v Azure Portal

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí Azure Portal.

 > [!NOTE] 
 > Instanční objekty jsou podnikové aplikace. 

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolte [identitu přiřazenou systémem na virtuálním počítači nebo v](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikaci](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Zobrazení instančního objektu

Tento postup ukazuje, jak zobrazit instanční objekt virtuálního počítače s povolenou identitou přiřazení systému (stejný postup platí pro aplikaci).

1. Klikněte na **Azure Active Directory** a pak klikněte na **podnikové aplikace**.
2. V části **Typ aplikace** zvolte **všechny aplikace** a pak klikněte na **použít**.
3. Do pole vyhledávací filtr zadejte název prostředku Azure, který má povolenou spravovanou identitu, nebo ho vyberte v seznamu zobrazených.

   ![Zobrazit objekt spravované služby identity na portálu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Další kroky

[Spravované identity pro prostředky Azure](./overview.md)