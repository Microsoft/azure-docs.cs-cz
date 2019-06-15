---
title: Postup zobrazení instanční objekt spravovanou identitu na webu Azure Portal
description: Podrobné pokyny pro zobrazení instanční objekt spravovanou identitu na webu Azure Portal.
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
ms.openlocfilehash: 5f6139062c4d302284cc653606ae838206d3691a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290714"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Zobrazení instanční objekt spravovanou identitu na webu Azure Portal

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak chcete-li zobrazit instanční objekt spravovanou identitu pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolit [identitou přiřazenou systémem ve virtuálním počítači](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) nebo [aplikace](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Zobrazení instanční objekt služby

Tento postup ukazuje, jak zobrazit instanční objekt služby virtuálního počítače s identitou přiřazenou systémem povolené (stejný postup platí pro aplikaci).

1. Klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **podnikové aplikace**.
2. V části **typ aplikace**, zvolte **všechny aplikace**.
3. V poli filtru hledání zadejte název virtuálního počítače nebo aplikace, který se má spravovat identity povoleno nebo ho zvolit ze seznamu uvedené.

   ![Zobrazit spravovaná identita instančního objektu služby v portálu](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Další postup

[Spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview)

